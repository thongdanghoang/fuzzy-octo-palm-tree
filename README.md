---

## Brainstorm Output — Prompting Techniques Learning Project

### Mục tiêu
Học 7 prompting techniques thông qua **observe-driven test suite** trên Quarkus + LangChain4j, dùng Judge model để evaluate có cơ sở thay vì đọc output bằng mắt.

---

### Stack

| Layer         | Choice                       | Lý do                                            |
|---------------|------------------------------|--------------------------------------------------|
| Framework     | Quarkus + LangChain4j        | Background sẵn, annotation-driven clean          |
| Student model | Ollama local — `qwen2.5:14b` | M2 32GB chạy tốt, đủ mạnh để tạo meaningful diff |
| Judge model   | Claude Haiku 3.5 (API)       | Đủ thông minh để evaluate, cực rẻ                |
| Test          | `@QuarkusTest` + JUnit 5     | Test-first, không cần UI                         |

---

### Task Bank

3 loại task cố định, **mỗi technique chạy qua cả 3** để compare dọc:

| #  | Loại                                     | Mục tiêu observe                               |
|----|------------------------------------------|------------------------------------------------|
| T1 | **Logic trap** (bat & ball style)        | CoT vs Zero-shot diff rõ nhất ở đây            |
| T2 | **Ambiguous classification + edge case** | Few-shot, Step-Back, Self-Consistency tỏa sáng |
| T3 | **Multi-step real-world**                | ReAct showcase, ToT có thể so sánh ở đây       |

---

### Technique Roadmap

```
Phase 1 — Core (implement trước)
├── Zero-Shot          ← baseline
├── Few-Shot           ← thêm examples vào prompt
├── Step-Back          ← hỏi abstract question trước
└── Chain of Thought   ← "think step by step"

Phase 2 — Advanced
├── Self-Consistency   ← separate Maven profile, run N lần + aggregate
└── ReAct              ← real tools (xem bên dưới)

Phase 3 — Cuối cùng
└── Tree of Thoughts   ← multi-call orchestration, effort cao nhất
```

---

### ReAct Tools (real, không mock)

Học tích hợp tool thực qua `@Tool` annotation của LangChain4j:

| Tool                     | Học được gì                                              |
|--------------------------|----------------------------------------------------------|
| **Web search**           | External API integration cơ bản                          |
| **Calculator / math**    | Tool với pure logic, không side effect                   |
| **File system read**     | I/O tool, context injection                              |
| **REST API external**    | HTTP client tool, error handling                         |
| **Vector DB repository** | Inject Spring/Quarkus repo vào tool, RAG pattern preview |

---

### Judge Pattern

```
Student (Ollama) → response
                        ↓
Judge (Haiku) ← task + technique_name + response
                        ↓
              JudgeVerdict { score(1-5), correct, reasoning, weakness }
```

- Assert trên **structure** (score, correct) — không assert content
- Log `reasoning` và `weakness` để **đọc và học**
- Self-Consistency dùng thêm **aggregate verdict** từ N runs

---

### Test Structure

```
src/test/java/prompting/
├── base/
│   ├── PromptingBaseTest.java      ← task bank, logging, assert utils
│   └── JudgeService.java
├── phase1/
│   ├── ZeroShotTest.java
│   ├── FewShotTest.java
│   ├── StepBackTest.java
│   └── ChainOfThoughtTest.java
├── phase2/
│   ├── SelfConsistencyTest.java    ← @Tag("consistency") profile riêng
│   └── ReActTest.java
└── phase3/
    └── TreeOfThoughtsTest.java
```

---

### Next Step

Scaffold project — `pom.xml`, extensions, `application.properties`, base test class, và `ZeroShotTest` đầu tiên làm
baseline. Bắt đầu chưa?
