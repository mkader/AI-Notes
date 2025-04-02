1. Catch up on Slack threads - 70+ message Slack thread with zero context. So, instead of reading the full thread, I use AI to summarize the takeaways, action items, and next steps:
  ```
    <data>
    [Slack thread]
    </data>
    
    <instructions>
    You're a senior product leader who excels at synthesis. Your goal is to extract the key points from this Slack thread:
    - Takeaways
    - Action items and owners
    - Open questions
    Use quotes where relevant. Keep it under 250 words.
    </instructions>
  ```
2. Summarize user feedback - Manually parsing raw user feedback (e.g., Discord channels, forums, surveys) can take hours. Here’s a prompt that I use to get the insights in minutes instead:
  ```
    <data>
    [Customer interviews/surveys/community posts]
    </data>
    
    <instructions>
    You're an experienced user researcher. Extract the following from this feedback:
    - Key pain points (with supporting quotes)
    - Prioritized list of feature requests
    
    Use nested bullets with clear headlines. 
    </instructions>
  ```
3. Learn new topics fast - Learn a new topic is from a YouTube explainer video. so I use AI instead to get the takeaways instead:
  ```
    Click “Show transcript” under a YouTube video. Copy and paste the transcript into AI with the following prompt:
    
    <data>
    [Video transcript]
    </data>
    
    <instructions>
    You're amazing at extracting insights from long video transcripts. Transform the <data> transcript into a learning guide with:
    - Clear section headers
    - 20+ nested bullets
    - Include direct quotes where relevant
    Be extremely detailed and thorough.
    </instructions>
    P.S. This is also a great way to consume hour-long podcasts in 5 minutes.
  ```
4. Turn voice notes into documents - The more context you give AI, the better its output becomes. One of the best ways to give a lot of context quickly is to record a voice note and then get AI to summarize it:
  ```
    Record a voice note with Superwhisper, ChatGPT Voice, or another tool. Copy the full transcript, Paste it into AI with this prompt to clean it up:
    
    <data>
    [Voice transcript]
    </data>
    
    <instructions>
    You're a talented writer who maintains voice while adding structure. Transform these thoughts into:
    - Clear main points
    - Logical flow
    - Supporting examples
    Give me three variations. Keep paragraphs short (2-3 sentences).
    </instructions>
  ```
5. Make writing crystal clear - paste your wall of text into AI first to make it more concise. The prompt below helps me edit everything from Slack messages to docs:
  ```
    <data>
    [Text to make more concise]
    </data>
    
    <instructions>
    You're an expert editor focused on clarity. Make this more clear and concise:
    - Use simple language
    - Break into short paragraphs
    - Remove redundancy
    Give me three variations.
    </instructions>
  ```
6. Write better documentation - Documentation often takes a long time to write. AI is great at writing a first draft that you can then edit later:
  ```
    <data>
    [Code/API/Feature details]
    </data>
    
    <instructions>
    You're a senior engineer who writes exceptional docs. Create documentation that:
    - Starts with a clear overview
    - Includes practical examples
    - Covers error scenarios
    Include code samples. Keep it under two pages. Focus on what developers need to know.
    </instructions>
  ```
7. Make a quick prototype - AI prototyping has come a long way. Here’s how I use it to make prototypes for my product ideas that I can then show to stakeholders and customers:
  ```
    Draw a sketch or export a Figma. Upload the image to v0 or Bolt with the prompt below:
    
    <instructions>
    You're an expert at building rapid UX prototypes. Please make a working prototype of the attached image. Here are the requirements:
    
    [Share high-level requirements or user stories in a list of bullets]
    
    </instructions>
    Keep iterating if AI doesn’t create what you want in one go. Here’s the website v0 made for me from a wireframe that I found online:
  ```

8. Solve problems - Let’s close with perhaps the most important tip of them all: Think of AI as a coworker who is patient, knowledgeable, and available 24/7. Give it context and talk to it throughout the day.
