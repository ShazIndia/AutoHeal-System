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

<img width="468" height="22" alt="image" src="https://github.com/user-attachments/assets/0cfc0d04-1c89-4775-913c-702d8a095001" />

🔁 CI/CD Healing Lifecycle Illustration
sequenceDiagram

<img width="468" height="224" alt="image" src="https://github.com/user-attachments/assets/7e5322ff-30c7-4c85-9834-5f01aa74dc98" />

📦 Tech Stack
  •	LangGraph
  •	OpenAI GPT-4
  •	GitHub REST API
