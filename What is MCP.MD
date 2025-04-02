
<img src ="https://substack-post-media.s3.amazonaws.com/public/images/840e868d-2c83-4b1b-a881-df1da6c6e332_1309x1536.gif">

Model Context Protocol (MCP) is a new system introduced by Anthropic to make AI models more powerful.

It is an open standard (also being run as an open-source project) that allows AI models (like Claude) to connect to databases, APIs, file systems, and other tools without needing custom code for each new integration.

MCP follows a client-server model with 3 key components:

Host: AI applications like Claude that provide the environment for AI interactions so that different tools and data sources can be accessed. The host runs the MCP Client.

MCP Client: The MCP client is the component inside an AI model (like Claude) that allows it to communicate with MCP servers. For example, if the AI model wants data from PostgreSQL, the MCP client formats the request into a structured message to send to the MCP Server

MCP Server: This is the middleman that connects an AI model to an external system like PostgreSQL, Google Drive, or an API. For example, if Claude analyzes sales data from PostgreSQL, the MCP Server for PostgreSQL acts as the connector between Claude and the database.

MCP has five core building blocks (also known as primitives). They are divided between the client and server.

For the clients, the building blocks are Roots (secure file access) and Sampling (ask the AI for help with a task such as generating a DB query).

For the servers, there are Prompts (instructions to guide the AI), Resources (Data Objects that the AI can reference) and Tools (functions that the AI can call such as running a DB query).
