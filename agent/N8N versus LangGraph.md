N8N is an open-source automation tool that lets you visually build workflows by connecting different services, APIs, and AI tools in a sequence. Here’s how it works:
  * Starts with Input from the user.
  * Passes it to an AI Agent for processing.
  * The AI Agent can either make a Tool Call or access Memory.
  * A Decision node chooses the next action and produces the final LLM output for the user.

LangGraph is a Python framework for building AI Agent workflows using a flexible graph structure that supports branching, looping, and multi-agent collaboration. Here’s how it works:
  * Starts with a shared State containing workflow context.
  * Can route tasks to different agents.
  * Agents interact with a Tool Node to perform tasks.
  * A Conditional node decides whether to retry or mark the process done.


<img src="https://github.com/mkader/AI-Notes/blob/main/agent/N8N%20versus%20LangGraph.jpg"
