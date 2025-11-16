
# 📘 요약 기반 기억 챗봇 (Summary-Augmented Memory Chatbot)

이 프로젝트는 **LangChain**, **OpenAI(ChatOpenAI)**, **Streamlit**을 이용해  
사용자의 대화 내용을 **장기 요약 메모리(Long-term Summary Memory)**로 관리하는 챗봇 데모입니다.

사용자의 대화는 매 대화마다 LLM이 자동으로 요약하여 개별 사용자 ID 기준으로 파일로 저장되며,  
챗봇은 **과거 요약된 정보 + 최근 대화 기록**을 함께 사용하여 일관된 답변을 제공합니다.

---

## ✨ 주요 기능

### ✅ 1. 사용자별 장기 기억 저장  
- `summaries/{user_id}.txt` 파일에 과거 대화 내용의 **요약본** 저장  
- ID를 변경하면 각각 다른 기억을 가진 챗봇처럼 동작

### ✅ 2. ConversationBufferMemory 기반 단기 메모리  
- LangChain의 `ConversationBufferMemory` 로 최근 대화 유지  
- `history` 값으로 LCEL 체인에 전달되어 답변 생성에 반영

### ✅ 3. 요약 기반 장기 기억 자동 업데이트  
- 최근 대화(약 6개 메시지)를 기반으로  
  이전 요약 + 새로운 대화를 결합하여 LLM이 자동으로 통합 요약 생성  
- 매 대화마다 요약 파일 자동 갱신

### ✅ 4. Streamlit UI 제공  
- 사용자 ID 입력  
- 질문 입력  
- "답변 생성" 버튼 클릭  
- 생성된 요약 표시  
- 최근 대화 기록 출력  

---

## 📁 폴더 구조

project/
│── summaries/ # 사용자별 요약 저장 디렉토리 (자동 생성)
│── app.py # Streamlit 실행 파일
│── .env # OpenAI API Key 저장
│── README.md

yaml
코드 복사

---

## 🚀 실행 방법

### 1. 저장소 클론 또는 코드 다운로드
```bash
git clone <your_repo_url>
cd project
2. 패키지 설치
bash
코드 복사
pip install -r requirements.txt
필요 패키지 예시:

nginx
코드 복사
streamlit
python-dotenv
langchain
langchain-community
langchain-core
openai
3. .env 파일 생성 (루트 폴더)
ini
코드 복사
OPENAI_API_KEY=your_api_key_here
4. 실행
bash
코드 복사
streamlit run app.py
🧠 시스템 구조 설명
▶ 1. 단기 기억 — ConversationBufferMemory
python
코드 복사
memory = ConversationBufferMemory(
    memory_key="history",
    return_messages=True
)
사용자의 최근 대화를 저장하며
챗봇 응답 생성 시 history로 포함됨.

▶ 2. 장기 기억 로드
python
코드 복사
summary_path = f"summaries/{thread_id}.txt"
사용자 ID별로 요약 파일을 읽어 longterm_summary 로 설정.

▶ 3. LCEL 기반 프롬프트 체인
python
코드 복사
{
    "input": RunnablePassthrough(),
    "history": RunnableLambda(...),
    "longterm_summary": RunnableLambda(...)
} 
| main_prompt 
| llm
사용자 입력 → 프롬프트 생성 → 모델 호출로 구성된 LCEL 체인.

▶ 4. 요약 자동 갱신
최근 메시지 6개를 기반으로 새로운 통합 요약 생성:

python
코드 복사
summarizer_prompt = PromptTemplate.from_template("""
[이전 요약]
{old_summary}

[새로운 대화]
{recent_chat}

새로운 통합 요약:
""")
LLM이 생성한 요약은 사용자별 .txt 파일로 저장됨.

🖼 화면 구성
Streamlit UI로 제공되는 항목:

사용자 ID 입력창

질문 입력창

"답변 생성" 버튼

챗봇 답변

자동 갱신된 장기 요약 표시

최근 대화 기록 리스트

🛠 사용 기술
기술	역할
Streamlit	웹 UI
LangChain LCEL	체인 기반 프롬프트 처리
ConversationBufferMemory	단기 메모리
OpenAI ChatOpenAI	LLM 응답 생성
dotenv	API 키 관리
파일 기반 저장소	사용자별 장기 기억 유지

