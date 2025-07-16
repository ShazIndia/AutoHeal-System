🧠 Self-Healing DevOps Agent (AutoFix CI/CD)
A LangGraph-based AI agent system that autonomously detects, understands, and fixes CI/CD failures in GitHub Actions using GPT-4.

🎓 What It Does
•	Detects failed GitHub Actions workflows
•	Uses LLM to suggest contextual fixes
•	Optionally posts PR suggestions or comments
•	Stores prior issues in a vector DB for reuse

📊 Architecture
•	fetch_ci_failure: pulls job & step failures from GitHub
•	suggest_fix: formats logs + workflows and prompts GPT-4
•	validate_fix: checks for YAML syntax, dry-run results
•	memory_search: retrieves similar errors and past solutions

🧩 LangGraph Architecture Diagram
<img width="302" height="648" alt="image" src="https://github.com/user-attachments/assets/b04f3b4c-33e3-4390-99c7-fce7d7b13256" />

🧠 Agent Flow Diagram
flowchart LR
    Start([Start]) --> FetchCI[Fetch CI Failure]
    FetchCI --> SuggestFix[LLM Suggestion Agent]
    SuggestFix --> Validate[Validator Agent]
    Validate --> Memory[Memory Lookup Agent]
    Memory --> Output([Apply Fix / Comment])
    Output --> End([End])
🔁 CI/CD Healing Lifecycle Illustration
sequenceDiagram
    participant DevOps as DevOps System
    participant Agent as AutoHeal Agent
    participant LLM as LLM
    participant GitHub as GitHub Actions

    GitHub->>Agent: Notify Failure
    Agent->>GitHub: Fetch Logs + YAML
    Agent->>LLM: Generate Fix Suggestion
    LLM-->>Agent: Suggested Fix
    Agent->>DevOps: Validate Fix (dry run)
    Agent->>GitHub: Post PR or Comment Fix
    Agent->>Memory: Store error & solution
📦 Tech Stack
•	LangGraph
•	OpenAI GPT-4
•	GitHub REST API
•	Chroma / Weaviate (for memory)
<img width="468" height="637" alt="image" src="https://github.com/user-attachments/assets/5470828c-dc15-4f22-ae14-8dbabbdd9ed3" />
