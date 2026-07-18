# MCP (Model Context Protocol) Server Tutorial: Expose Tools and Resources to AI
* https://codemag.com/Article/268021/MCP-Server-Tutorial-Expose-Tools-and-Resources-to-AI

## AI Roles
* <b>Retrieval architect (RAG specialist)</b>,
  * who focuses on grounding.
  * They bridge the gap between a “raw” AI model and a company's private data.
  * This professional builds retrieval-augmented generation (RAG) pipelines and has expertise in vector databases (like Pinecone or Milvus), semantic search, and metadata filtering.

* <b>agentic orchestrator</b>,
  * who sees AI not just as a dummy text generator but as an agent, an actor that can perform valuable tasks in an unattended form.
  * They build systems where AI can use tools, browse the web, or execute code to complete multi-step tasks.
  * This person excels at designing “agentic workflows” using frameworks like LangChain or CrewAI.

* <b>prompt engineer & evaluator</b>
  * who often comes from a technical writing or QA background.
  * This professional focuses on the interface and reliability.
  * This person is great at optimizing system instructions and creating “golden datasets” to test if model updates break existing logic.
  * This person is great at chain-of-thought prompting and automated evaluation metrics (like RAGAS or BERTScore).

* <b>AI infrastructure (AIOps) engineer</b>,
  * who is the modern evolution of the DevOps role.
  * This person ensures the plumbing for AI is scalable, cost-effective, and secure.
  * They worry about things like managing “GPU-seconds,” monitoring token usage/latency, and deploying models on-premises or in private clouds.
  * This person is great at Kubernetes, cloud resource management (AWS/GCP/Azure), and implementing “guardrails” to prevent data leakage.

* <b>fine-tuning specialist</b>
  * While many use off-the-shelf models, this professional specializes in domain-specific deep learning.
  * This person takes a base model and trains it on specialized datasets (e.g., legal, medical, or niche codebases) to improve performance on specific tasks.
  * This person excels at things such as PyTorch/TensorFlow, LoRA (Low-Rank Adaptation), and data curation, etc.

* But in this article, Focus on the agentic orchestrator persona, who is proficient in API integration, Python, and MCP to allow models to interact with local files and databases safely.

## AI as a “Brain in a Vat”
* Ask a standard LLM to “Check why the database is slow,” it will give 10-point of theoretical reasons why databases get tired.
  * What it won't do is actually look at your database.
  * But I want AI to solve my problems, not just give me generic advice.

* Realistic problem.
  * Production systems generates emergencies. But before it produces alerts.
  * For example, disk is running out of space, etc.
  * Smart person who knows what that alert means knows what to do about it.
  * If a disk is running out of space or a SQL query is taking too long to query, you clean up the disk, examine and rebuild indexes, and so forth.

* Now, with AI, asking like “What do I do when my disk is low on space?”

* Could you automate this? Could you write this logic in simple English and use AI to do more complex tasks?
  * Consider something like:
      * “This kind of conditional access policy was violated three times by the same user in the last hour.
      * Query the AAD logs for the past year for this user and establish patterns;
      * then query this user's mailbox for any suspicious outgoing emails;
      * also search OneDrive for external sharing.”

## An MCP Server
* An MCP server is a lightweight, standalone program that exposes specific data or capabilities from a local or remote system to an AI model.
  * It acts as a translator between the AI's high-level requests and the specific technical requirements (APIs, databases, file systems) of an external tool.

  * use an MCP server when you need an AI to interact with local data, for example read your local codebase, search your documents, or query a local SQLite database.
  * Or when you'd want to perform real-time actions such as post to Slack, create a GitHub issue, or trigger a cloud deployment.
  * Or when you want it to maintain state.
  * Unlike simple API calls, MCP servers can maintain a persistent connection, which is vital for complex, multi-step agentic workflows.

## MCP Server vs. Skill
* A somewhat similar concept to an MCP server is a skill.
* An AI skill is a structured set of instructions, knowledge, and tool-access patterns that teaches a model how to perform a specific, repeatable task.

* While an MCP server provides the “muscles” (the ability to read files or hit APIs), a skill provides the “brain” (the logic, tone, and step-by-step reasoning).
* In many modern agentic frameworks, a skill is essentially a packaged “mini-persona” that can be handed to an AI to make it an expert in a narrow domain.

* build or use a skill when you need the AI to go beyond general chat and follow a strict, high-quality workflow. For example,
  * when you want standardized technical tasks, i.e., you want the AI to always use a specific format for GIT commit messages or follow a specific coding style.
  * Or maybe you want to use complex multi-step workflows when a task requires chain-of-thought reasoning, for example, “First, audit the security of this code; second, check for performance bottlenecks; third, summarize findings.”
  * Or perhaps you want to do data transformation when you consistently need to turn messy inputs (like raw logs) into structured outputs (like a JSON report).
  * Or maybe you want domain-specific reasoning, giving the AI the “skill” of a senior Java architect so it critiques code with a specific focus on design patterns rather than just syntax.

* Think of an MCP server as the kitchen, and the AI skill as the recipe.

## Components of an MCP Server
* An MCP server involves 3 main players.
  * 1.<b>host</b> - is the AI application you are using (e.g., Claude Desktop, VSC or a custom Python script).
  * 2.<b>client</b> - is a component inside the host that manages the 1:1 connection to a specific server.
  * 3.<b>server</b> -  is a program providing the capabilities. This program should define 3 primitives:
    * 1.Tools - are the actions the model can take (e.g., calculate_tax()).
    * 2.Resources - are the data the model can read (e.g., customer_log.txt), and
    * 3.prompts - pre-defined templates to help the model format its thoughts.

## Lifecycle of an MCP Server
* The lifecycle is governed by a JSON-RPC 2.0 handshake to ensure both sides are “speaking the same language.”
* <b>1.initialization phase</b>.
    * Here the client sends an initialize request with its protocol version.
    * This is also where the server can communicate instructions back to the client.
    * For example, if you were writing an MCP server around log analytics in Azure, you'd say, “Execute this cheap query first, if that doesn't work, expand to progressively more expensive queries with user confirmation,” etc.
    * But at the minimum, during initialization, the server responds with its version and a list of capabilities (what tools/resources it has).
    * The client then sends an initialized notification to confirm the handshake.

* <b>2.operation phase</b>
    * Here, the AI decides it needs a tool.
    * The client sends a call_tool request.
    * The server executes the logic and returns the result.
    * This continues as a stateful, back-and-forth conversation.

* <b>3.termination phase</b>
    * When the session ends, a close method is called.
    * The server performs a “graceful shutdown,” cleaning up database connections or temporary files to avoid resource leaks.

## Building an MCP Server
* Build a “production incident assistant”—a realistic MCP server that lets an AI orchestration system fetch simulated system alerts and “reboot” failing services.
  * I'll use the Gemini CLI, but feel free to use Claude or Codex or anything.

* Let's understand the 3 main characters.
  * 1.the host - In this case, the Gemini CLI. It's the brain.
      * It decides what needs to happen. When you say something like “Such and such problem happened” it is Gemini CLI that understands that request and routes it to one of many registered MCP servers, in our case our MCP server.
  * 2.the server itself.
      * This is the Node.js app I am about to build. It holds the tools (actions) and resources (data).
  * 3.use stdio as the transport.
      * It's simple, it's fast, and it doesn't require setting up complex networking.
      * The CLI just starts your script and talks to it via standard input/output.
      * You could think of many other interesting surfaces or transports, but I'll stick with simple for this article.

## The Basic Project Setup
* 1.Install latest LTS version of Node.js, using 22.17.0.
* 2.create a folder called incident-assistant
  ```
  mkdir incident-assistant
  cd incident-assistant
  npm init -y

  npm install @modelcontextprotocol/sdk zod
  npm install -D typescript @types/node
  ```
    * The @modelcontextprotocol/sdk node package is the standard TypeScript SDK that implements the full MCP specification, making it easy to create MCP servers that expose resources, prompts and tools, build MCP clients that can connect to any MCP server, and use standard transports like stdio and Streamable HTTP.
    * This SDK has a required peer dependency on zod for schema validation.

* 3.run the following command. ``` npx tsc --init ```
    * telling the TypeScript compiler to bootstrap a new project.
    * It is the standard way to transition a project from “plain JavaScript” to “managed TypeScript.”
    * The primary action is the generation of a tsconfig.json file in your current directory.
    *  This file is the “brain” of your TypeScript project.
    *  Without it, the compiler treats files in isolation; with it, the directory is treated as a TypeScript project.
    * Using npx ensures you are using the version of the TypeScript compiler (tsc) installed in your local node_modules.
      *  If you don't have it installed locally, npx will download a temporary version to create the file.
      *  This prevents “version mismatch” issues where your global TypeScript version is different from the one your project requires.

* 4.Added a .gitignore - https://github.com/maliksahil/mcp-incident-assistant.
* see my committed code - https://github.com/maliksahil/mcp-incident-assistant/tree/59f3bf8497cc396556be4cf0077c9a06b7d78bf1.

## The Core Logic
* 1.TypeScript project, create a src/index.ts file. 
  * This is where the magic (and the bugs) will live.
  * Logic is - initialize our MCP server and we give it some fake data to work with.
  * In a real-world application, you'd connect to your business systems here, such as connection strings, sql database information, or your internal APIs, or whatever you are writing this MCP server for.
  ```
  import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
  import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
  import { z } from "zod";
  
  // Initialize the server with some personality
  const server = new McpServer({
      name: "IncidentResponsePro",
      version: "1.2.0",
  });
  
  // Fake data because real production data is scary
  const MOCK_ALERTS = [
      {
          id: "ALRT-001",
          service: "auth-api",
          status: "CRITICAL",
          message: "Memory leak detected",
      },
      {
          id: "ALRT-002",
          service: "payment-gateway",
          status: "WARNING",
          message: "Latency > 500ms",
      },
  ];
  ```

* 2.tweak your tsconfig.json as well.
  * rely on node types for a few operations, modify the “types”. ``` "types": ["node"], ```
  * Change the verbatimModuleSyntax to false so our imports work. ``` "verbatimModuleSyntax": false, ```
  * finally instruct our TS compiler to read from the src folder and drop the build in the dist folder as shown below.
      ```
      "outDir": "dist",
      "rootDir": "src",
      ```
  * full set of code changes at this point - https://github.com/maliksahil/mcp-incident-assistant/pull/1.

## Registering a Resource
* Resources are things the AI can read. We use registerResource to expose our current alerts. 
  ```
  server.registerResource(
      "active-alerts",
      "resource://incidents/active",
      {
          description: "Provides a list of currently active system alerts",
          mimeType: "application/json"
      },
      async (uri) => ({
          contents: [
              {
                  uri: uri.href,
                  text: JSON.stringify(MOCK_ALERTS, null, 2)
              }
          ]
      })
  );
  ```
* think of this code as giving your AI host a library card to a very specific, private shelf in your digital office.
* In plain English, you are telling the AI: “Hey, if you ever need to know what's going wrong with the system, go to this specific ‘address’ and I'll hand you a list of active alerts.”

* 4 important parts.
  * 1.the name: active-alerts
      * This is the internal ID for the resource. It's how the server keeps track of what you've registered.
  * 2.the address: resource://incidents/active.
      * Just like a website has a URL (https://...), MCP data has a URI.
      * This is the location your AI will look at when it wants to find this specific data.
      * It doesn't live on the internet; it lives inside your server's logic.

  * 3.the instruction manual (metadata).
      * This bit tells AI why it should care about this data.
      * The description field is actually the most important part for the AI.
      * Gemini or Claude would read this to decide: “Hmm, the user asked about system health… oh, I see a resource described as ‘Provides a list of currently active system alerts.’ I should probably read that!”
  * 4.the mimeType
      * which tells the system the data is formatted as JSON, so it knows how to parse the text.

* When all this information is collected, it is time for the “delivery guy” (the function), which can be seen as the async (uri) => { ... } part is the actual workhorse.
  * It's a function that stays “asleep” until AI “clicks” the link to that resource.
  * When triggered, it takes your MOCK_ALERTS(fake data), does JSON.stringify, which turns that data into a long string of text and content, packs that text into a format the protocol understands, and ships it off to Gemini or Claude's brain.

* full set of code changes - https://github.com/maliksahil/mcp-incident-assistant/pull/2.

## Registering a Tool
* Tools are things the AI can do. This tool will simulate “restarting” a service.
* use zod to ensure Gemini doesn't try to reboot “the moon” or “your mom.”
    ```
    server.registerTool(
        "resolve_incident",
        {
            title: "Service restart",
            description: "The name of the service to restart (e.g., 'auth-api')",
            inputSchema: {
                serviceName: z.string().describe(
                    "The name of the service to restart (e.g., 'auth-api')"),
                reason: z.string().describe(
                    "A brief explanation for the audit log of why this fix 
                    is being applied"),
            },
        },
        async ({ serviceName, reason }) => {
            console.error(
                `[AUDIT] AI is attempting to fix  
                ${serviceName}. Reason: ${reason}`);
    
            // Simulating a realistic 80% success rate
            const success = Math.random() > 0.2;
            if (success) {
                return {
                    content: [{ type: "text", 
                                text: `SUCCESS: ${serviceName} has been 
                                  gracefully restarted.` }],
                };
            }
    
            return {
                content: [{ type: "text", 
                            text: `FAILURE: Could not restart ${serviceName}. 
                              Please check logs manually.` }],
                isError: true,
            };
        }
    );
    ```
* If the resource code we looked at earlier gave AI “eyes” to see your data, this tool code gives AI “hands” to actually do work.

* In plain English, you are telling the AI: “If you decide a service needs a restart, here is the specific button you press, the information I need from you to press it, and what will happen when you do.”

* 1.the name: resolve_incident - the name of the command.
  * When AI is thinking, it sees this in its list of available actions, similar to how you see a list of apps on your phone.

* 2.the instruction manual, the metadata - This tells AI how and when to use the tool.
  * The title and description explain to the AI what the tool does.
  * AI reads this and thinks: “The user wants to fix the API… oh, I have a tool called Service restart that attempts a fix. I should use that!”
  * The inputSchema defines the “form” AI must fill out. It tells the AI: “If you want to use this tool, you must provide a serviceName and a reason.”
  * The .describe() parts are clues for the AI so it knows exactly what to type into those fields.
  * The execution (the “work”), which is the async ({ serviceName, reason }) => { ... } block is what actually happens on your computer when AI clicks the digital button.

* console.error - it prints a log to your terminal so you can see what the AI is doing. Never use console.log since that will interfere with the JSON-RPC protocol.

* use a random number to simulate a real-world scenario where restart might fail.
  * We have put in an 80% success rate.
  * In a real app, this is where you'd put code to talk to a server or cloud provider. But for a demo this is good enough.

* the feedback loop where the code sends a message back to the AI works in one of two ways:
  * SUCCESS: The AI gets a text confirmation and can tell you, “I've restarted it for you!”
  * FAILURE: AI gets an error message and can say, “I tried to fix it, but something went wrong; you should probably take a look.”

* code changes at this point - https://github.com/maliksahil/mcp-incident-assistant/pull/3.

## Add the Startup Logic
 ```
 Listing 4: Startup logic
 // The actual startup logic
 async function main() {
     const transport = new StdioServerTransport();
     await server.connect(transport);
     console.error("IncidentResponsePro is standing by.");
 }
 
 main().catch((err) => {
     console.error("Fatal error during startup:", err);
     process.exit(1);
 });
 ```
 * If the resources were the AI's “eyes” and the tools were its “hands,” this bit of code is the “phone line” and the “power switch” that turns the whole operation on.

* In plain English, you are telling your computer: “Open up a communication channel, connect my AI assistant to it, and let me know when it's ready to start taking orders.”

* 1.create a new pipe using new StdioServerTransport().
  * MCP servers need a way to send and receive messages.
  * Stdio (Standard Input/Output) creates a virtual pipe.
  * When the Gemini CLI sends a command, it goes into the pipe's input.
  * When your server responds, it pushes data out the pipe's output.
  * It's the simplest, fastest way for two programs on the same computer to talk to each other.

* 2.the handshake: await server.connect(transport), which is when the “brain” meets the “pipe.”
  * It takes all those tools and resources you defined earlier and hooks them up to the communication channel.
  * It's like plugging a telephone into the wall jack. Once this line finishes, your server is officially listening for Gemini to ask it a question.

* 3.the status light: console.error(...).
  * You'll notice we use console.error again and not console.log.
  * Because the “pipe” (stdout) is busy sending technical JSON-RPC data to Gemini, we use the “error” channel (stderr) to talk to you, the human.
  * This prints a nice message in your terminal so you know the server didn't just crash—it's actually running and waiting.

* 4.the safety net: main().catch(...).
  * Hey, computers are unpredictable. Sometimes a port is blocked or a file is missing.
  * This part is the “emergency brake.” If the server fails to start for any reason, it catches the error, prints exactly what went wrong to your screen, and process.exit(1) tells the computer to shut the program down immediately rather than let it sit there “broken” and waste memory.

* code changes at this point- https://github.com/maliksahil/mcp-incident-assistant/pull/4.

## Building the MCP Server
* To build our server, run the following command. ``` npx tsc ```
  * Since we have already configured our tsconfig.json to put the built version in the dist folder, you should now see a dist folder in your project with the built version of the code.

* Now we need to configure our CLI.
  * Specifically, our AI host, Gemini CLI in this case, needs to be told what MCP servers it can use and where they live.
  * To do so, edit the ~/.gemini/settings.json file, and add the following snippet.
    ``` 
    {
        "mcpServers": {
            "incident-bot": {
                "command": "node",
                "args": [
                    "/..fullpathto../dist/index.js"
                ]
            }
        }
    }
    ```
      * A few things to be careful of here.
        * 1.may not have a ~/.gemini/settings.json file; if it doesn't exist, create it.
        * 2.may already have MCP servers in that list. If you do, add incident-bot into that list rather than replacing the full list.
        * 3.in args, ensure you specify the full path to the index.js file you just built.
That's it. It's time to take this MCP server for a spin.

## Running the MCP Server
* Launch Gemini CLI, run the cmd to verify that your MCP server is available: ``` /mcp list ```
  * It should produce an output like Figure 2.
  * <img width="672" height="175" alt="image" src="https://github.com/user-attachments/assets/c8753bdd-1f38-43ba-b248-e0d7350f6178" />

* Now give it a prompt as below.
  ```
  Prompt
  Are there any critical alerts? If so, try to fix them and let me know the result.
  ```

* Free version of Gemini CLI is slow compare to paid version of Claude with the same MCP server are much faster and better. The results from Gemini are somewhat similar.

* Gemini starts by understanding what we mean by our command. As Figure shows, the first thing it did was to look in the current folder.
  * <img width="1425" height="139" alt="image" src="https://github.com/user-attachments/assets/94fc8ffb-64e1-44d9-9e0b-17befc43b5fa" />
  * This is an important point here. When you start Gemini or Claude, you are trusting that folder.
  * I usually create a folder called “gemini” or “claude” on my machine, and in that I specify sandbox settings, so it doesn't keep prompting me for what I consider safe.
  * But I certainly do not give these CLIs carte blanche access to my whole disk. That would be a bad idea.

* Next, Gemini uses cli_help to try and determine if our mcp_incident_bot tool can be of any help. This can be seen in Figure.
   * <img width="1232" height="117" alt="image" src="https://github.com/user-attachments/assets/fed0916b-9423-486f-a91d-4ce6e9af12a6" />

* Well, it looks like that request timed out, so now Gemini is trying alternate sources of information such as Google web search or parent directory, and then it returns to cli_help to try and figure out what we intend to do, as shown in Figure.
  * <img width="1335" height="397" alt="image" src="https://github.com/user-attachments/assets/a5f78af4-45c5-4682-886b-338361c26c67" />

* After some gyrations, Gemini knows exactly what to do and it asks for your permission to continue, as can be seen in Figure.
  * <img width="1700" height="468" alt="image" src="https://github.com/user-attachments/assets/8a7e1d1a-5cb5-492f-a9d1-62513c0566d5" />

* And now Gemini calls our action and successfully resolves the alerts as can be seen in Figure.
  * <img width="1667" height="337" alt="image" src="https://github.com/user-attachments/assets/c76e4198-6239-48c3-8cda-0356228b9cba" />

* Just solved a bunch of alerts using your very own MCP server.

## Summary
* While our example focused on restarting a server, MCP is a Swiss Army knife for developers.
* Write many other useful MCP servers.
  * an infrastructure doctor - an AI that monitors your sentry logs or AWS alerts and automatically suggests (or applies) patches to your staging environment.
  * a database whisperer - instead of writing complex SQL joins, you can ask the AI to “Find all users who haven't logged in since the 2026 tax season began” and let it query your PostgreSQL or MongoDB server directly.
  * ultimate PR reviewer? This MCP server connected to GitHub can let an AI pull down your local branch, run a linter, and check for security vulnerabilities before you even push your code.
  * knowledge hub - which connects your AI to your private Notion or Slack history so it can answer questions like, “What did the team decide about the zero-turn mower project back in January?”

* Many MCP servers already available to use.
  * GitHub, Vercel, Docker, SQLite, Postgres, Slack, Notion, Google Workspace, Microsoft 365, and many others already offer MCP servers for their functionality.
