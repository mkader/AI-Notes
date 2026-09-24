https://www.youtube.com/watch?v=C_GG5g38vLU

An IBM engineer explains Agentic Harnesses in 20 min, better than any other tutorial I've seen. 

Tejas Kumar builds agent tooling at IBM. His whole point: you don't fix a flaky agent with a better prompt. You wrap it in a harness.

A harness pins a non-deterministic model to deterministic code wherever the job allows. The model is a black box you rent. It can lie, drift, or quit halfway. The Agentic Harness is the part you control, so the same run gives you the same result. 

He proved it on GPT-3.5, a bad 2023 model, doing one job: upvote the top Hacker News post. Same prompt throughout. He only built the six part harness around it.

Save this before your next build:

1️⃣ Tool registry. 
↳ Give the agent a fixed set of tools, each with its own read and write limits. Read this file, don't write to it. Run this command, not that one.

2️⃣ Model. 
↳ Don't reach for the smartest model first. A cheap, weak one plus a good harness can outperform an expensive one running loose. 
↳ Keep it swappable so you're never locked to one vendor.

3️⃣ Context management. 
↳ The context window fills fast. The harness determines how to trim and compact the history so the model doesn't choke or forget the task.

4️⃣ Guardrails. 
↳ These are the limits that stop the agent from going off the rails. 
↳ Cap the tool calls so it can't loop forever and drain your tokens. 
↳ Cap the message count too, so it compresses the context before the window overflows.

5️⃣ Agent loop. 
↳ The cycle of think, act, observe, repeat. It can even be a loop wrapped around your loop, retrying the whole job.
↳ Cap the number of retries, so it doesn't drain the tokens. 
↳ Log every tool call and action to a trace as it runs. That log is how you can see if the agent lies. 

6️⃣ Verify. 
 ↳ Check the trace, not what the model told you. It can make up stuff
 ↳ Make it admit when it failed, instead of lying about it. 

Then he proved it live:  
He took a shitty shitty 2023 model, and a simple prompt. 
It failed the task on the first try. 
Then he only added the harness, and a failing agent turned into a working one.
Model and prompt didn't change. 

That's the part worth watching.

Any model gets reliable when you make the parts you can control deterministic. Stop prompting harder. Build the harness.
