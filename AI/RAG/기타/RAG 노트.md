

1. 검색 속도 (파인콘, 파이스 > 엘라스틱 서치 > 크로마 등등)
2. 검색 엔진 (하이브리드 검색 > 벡터 검색 > 시맨틱 검색)
3. Reranking
4. Reranking이 필요한 이유
	RAG는 검색 시간 단축을 위해 Approximate Nearnest Neighbor search (ANNs) 기술을 활용하기 때문에
	질문과 관계없는 문서가 검색될 가능성이 높음

5. 사용자 피드백 받기

6. 이전 대화 내용 기억
	from langchain.memory import ConversationBufferMemory
	memory = ConversationBufferMemory(memory_key='chat_history', return_messages=True)
7. chunk_size는 보통 300~ 500으로


8. 단어기반 vs 벡터기반 
	BM25 검색 결과: [Document(metadata={'source': 1}, page_content='우리나라 2024 년 GDP 전망은 3.0%이다.')]
	-----------
	FAISS 검색 결과: [Document(metadata={'source': 1}, page_content='우리나라는 2022년 국내총생산 중 연구개발 예산은 약 5%이다.')]