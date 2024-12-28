
---

광고성 수신 동의 추가
닉네임
아이디
프로필 사진 url
~~관심 카테고리~~

DTO
```
package com.flip.flipapp.domain.account.controller.dto.request;  
  
import jakarta.validation.Valid;  
import jakarta.validation.constraints.NotBlank;  
import jakarta.validation.constraints.NotNull;  
import jakarta.validation.constraints.Pattern;  
import jakarta.validation.constraints.Size;  
import java.util.List;  
  
public record RegisterRequest(  
@NotBlank @Pattern(regexp = "google|kakao|naver|apple", message = "잘못된 소셜 로그인 공급자입니다.")  
String provider,  
@NotNull @Size(min = 3, max = 12)  
List<Long> categories,  
@Valid  
UserProfile profile  
) {  
  
public record UserProfile(  
@NotBlank String userId,  
@NotBlank String nickname,  
String photoUrl  
) {  
  
}  
}
```
controller
```
package com.flip.flipapp.domain.account.controller;  
  
import com.flip.flipapp.domain.account.controller.dto.request.RegisterRequest;  
import com.flip.flipapp.domain.account.controller.dto.response.JwtResponse;  
import com.flip.flipapp.domain.account.service.oauth.service.OauthVerificationService;  
import com.flip.flipapp.domain.account.service.RegisterService;  
import com.flip.flipapp.domain.profile.model.Profile;  
import com.flip.flipapp.domain.token.service.TokenService;  
import jakarta.validation.Valid;  
import lombok.RequiredArgsConstructor;  
import org.springframework.http.HttpHeaders;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.ResponseEntity;  
import org.springframework.web.bind.annotation.PostMapping;  
import org.springframework.web.bind.annotation.RequestBody;  
import org.springframework.web.bind.annotation.RequestHeader;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
@RequiredArgsConstructor  
public class RegisterController {  
  
private final OauthVerificationService oauthVerificationService;  
private final RegisterService registerService;  
private final TokenService tokenService;  
  
@PostMapping("/api/v1/accounts")  
public ResponseEntity<JwtResponse> register(  
@RequestHeader(HttpHeaders.AUTHORIZATION) String socialAccessToken,  
@RequestBody @Valid RegisterRequest registerRequest) {  
  
String oauthId = oauthVerificationService.verifyAndExtractSub(registerRequest.provider(),  
socialAccessToken);  
  
Profile profile = registerService.register(registerRequest, oauthId);  
  
JwtResponse jwtResponse = tokenService.createAndSaveTokens(profile);  
  
return ResponseEntity.status(HttpStatus.OK).body(jwtResponse);  
}  
}
```

exception
```
package com.flip.flipapp.domain.account.exception;  
  
import com.flip.flipapp.global.error.ErrorCode;  
import lombok.RequiredArgsConstructor;  
  
@RequiredArgsConstructor  
public enum AccountErrorCode implements ErrorCode {  
  
ACCOUNT_SUSPENDED("A001", "계정이 정지되었습니다.", 403),  
ACCOUNT_NOT_FOUND("A002", "계정을 찾을 수 없습니다.", 404),  
UNSUPPORTED_PROVIDER("A003", "지원하지 않는 소셜 로그인 제공자입니다.", 400);  
  
  
private final String code;  
private final String message;  
private final int status;  
  
@Override  
public String getCode() {  
return this.code;  
}  
  
@Override  
public String getMessage() {  
return this.message;  
}  
  
@Override  
public int getStatus() {  
return this.status;  
}  
  
}


package com.flip.flipapp.domain.account.exception;  
  
import com.flip.flipapp.global.error.exception.BusinessException;  
  
public class UnsupportedProviderException extends BusinessException {  
  
public UnsupportedProviderException() {  
super(AccountErrorCode.UNSUPPORTED_PROVIDER);  
}  
}
```

service
```
package com.flip.flipapp.domain.account.service;  
  
import com.flip.flipapp.domain.account.controller.dto.request.RegisterRequest;  
import com.flip.flipapp.domain.account.model.Account;  
import com.flip.flipapp.domain.account.repository.AccountRepository;  
import com.flip.flipapp.domain.category.model.Category;  
import com.flip.flipapp.domain.interest_category.exception.InterestCategoryRelationException;  
import com.flip.flipapp.domain.interest_category.model.InterestCategory;  
import com.flip.flipapp.domain.interest_category.repository.InterestCategoryRepository;  
import com.flip.flipapp.domain.profile.model.Profile;  
import com.flip.flipapp.domain.profile.repository.ProfileRepository;  
import com.flip.flipapp.domain.profileImage.model.ProfileImage;  
import com.flip.flipapp.domain.profileImage.repository.ProfileImageRepository;  
import jakarta.validation.Valid;  
import java.util.List;  
import lombok.RequiredArgsConstructor;  
import org.springframework.dao.DataIntegrityViolationException;  
import org.springframework.stereotype.Service;  
import org.springframework.transaction.annotation.Transactional;  
import org.springframework.util.StringUtils;  
  
@Service  
@RequiredArgsConstructor  
public class RegisterService {  
  
private final AccountRepository accountRepository;  
private final ProfileRepository profileRepository;  
private final ProfileImageRepository profileImageRepository;  
private final InterestCategoryRepository interestCategoryRepository;  
  
@Transactional  
public Profile register(@Valid RegisterRequest registerRequest, String oauthId) {  
Account account = Account.builder()  
.oauthId(registerRequest.provider() + oauthId)  
.recentLogin(null)  
.build();  
accountRepository.save(account);  
  
Profile profile = Profile.builder()  
.userId(registerRequest.profile().userId())  
.nickname(registerRequest.profile().nickname())  
.introduce("")  
.followerCnt(0L)  
.followingCnt(0L)  
.postCnt(0L)  
.account(account)  
.build();  
profileRepository.save(profile);  
  
account.setRecentLogin(profile.getProfileId());  
  
if (StringUtils.hasText(registerRequest.profile().photoUrl())) {  
ProfileImage profileImage = ProfileImage.builder()  
.imageUrl(registerRequest.profile().photoUrl())  
.profile(profile)  
.build();  
profileImageRepository.save(profileImage);  
  
}  
  
List<InterestCategory> categoriesToAdd = registerRequest.categories().stream()  
.map(categoryId ->  
InterestCategory.builder()  
.profile(profile)  
.category(Category.builder().categoryId(categoryId).build())  
.build()  
)  
.toList();  
try {  
interestCategoryRepository.saveAll(categoriesToAdd);  
} catch (DataIntegrityViolationException e) {  
throw new InterestCategoryRelationException();  
}  
  
return profile;  
}  
}
```

oauth service
```
package com.flip.flipapp.domain.account.service.oauth.service;  
  
import org.springframework.http.HttpEntity;  
import org.springframework.http.HttpMethod;  
import org.springframework.http.ResponseEntity;  
import org.springframework.stereotype.Service;  
import org.springframework.web.client.RestTemplate;  
  
@Service  
public class GoogleAuthService {  
  
private static final String GOOGLE_API_URL = "https://oauth2.googleapis.com/tokeninfo?id_token=";  
  
public String validateAccessTokenAndGetSub(String idToken) {  
RestTemplate restTemplate = new RestTemplate();  
ResponseEntity<GoogleUserResponse> response = restTemplate.exchange(  
GOOGLE_API_URL + idToken, HttpMethod.GET, HttpEntity.EMPTY, GoogleUserResponse.class);  
  
return response.getBody().getSub(); // Google의 user_id (sub)}  
  
static class GoogleUserResponse {  
private String sub;  
  
public String getSub() {  
return sub;  
}  
  
public void setSub(String sub) {  
this.sub = sub;  
}  
}  
}




package com.flip.flipapp.domain.account.service.oauth.service;  
  
import org.springframework.http.HttpEntity;  
import org.springframework.http.HttpHeaders;  
import org.springframework.http.HttpMethod;  
import org.springframework.http.ResponseEntity;  
import org.springframework.stereotype.Service;  
import org.springframework.web.client.RestTemplate;  
  
@Service  
public class KakaoAuthService {  
  
private static final String KAKAO_API_URL = "https://kapi.kakao.com/v2/user/me";  
  
public String validateAccessTokenAndGetSub(String accessToken) {  
RestTemplate restTemplate = new RestTemplate();  
  
HttpHeaders headers = new HttpHeaders();  
headers.set("Authorization", "Bearer " + accessToken);  
  
HttpEntity<String> entity = new HttpEntity<>(headers);  
ResponseEntity<KakaoUserResponse> response = restTemplate.exchange(  
KAKAO_API_URL, HttpMethod.GET, entity, KakaoUserResponse.class);  
  
return response.getBody().getId(); // KaKao의 user_id (sub)}  
  
static class KakaoUserResponse {  
private String id;  
  
public String getId() {  
return id;  
}  
  
public void setId(String id) {  
this.id = id;  
}  
}  
}

package com.flip.flipapp.domain.account.service.oauth.service;  
  
import org.springframework.http.HttpEntity;  
import org.springframework.http.HttpHeaders;  
import org.springframework.http.HttpMethod;  
import org.springframework.http.ResponseEntity;  
import org.springframework.stereotype.Service;  
import org.springframework.web.client.RestTemplate;  
  
@Service  
public class NaverAuthService {  
  
private static final String NAVER_API_URL = "https://openapi.naver.com/v1/nid/me";  
  
public String validateAccessTokenAndGetSub(String accessToken) {  
RestTemplate restTemplate = new RestTemplate();  
  
HttpHeaders headers = new HttpHeaders();  
headers.set("Authorization", "Bearer " + accessToken);  
  
HttpEntity<String> entity = new HttpEntity<>(headers);  
ResponseEntity<NaverUserResponse> response = restTemplate.exchange(  
NAVER_API_URL, HttpMethod.GET, entity, NaverUserResponse.class);  
  
return response.getBody().getResponse().getId(); // Naver의 user_id (sub)}  
  
static class NaverUserResponse {  
private Response response;  
  
public Response getResponse() {  
return response;  
}  
  
public void setResponse(Response response) {  
this.response = response;  
}  
  
static class Response {  
private String id;  
  
public String getId() {  
return id;  
}  
  
public void setId(String id) {  
this.id = id;  
}  
}  
}  
}

package com.flip.flipapp.domain.account.service.oauth.service;  
  
import com.flip.flipapp.domain.account.exception.UnsupportedProviderException;  
import lombok.RequiredArgsConstructor;  
import org.springframework.stereotype.Service;  
  
/*  
* 박준용  
* 우선 aaple 로그인  
*  
*/  
@Service  
@RequiredArgsConstructor  
public class OauthVerificationService {  
  
private final KakaoAuthService kakaoAuthService;  
private final GoogleAuthService googleAuthService;  
private final NaverAuthService naverAuthService;  
  
  
public String verifyAndExtractSub(String provider, String accessToken) {  
switch (provider.toLowerCase()) {  
case "kakao":  
return kakaoAuthService.validateAccessTokenAndGetSub(accessToken);  
case "google":  
return googleAuthService.validateAccessTokenAndGetSub(accessToken);  
case "naver":  
return naverAuthService.validateAccessTokenAndGetSub(accessToken);  
default:  
throw new UnsupportedProviderException();  
}  
}  
  
}


```