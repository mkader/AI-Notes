An AI agent is a software program that can interact with its environment, gather data, and use that data to achieve predetermined goals. AI agents can choose the best actions to perform to meet those goals.
  * Key characteristics of AI agents are as follows:
  * An agent can perform autonomous actions without constant human intervention. Also, they can have a human in the loop to maintain control.
  * Agents have a memory to store individual preferences and allow for personalization. It can also store knowledge. An LLM can undertake information processing and decision-making functions.
  * Agents must be able to perceive and process the information available from their environment.

Model Context Protocol (MCP) is a new system introduced by Anthropic to make AI models more powerful.
  * It is an open standard that allows AI models (like Claude) to connect to databases, APIs, file systems, and other tools without needing custom code for each new integration.
  * MCP follows a client-server model with 3 key components:
  * Host: AI applications like Claude
  * MCP Client: Component inside an AI model (like Claude) that allows it to communicate with MCP servers
  * MCP Server: Middleman that connects an AI model to an external system

<img src="https://github.com/mkader/AI-Notes/blob/main/mcp/AI%20Agent%20versus%20MCP.jpg">
