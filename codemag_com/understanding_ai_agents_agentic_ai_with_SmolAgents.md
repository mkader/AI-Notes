# Understanding AI Agents and Agentic AI: Concepts, Tools, and Implementation with SmolAgents

* AI agents—can understand user intent, plan a sequence of actions, invoke external tools, execute code, and synthesize the results into intelligent responses.
* Agentic AI, represents a shift from passive LMs to active problem-solvers capable of handling complex, multi-step tasks in the real world.
  * Instead of merely predicting text, agentic AI empowers models to reason, act, and iterate, bringing them closer to true autonomous assistants.

* SmolAgents, a lightweight and developer-friendly framework designed to connect LLMs with actionable tools.
* 2 core agent types
  *  SmolAgents—CodeAgent, which specializes in generating and executing sandboxed Python code,
  * ToolCallingAgent, which enables an LLM to call external APIs, run custom functions, and integrate with a Python interpreter.

## What Is an Agent?
* an agent is a specialized system designed to perform tasks autonomously by combining language understanding, reasoning, and tool usage. Figure shows the composition of an agent.
  * <img width="250" height="150" alt="image" src="https://github.com/user-attachments/assets/2bb0ec8c-f5a9-4cba-a10a-d6f65e0be465" />
* An agent typically consists of a LLM combined with a set of tools it can invoke to perform actions.
* These tools enable the agent to interact with external systems and access information or capabilities beyond what the LLM alone can provide.
* For example
  * if a user asks a general question such as “Who is Bill Gates?”, the LLM can answer directly because its training data likely includes information about him.
  * if the user asks, “What is the weather in Singapore today?”, the LLM cannot rely on its training data, as this information changes continuously.
  * In such cases, the agent must call an external tool—such as a Weather API—to retrieve the latest weather data, which is then fed back to the LLM to generate a final, coherent response.
* An AI agent can combine a LM with external tools to perform tasks it couldn’t accomplish with language alone, such as retrieving real-time data, executing code, or interacting with other software.
* <img width="200" height="150" alt="image" src="https://github.com/user-attachments/assets/f2824d47-da3c-460a-8e5e-957ebe0795d9" />
* Specifically, an AI agent:
  * Understands natural language: Leverages a LLM to interpret user queries or instructions.
  * Reasons and plans: Analyzes the task, breaks it into steps, and decides how to proceed.
  * Acts using known tools: Selects and executes actions from a set of tools (e.g., APIs, search engines, or custom scripts) to gather data or perform operations.
  * Delivers results: Processes tool outputs and returns a coherent response to the user.
* To understand how an AI agent works, let’s explore a practical example. Suppose a user asks, “Book me a table for two at an Italian restaurant near Orchard Road tonight, and also check if it’s going to rain.” * Here’s how the agent handles this request:
  * Ask in natural language: The user submits a single, complex question that involves dining reservations and weather forecasting.
  * Task breakdown and planning: The agent analyzes the query and breaks it into separate subtasks—finding suitable Italian restaurants, checking table availability, making a reservation, and retrieving the weather forecast for the evening.
  * Tool selection and execution: The agent may call a restaurant-search API to locate Italian restaurants near Orchard Road. It then uses a reservation API to check availability and book a table. Finally, it calls a weather API to determine whether rain is expected tonight.
  * Result delivery: The agent combines the outputs from these tools and presents a cohesive answer, such as: “I’ve reserved a table for two at La Tavola on Orchard Road for 7:00 PM. Also, it looks like there’s a high chance of rain tonight, so you may want to bring an umbrella.”
* Unlike a standard AI model that only responds to text prompts, an agentic AI can act autonomously to achieve complex, multi-step goals.
* It not only interprets the user’s request but also plans, decides which tools to use, executes actions, and integrates the results into a coherent response.
* In essence, agentic AI behaves like an intelligent agent capable of reasoning, taking initiative, and interacting with external systems to accomplish tasks on behalf of the user.
* Agentic AI doesn’t just answer questions—it plans, acts, and interacts with the world to achieve complex goals autonomously.

## SmolAgents
* Numerous frameworks available for building AI agents.
* Popular options include SmolAgents by Hugging Face, LangChain, LangGraph (both by LangChain, Inc.), and Microsoft’s AutoGen.
* SmolAgents enables LLMs to reason, plan, and act by integrating tool usage directly into the agent’s workflow.
* With support for multi-step reasoning, API calls, code execution, and interaction with external systems, it provides a clean and intuitive way to create powerful agentic behaviors without unnecessary overhead.
* key features of the SmolAgents framework:
  * Agent Types
    * CodeAgent: Focused on generating and executing Python code inside a sandboxed environment.
    * ToolCallingAgent: Can use multiple tools (APIs, Python interpreter, custom functions) to solve more complex tasks.
  * Tool Integration. Agents can call built-in tools like:
    * PythonInterpreterTool: Execute code
    * DuckDuckGoSearchTool: Perform web searches
    * FinalAnswerTool: Mark the final answer
    * Custom Tools: Define your own tools to access APIs, read files, or perform specialized actions
 * Task Planning and Reasoning
   * Agents can break a user query into subtasks, select appropriate tools, execute them, and synthesize the results into a final answer.
 * Lightweight and Flexible
   * Works with multiple LLM back-ends.
   * Designed for both educational use and real-world agent applications.

## Creating Your Own Agent
1. Install a few packages
```
!pip install 'smolagents[litellm]'
!pip install ddgs
```
 * Installs SmolAgents along with the LiteLLM integration, which allows you to connect and use lightweight LLMs.
 * ddgs, a Python package for DuckDuckGo search, which can be used as a built-in tool for agents to perform web searches. O

2. For the underlying LLM, you have multiple options.
 * use OpenAI’s models
 * choose Ollama - , a platform that allows you to run LLMs locally on your machine for privacy and offline use.
```
import os
os.environ["OPENAI_API_KEY"] = "<OPENAI_API_KEY>"
```

3. create an agent using the ToolCallingAgent class from SmolAgents:
 * ToolCallingAgent instance that is ready to perform reasoning and call tools.
 * No custom tools added, and the built-in base tools are disabled (add_base_tools=False).
 * Later, you can extend this agent by adding tools such as a Python interpreter, search APIs, or any custom function, allowing it to execute multi-step tasks and interact with external systems.
```
from smolagents import ToolCallingAgent, LiteLLMModel 

# use a model from OpenAI
model = LiteLLMModel(model_id="gpt-4o-mini", api_base="https://api.openai.com/v1")

# create an agent
agent = ToolCallingAgent(tools = [], model = model, add_base_tools = False) 

```
 
4. Test this agent by asking it a simple question:
 * straightforward question for the agent, as it can answer directly using the knowledge encoded in its training data.
 * In this case, the agent doesn’t need to call any external tools;
 * the only tool it invokes is the FinalAnswerTool (“final_answer”), which signals the completion of the workflow and marks the final answer.
 * <img width="550" height="150" alt="image" src="https://github.com/user-attachments/assets/2b3301da-ad01-4d95-8d8f-74f4c3cdfdb8" />
```
agent.run("Where is Singapore located?") 
```
 
5. Try a more challenging question:
 * this query requires up-to-date information that’s not included in the agent’s training data.
 * To answer it, the agent must identify that an external tool—such as a weather API—is needed.
 * However, because our agent isn’t equipped with another tools (add_base_tools = False), it failed to answer the question.
 * <img width="550" height="150" alt="image" src="https://github.com/user-attachments/assets/046f84f0-7218-4934-97d7-f300f2dc1f1a" />
```
agent.run("What is the weather for Singapore today?")
```

## Changing LLM
 * Use Ollama model instead of OpenAI.
```  
model = LiteLLMModel(
    model_id = "ollama/llama3.2",
    api_base = "http://127.0.0.1:11434",
    num_ctx = 8192, # context window size
)
```

## Using Built-In Tools
6. When you set add_base_tools=True, the following base tools are added by default:
   * PythonInterpreterTool: To execute/evaluate Python code
   * DuckDuckGoSearchTool: For web search
   * ApiWebSearchTool: To perform API-based web searches (e.g., Brave Search)
   * GoogleSearchTool: To search via Google (using SerpAPI / Serper)
   * WebSearchTool: Another web-searching tool (supports DuckDuckGo/Bing)
   * WikipediaSearchTool: To fetch content from Wikipedia
   * VisitWebpageTool: To fetch and read webpage content
   * FinalAnswerTool: Marks the final answer in the agent’s workflow
   * UserInputTool: For interactive/user input during agent execution
   * SpeechToTextTool: Transcribes audio to text
``` 
# add all base tools automatically
agent = ToolCallingAgent(tools = [], model = model, add_base_tools = True)
```
   * <img width="550" height="375" alt="image" src="https://github.com/user-attachments/assets/4afeb038-5547-4474-ac61-c55a57261e77" />
   * <img width="550" height="300" alt="image" src="https://github.com/user-attachments/assets/fe56e3fd-5b25-4eb8-898e-b55c4c7066de" />
   * <img width="500" height="400" alt="image" src="https://github.com/user-attachments/assets/f1654fcf-56e1-4a58-99d6-c5734fcf94ae" />
   * <img width="500" height="400" alt="image" src="https://github.com/user-attachments/assets/2e0277a4-9430-4f4e-96c2-3419bbc85ad7" />
   * <img width="500" height="400" alt="image" src="https://github.com/user-attachments/assets/f4512927-09c2-40e2-a2e1-f832754180bf" />

7. If you want to include specific base tools, you can add them directly via the tools parameter when creating the agent. For example:
``` 
from smolagents import PythonInterpreterTool, DuckDuckGoSearchTool

# adds the tools manually
agent = ToolCallingAgent (tools = [PythonInterpreterTool(), DuckDuckGoSearchTool()], model = model)
```
   * With the tools added to the agent, it can now select and call the appropriate tools to answer user questions.
   * DuckDuckGoSearchTool can easily encounter rate-limit errors. Bcause DuckDuckGo doesn’t provide an official public API.
   * So most tools that rely on scraping or unofficial endpoints are subject to strict throttling.
   * If the agent sends too many requests in a short period, the service may temporarily block further queries, resulting in errors.

8. Try a query, you can error
```
agent.run("Open the file named Speakers.json")
```
   * Because the agent’s tools, including file access and web scraping tools, have limitations on accessing external resources.
   * Files must exist in a location accessible to the agent, and tools like DuckDuckGo are unreliable for frequent or automated queries.
   * To avoid these issues, it’s often better to use custom tools with official APIs, such as Tavily for search, or ensure that files are located in accessible paths when using file-reading tools.

## Building Your Own Custom Tools
* create the following tools:
   * A tool to read a file from the local directory: For accessing structured data like JSON or CSV files
   * A search tool using Tavily: A reliable search API optimized for LLMs, providing structured and concise results
   * A weather search tool using OpenWeatherMap: To retrieve up-to-date weather information for any location
   * A stock price search tool using finnhub.io: To retrieve up-to-date stock information
* Creating a custom tool in SmolAgents is straightforward.
* just define a Python function that performs the desired task and prefix it with the @tool decorator.
* Once decorated, the function becomes a tool that your agent can invoke during its reasoning and planning process.
9. Reading a File from the local directory
   * get_my_files() is a function decorated with the @tool decorator, which turns it into a tool that can be used by a ToolCallingAgent.
   * To ensure that the LLM can correctly identify when and how to use this tool, the function must include a clear and descriptive docstring.
   * The docstring should explain what the tool does, describe its input parameters, and specify its output.
```
from smolagents import ToolCallingAgent, LiteLLMModel, tool

@tool
def get_my_files(filename: str) -> str:
    """Opens a file and reads its contents
    Args:
        filename: The name of the file to open
    Returns:
        str: The contents of the file specified
    """
    with open(f'./{filename}', 'r') as file:
        content = file.read()
        return content

# set get_my_files as a tool for the agent
agent = ToolCallingAgent(tools = [get_my_files], model = model, add_base_tools = True)

agent.run("Open the file named Speakers.json")
```
* <img width="400" height="200" alt="image" src="https://github.com/user-attachments/assets/2bf476c0-7075-4dce-866c-f1107f3e4b26" />
* <img width="450" height="200" alt="image" src="https://github.com/user-attachments/assets/0fd3b57c-fbd4-400f-9468-774a65d0d894" />

Web Search Tool
In the earlier example, you may find that search results returned by DuckDuckGoSearchTool are often unreliable. A better approach is to create your own search tool using Tavily.

The Tavily API allows you to filter results by type—such as news, academic papers, or recent content—and control how many results are returned. Access requires an API key, which you can obtain at https://www.tavily.com.

Advertisement

The following code snippet shows how you can set up an agent to call a custom Tavily search tool, fetch real-time information, and answer questions using the results combined with the LLM’s reasoning.

 
import os
import requests
from smolagents import ToolCallingAgent, 
  LiteLLMModel, tool

TAVILY_API_KEY = "<TAVILY_API_KEY>"

@tool
def tavily_search(query: str, 
                  max_results: int = 5) -> str:
    """Perform a web search
    Args:
        query: The search query
        max_results: The maximum number of 
        results to return
    Returns:
        str: The search result
    """
    url = "https://api.tavily.com/search"
headers = {"Authorization": f"Bearer
             {TAVILY_API_KEY}"}
    payload = {
        "query": query,
        "num_results": max_results
    }
    
response = requests.post(url, json=payload,
  headers=headers)
    response.raise_for_status()
    return response.json()

model = LiteLLMModel(
    model_id = "gpt-4o-mini",        
    api_base = "https://api.openai.com/v1",
)

agent = ToolCallingAgent(tools=[tavily_search], 
                         model = model,
                         add_base_tools = False)

You can now ask a question like this:

 
agent.run("Who won the U.S. presidential election in 2024?")

Figure 5 shows the agent calling the tavily_search() tool.

Figure 5: The agent calling the tavily_search() tool to find answers to the question
Figure 5: The agent calling the tavily_search() tool to find answers to the question
In the second step, it will call the final_answer() tool to return the answer (see Figure 6).

Figure 6: The tool calling the final_answer() tool to return the answer
Figure 6: The tool calling the final_answer() tool to return the answer
Weather Search Tool
The next custom tool to build enables the agent to fetch real-time weather information for any location. To accomplish this, you’ll integrate the OpenWeatherMap API, a widely used service that provides current weather data, forecasts, and other meteorological information. You can apply for a free API key from https://openweathermap.org.

By connecting the agent to OpenWeatherMap, it can retrieve up-to-date weather conditions—such as temperature, humidity, wind speed, and precipitation—for a specified city. This allows the agent to answer dynamic, real-world questions like, “What’s the weather in Singapore today?” or “Will it rain in New York tomorrow?”, demonstrating how agents can combine reasoning with live data from external services.

The following code snippet shows how you can set up an agent to call a custom weather search tool and fetch real-time weather information:

 
from smolagents import ToolCallingAgent, LiteLLMModel, tool
import requests

@tool
def get_weather_info(city: str) -> str:
"""Retrieve the current weather information for a given city.
    Args:
        city: The name of the city to get the 
        weather information for.
    Returns:
        str: A description of the current weather 
        and temperature in the city.
    """
    
    api_key = "<OpenWeatherMap_API_key>"

url = f"http://api.openweathermap.org/
data/2.5/weather?q={city}&appid={api_key}&units=metric"

    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        weather = data["weather"][0]["description"]
        temperature = data["main"]["temp"]

        return f"The weather in {city} is {weather}
                 with a temperature of {temperature}°C."
    else:
        return f"Could not retrieve weather information for {city}."

agent = ToolCallingAgent(tools = [get_weather_info], model = model)

You can now ask a question like this:

 
agent.run("What is the current weather for Singapore?")
Figure 7 shows how the agent can fetch the weather for Singapore using this tool.

Figure 7: The agent using the get_weather_info() tool to fetch weather information for a country
Figure 7: The agent using the get_weather_info() tool to fetch weather information for a country
Stock Price Tool
Now that you’ve built several foundational tools (file tools, weather, etc.), the next tool to add to the agent’s toolkit is a Stock Price Search Tool. This tool allows the agent to retrieve the latest stock price for a given ticker symbol—useful for financial queries, dashboards, or automated workflows. To retrieve real-time stock prices, you’ll make use of the Finnhub API. Finnhub provides fast and reliable market data with a simple REST interface, making it ideal for building agent tools.

To use the Finnhub API, you need to sign up for a free API key at finnhub.io. The following code snippet shows how you can set up the agent to call the get_stock_price() as a tool:

 

import requests

API_KEY = "<finnhub.io_KEY>"  

@tool
def get_stock_price(symbol:str) -> str:
    """Retrieve the current stock price for a given stock symbol.
    Args:
        symbol: The symbol for the stock
    Returns:
        str: A string containing the price for the specified stock.
    """

url = f"https://finnhub.io/api/v1/quote?symbol={symbol}&token={API_KEY}"

    response = requests.get(url)
    data = response.json()    
return (f"Current Price of {symbol}: {data['c']}")

agent = ToolCallingAgent(tools = [get_stock_price], model = model)

You can now ask a question like this:

 
agent.run("What is the current stock price for Nvidia?")

The agent will now call the get_stock_price() tool to fetch the latest stock price for Nvidia (see Figure 8).

Figure 8: The agent calling the get_stock_price() tool to fetch the prices of a stock
Figure 8: The agent calling the get_stock_price() tool to fetch the prices of a stock
Agentic AI in Action
Agentic AI starts with the ability to answer simple, straightforward questions—like checking today’s weather or converting a file format—but its real strength emerges when faced with more complex, multi-step problems. Instead of merely providing information, the agent can plan, reason, and use multiple tools to achieve a goal: fetching live market data, analyzing trends, querying databases, generating reports, or orchestrating entire workflows automatically.

To see this in action, let’s consider a concrete example where agentic AI shines: breaking down a complex question into smaller steps and calling different tools to systematically arrive at a complete answer. Listing 1 shows an agent equipped with two tools: weather_tool() and currency_exchange_tool().

Listing 1. An agent with two tools
from smolagents import LiteLLMModel, ToolCallingAgent, tool
import requests

@tool
def weather_tool(city: str) -> str:
    """
    Fetches the weather for a city using OpenWeatherMap API.
    
    Args:
        city: Name of the city to get the weather for.
    """
    api_key = "<OpenWeatherMap_API_KEY>"  
    url = "http://api.openweathermap.org/data/2.5/forecast"
    params = {
        "q": city, 
        "appid": api_key, 
        "units": "metric", 
        "cnt": 8
    }  # next 24h

    response = requests.get(url, params=params)
    data = response.json()
    if "list" in data:
        temps = [item["main"]["temp"] for item in data["list"]]
        avg_temp = sum(temps) / len(temps)
        description = data["list"][0]["weather"][0]["description"]
        return f"Weather in {city} next 24 hours: {description}, 
            avg temp {avg_temp:.1f}°C"
    else:
        return f"Failed to fetch weather for {city}."

@tool
def currency_exchange_tool() -> str:
    """
    Fetches the current USD to EUR exchange rate.
    """
    url = "https://api.exchangerate-api.com/v4/latest/USD"
    response = requests.get(url)
    data = response.json()
    if "rates" in data and "EUR" in data["rates"]:
        return f"Current USD → EUR rate: 
            {data['rates']['EUR']:.4f}"
    else:
        return "Failed to fetch exchange rate."

# the LLM to use
model = LiteLLMModel(model_id="gpt-4o-mini")

# the tools to use for this agent
tools = [weather_tool, currency_exchange_tool]

# create the agent with the LLM and tools
agent = ToolCallingAgent(
    model = model,
    tools = tools   
)

query = """
  Check the weather for Paris next week. If the weather 
  is cool (avg temp < 20°C), fetch the USD → EUR exchange rate.
  Return me BOTH the weather for Paris and the USD → EUR 
  exchange rate.
"""

# call the agent
result = agent.run(query)
print("\nAgent final answer:")
print(result)
For instance, you might ask the agent a question like:

 
"Check the [MS2.1]weather for Paris next week. If the weather is 
cool (average temperature < 20°C), fetch the USD → EUR exchange rate.
Return both the weather for Paris and the USD → EUR exchange rate."
You will see the agent decompose the request into two separate tasks and call the corresponding tools one after the other. First, it retrieves the weather data for Paris, then, if the condition is met, it fetches the USD → EUR exchange rate, and finally it combines the results to provide a complete answer (see Figure 9).

Figure 9: The agent breaks down the question into separate tasks and calls the respective tools to answer the question
Figure 9: The agent breaks down the question into separate tasks and calls the respective tools to answer the question
CodeAgent vs. ToolCallingAgent
So far, all the agents you’ve built are based on the ToolCallingAgent, which is specifically designed to allow an LLM to reason, plan, and select the appropriate tools to accomplish a given task. This agent type is particularly effective for queries that require multi-step workflows, such as combining file access, web searches, and API calls to generate a complete response.

However, there are scenarios where the agent’s primary task is to write, execute, and reason with Python code rather than call external APIs or services. For these cases, SmolAgents provides the CodeAgent class. CodeAgent focuses on sandboxed Python execution, allowing the agent to perform computations, manipulate data, and generate outputs directly within a controlled environment. This makes it ideal for data analysis, algorithm development, debugging, or any task where code execution is central to solving the user’s query. By using CodeAgent, developers can create agents that handle programming-focused tasks without relying on external tools, making it a powerful complement to ToolCallingAgent in an agentic AI workflow.

The code snippet in Listing 2 demonstrates how a CodeAgent can be used to write Python code to answer a user’s analytical questions.

Listing 2. Using the CodeAgent to write Python code to answer an analytical question
from smolagents import CodeAgent, LiteLLMModel

# Initialize the model
model = LiteLLMModel(model_id = "gpt-4o-mini")

# Create the CodeAgent
agent = CodeAgent(tools = [], model = model)

# Inline data: student scores
student_scores = [
    {"Name": "Alice", "Score": 85},
    {"Name": "Bob", "Score": 92},
    {"Name": "Charlie", "Score": 78},
    {"Name": "David", "Score": 90},
    {"Name": "Eva", "Score": 88},
]

# Task for the agent
task = f"""
You have the following student scores:

{student_scores}

1. Compute the average score.
2. Identify the student(s) with the highest score.
3. Identify the student(s) with the lowest score.
Return a clear summary including all three points.
"""

# Run CodeAgent
response = agent.run(task)
print(response)
Figure 10 shows the code generated by the CodeAgent. You can see that in Step 1 (in Figure 10), it creates a set of Python statements to calculate the total and average scores for all the students.

Figure 10: The CodeAgent generates Python code to answer the user’s question.
Figure 10: The CodeAgent generates Python code to answer the user’s question.
In Step 2, it generates the code to find and print the highest score and the name of the corresponding student:

 
# Finding the highest score and the corresponding 
# student(s)      

highest_score = max(student['Score'] for 
  student in scores)  

top_students = [student['Name'] for 
  student in scores if student['Score'] == 
  highest_score]                      

print(f"Highest score: {highest_score}, 
  Students: {top_students}")

Likewise, in Step 3, it generates the code to find and print the lowest score and the name of the corresponding student:

 
# Finding the lowest score and the corresponding 
# student(s)   

lowest_score = min(student['Score'] for 
  student in scores)   

lowest_students = [student['Name'] for
  student in scores if student['Score'] == 
  lowest_score]                    

print(f"Lowest score: {lowest_score}, 
  Students: {lowest_students}")

It finally returns a JSON string containing the results—the highest and lowest scores and the corresponding name of the students:

 
{
  'average_score': 86.6, 
  'highest_score': 
    {
      'score': 92, 
      'students': ['Bob']
    }, 
  'lowest_score': 
    {
      'score': 78, 
      'students': ['Charlie']
    }
}
Summary
This article explores the capabilities of agentic AI and demonstrates how it can handle both simple and complex multi-step tasks. Starting with basic queries, agents can reason, plan, and orchestrate multiple tools to fetch data, analyze trends, and generate actionable insights. I introduced the ToolCallingAgent, which allows an AI agent to call specialized tools to answer user questions efficiently.

Several practical tools were implemented in the article, such as:

weather_tool(): Retrieves weather data for a specified location
currency_exchange_tool(): Fetches exchange rates between currencies
get_stock_price(): Retrieves real-time stock prices from Finnhub.io
Through examples, I demonstrated how the agent can decompose complex requests—like checking weather conditions and fetching currency exchange rates or stock data—by invoking these tools step by step, combining results, and delivering a complete answer.

I also highlighted CodeAgent, which enables the agent to execute Python code for analytical questions. Overall, this showcases how agentic AI, equipped with ToolCallingAgent and specialized tools, can transform traditional Q&A systems into intelligent assistants capable of solving real-world problems end-to-end.

What is Tavily?
Tavily is a search API service designed specifically for AI agents and applications. Unlike scraping Google or using simpler search wrappers like DuckDuckGo, Tavily provides a structured JSON API with relevant, concise search results. It’s optimized for LLM workflows, so results are easy for an AI to interpret and incorporate into reasoning.

Sponsored Sidebar: Adding LLM Assistants to Your Apps
The future is here now and you don’t want to get left behind. Unlock the true potential of your software applications by adding AI Assistants.

CODE Consulting can assess your applications and provide you with a roadmap for adding LLM features and optionally assist you in adding them to your applications.

Reach out to us today to get your application assessment scheduled. www.codemag.com/ai
