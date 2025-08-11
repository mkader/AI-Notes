Steal My 10 Principles of Building AI Agents:
(learned the hard way)

1. Don’t Use Agents If You Don't Have To

Nobody cares if it's an AI agent or a simple script, as long as it works. A good old if/else is faster, cheaper, and more reliable. And it's often all you need. Save the agents for when you really need them.

2. Small, Specialized, and Decoupled

Think "team of specialists," not "one agent to rule them all." A planner plans. A summarizer summarizes. A verifier checks. Decoupled agents are cheaper to run, easier to test and fix, and way more predictable.

3. Enforce Structured Output

I've learned that text is a mess to deal with. JSON is easier to debug, cheaper to parse, and acts like a contract between agents. Bonus: you can validate it automatically and stop errors before they spread.

4. Explain the Why, Not Just the What

I've discovered that anthropomorphizing AI works in many contexts. When delegating a task, don't just define the objective. Explain why it matters and provide the context in which you need it. This helps agents make better decisions with shorter prompts.

5. Orchestration > Autonomy

Autonomy sounds great, but what you need more in real life is predictability. Move all known logic (if/then, loops, retries, known procedures) out of agent prompts and into the orchestration layer.

6. Prompt Engineering > Fine Tuning

Before you jump to fine-tuning, ask: Why is the model failing?
• If it’s missing facts → try RAG.
• If it’s wrong formatting or doesn't follow your brand style → maybe fine-tune. But 80% of the time, it’s just a prompt problem.

7. Double Down on Tool Descriptions

Treat tool description as a micro-prompt that guides the agent's reasoning. Tell the agent when and why to use it, what to avoid, and include examples. Bonus: Descriptions provided by MCP servers are often insufficient. Also, consider explaining how to use multiple tools together.

8. Cache Like You Mean It

Often, an agent runs the same task on the same data over and over, like when scrapping a website. Cache responses (e.g., hash of agent ID + input) to reduce latency and API costs.

9. Use Shared Artefacts

Do you send documents you collaborate on as attachments? Of course, not. Similarly, empower your agents to collaborate by co-editing shared docs, plans, or code.

10. Log Everything (Seriously)

No logs = no learning. Track everything: inputs, outputs, retries, tool calls, agent thoughts. Add your own app-specific dimensions (e.g., customer type, use case). Then analyze errors and design evaluators.

<img src="https://github.com/mkader/AI-Notes/blob/main/10%20Principles%20of%20Building%20AI%20Agents.jpg">
