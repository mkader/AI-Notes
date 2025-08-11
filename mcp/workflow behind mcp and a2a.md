MCP and A2A are your biggest advantages while building AI Agents

If you don't know how they work, here's an easy explanation....

AI Agent development now takes days/weeks instead of weeks/months, thanks to new frameworks and protocols like MCP, A2A, and Agent SDK.

Among the new tools,

Today, we’ll see how MCP and A2A improve agent-to-agent and component interactions.

📌 Let us start with MCP:

1. Query: This can be a prompt given to an MCP client asking to build an AI Agent that can do a specific task.

2. MCP Client: The MCP client intercepts the query and shares it with the Large Language Model.

3. Query: The initial query is sent to the LLM by the client.

4. LLMs: MCP Client uses an LLM, and that particular LLM is responsible for generating answers based on the query and also for choosing the right tool.

5. Chooses the right server: After understanding the context of the query, the LLM sends a response to the Client to choose an appropriate MCP server for the task.

6. Server Approval Request: After the LLM sends a server selection request, the client optionally shares an approval request with the user for Human-in-Loop security.

7. MCP Server processing: The chosen server is then used to complete the given task by the user, utilising the user's query and the tools' data.

8. Result: Finally, after the processing is done, the result is then shared with the user.

📌 A2A Protocol:

1. Query: The user sends a query to AI Agent 1 (Client), requesting a specific task or information.

2. Agent Card: A public JSON file with an agent's capabilities, skills, endpoint URL, and authentication needs acts as a discovery card for clients.

- Through this Agent Card client discovers the capabilities of other agents, which helps them choose the best one for their current need.

3. Task: Task is the central unit of work. A client initiates a task by sending a message, and each Tasks have a unique ID and progresses through states.

5. Processing: The server either streams SSE events (status updates, artifacts) as the task progresses or processes the task synchronously, returning the final Task object in the response.

- Interaction (Optional): If a task requires input, the client sends further messages using the same Task ID via tasks/send or tasks/send Subscribe.

5. Completion: The task eventually reaches a terminal state.

6. Result: After the task is completed, the result is sent back to the user.

Accenture said it best: Interoperability is the next advantage for most Enterprise AI Agents.

If you want to learn how to build technical Enterprise systems with these protocols, you can join our latest cohort that we recently launched.

<img src="https://github.com/mkader/AI-Notes/blob/main/workflow%20behind%20mcp%20and%20a2a.gif">
