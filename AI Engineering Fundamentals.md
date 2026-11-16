# AI Engineering Fundamentals

<img width="348" height="286" alt="image" src="https://github.com/user-attachments/assets/252ba1e3-d152-403e-9623-3780b9e26f52" />

## What you'll learn

### Build real AI systems
* Build a RAG-based customer support chatbot and an LLM playground
* Ship an "Ask-the-Web" agent similar to Perplexity
* Build and deploy a deep research agent with web search and reasoning
* Run models locally on your own machine

### Understand how LLMs actually work
* Follow the full path from raw data to working chatbot
* Understand pre-training, SFT, and RLHF in plain English
* Compare models like GPT, DeepSeek, Qwen, and Gemma

### Master context engineering
* Apply few-shot and chain-of-thought prompting
* Write role-specific and user-context prompts
* Understand how fine-tuning with adapters and LoRA works
* Learn about context assembly and compaction

### Build RAG pipelines that don't hallucinate
* Parse, chunk, and index documents for retrieval
* Build a semantic-based retrieval search with FAISS
* Evaluate context relevance, faithfulness, and answer correctness

### Design agents with MCP and your own tools
* Connect agents to the web with tool calling
* Apply patterns like routing, reflection, and ReAct
* Evaluate agents and multi-agent systems

### Understand thinking and reasoning models
* Understand reasoning models like OpenAI's o family and DeepSeek-R1
* Implement inference-time scaling techniques like parallel sampling and sequential revision
* Learn training-time techniques to create reasoning models

## Course Outline
### Build an LLM Playground
#### LLM Overview and Foundations
##### Pre-Training
* Data collection (manual crawling, Common Crawl)
* Data cleaning (RefinedWeb, Dolma, FineWeb)
* Tokenization (e.g., BPE)
* Architecture (neural networks, Transformers, GPT family, DeepSeek, Qwen, Gemma)
##### Text Generation (Decoding)
* Greedy and beam search
* Top-k and top-p sampling
##### Post-Training
* SFT
* RL and RLHF (verifiable tasks, reward models, PPO, etc.)
##### Evaluation
* Traditional metrics
* Task-specific benchmarks
* Human evaluation and leaderboards
* Chatbots' Overall Design
##### Build an LLM Playground diagram

<img width="1254" height="354" alt="image" src="https://github.com/user-attachments/assets/b6b2f701-b24d-4f4a-8348-763b223d6f90" />
<img width="1254" height="369" alt="image" src="https://github.com/user-attachments/assets/5c237d62-3ab5-44fe-8768-e99914399722" />
<img width="1229" height="370" alt="image" src="https://github.com/user-attachments/assets/8d358f46-34a1-4972-b0f6-68f06dd00168" />
<img width="1277" height="378" alt="image" src="https://github.com/user-attachments/assets/6aef3190-a196-4060-8ddb-92c7477d0be5" />

### Build a Customer Support Chatbot
#### Overview of Adaptation Techniques
##### Fine-Tuning
* Parameter-efficient fine-tuning (PEFT)
* Adapters and LoRA
##### Prompt Engineering
* Few-shot and zero-shot prompting
* Chain-of-thought prompting
* Role-specific and user-context prompting
#### RAG Overview
##### Retrieval
* Document parsing (rule-based, AI-based) and chunking strategies
* Indexing (keyword, full-text, knowledge-based, vector-based, embedding models)
* Search methods (exact and approximate nearest neighbor)
##### Generation
* Prompt engineering for RAG
* RAFT: Training Technique for RAG
* RAG Evaluation (context relevance, faithfulness, answer correctness)
* RAG Overall Design
* Build a Customer Support Chatbot diagram

<img width="420" height="470" alt="image" src="https://github.com/user-attachments/assets/f2793d65-baa4-414a-b8f0-653e98e0cea1" />

### Build an Ask-the-Web Agent
#### Agents Overview
* Agents vs. agentic systems vs. LLMs
* Agency levels (e.g., workflows, multi-step agents)
#### Workflows
* Prompt chaining
* Routing
* Parallelization (sectioning, voting)
* Reflection
* Orchestrator-worker
#### Tools
* Tool calling
* Tool formatting
* Tool execution
* MCP
#### Multi-Step Agents
* Planning autonomy
* ReAct
* Reflexion, ReWOO, etc.
* Tree search for agents
### Multi-Agent Systems (challenges, use-cases, A2A protocol)
### Agent Evaluation
<img width="502" height="566" alt="image" src="https://github.com/user-attachments/assets/89228164-8792-4774-8611-a007bb2a89a9" />

### Build "Deep Research" Capability with Web Search and Reasoning Models
#### Reasoning and Thinking LLMs
* Overview of reasoning models like DeepSeek-R1 and GPT-5 thinking
#### Inference-time Techniques
* Inference-time scaling
* CoT prompting
* Parallel sampling
* Sequential sampling
* Tree of Thoughts (ToT)
* Search against a verifier
#### Training-time Techniques
* SFT on reasoning data (e.g., STaR)
* Reinforcement learning with a verifier
* Reward modeling (ORM, PRM)
* Self-refinement
* Internalizing search (e.g., Meta-CoT)
#### Local Deployment
<img width="537" height="589" alt="image" src="https://github.com/user-attachments/assets/b7c5622f-8d6c-4b04-aa62-3a6a56406b5d" />
