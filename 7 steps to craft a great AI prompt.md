* what makes a great AI prompt? Well, it usually includes these 7 steps:
  1. Give AI a specific role and task: e.g., “You’re an expert editor…your task is to…”
  1. Give specific instructions: e.g., “Follow these steps carefully…”
  1. Include examples: e.g., “Analyze the <examples> to understand my style…”
  1. Use XML tags for structure: e.g., “<data></data> and <prompt></prompt>.” Putting data before the instructions can improve AI’s response quality by 30%.
  1. Give AI space to think (chain of thought): e.g., “In <thinking> tags, describe the key aspects of the edits you will make.”
  1. Ask for variations: e.g., “Give me three variations.”
  1. Add constraints: e.g., “Limit to 500 words.”

* For example, here’s a prompt that I use to create PRDs from notes and discussions:
```
<reference>
Paste in my PRD template here
</reference>

<instructions>
You are a senior product leader and a clear written communicator. Your task is to help transform my rough notes into a great PRD. Please follow these steps carefully:

1. Analyze the <reference> to understand my desired style and format. In <thinking> tags, summarize the key characteristics of my PRD template.
2. Ask me to share my notes next. 
3. Structure the PRD as follows:
a) Problem: Clearly describe: Who is the customer? What is the customer's problem? How do we know that this is a problem?
b) Goals: Include 1 goal metric and 2-3 input metrics. 
c) Solution: Clearly describe the solution and milestones. For each milestone, write concise user stories in first person view ("I see...", "I can...") with nested bullet points describing how the feature works.
4. Limit the PRD to 2-3 pages max
5. Present your response using:
<draft_prd> tags for the structured document
<follow_up> tags for follow-up items

Ask me for more information if you need it. Be as clear, concise, and specific as possible. 
</instructions>
```

* If you only remember one thing from this section, make it this: The most important step in writing a great AI prompt is to include examples.

* Anyone can write a prompt to “make a viral social post” or “edit a newsletter.” But the magic happens when you show AI exactly what “viral” and “good” means.
