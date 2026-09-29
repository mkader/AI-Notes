# The New .NET AI Stack
* https://learn.microsoft.com/en-us/agent-framework/overview/?pivots=programming-language-csharp
* https://www.codemag.com/Article/266071/The-.NET-AI-Stack
  
## The New .NET AI Stack
* MS .net 8 time frame started breaking out the low-level bits from Semantic Kernel into an underlying set of libraries called Microsoft.Extensions.AI, which SK sat on top of.
* Microsoft.Extensions.AI contains concepts like IChatClient, IImageGenerator, and IEmbeddingGenerator, interfaces that work across models and across providers, including local providers like Ollama.
* The new high-level framework, Microsoft.Agent.AI has been described as the “spiritual successor of Semantic Kernel and AutoGen.”
* Agent Framework's features revolve around creating and orchestrating agents.
    * Agent Framework includes AIAgent, a concrete implementation built on top of IChatClient to extend basic, generic chat functionality, adding features like MCP integration, reliable structured output, background responses for durable, long-running workflows and workflow orchestration of agents.
    * It includes AgentSession, an implementation of memory functions like short- or long-term chat history, chat trimming, and cached knowledge.
    * Agent Framework also builds on the infrastructure features in the underlying Microsoft.Extensions.AI libraries including OpenTelemetry, Logging, Caching, Dependency Injection, Builders, and more.

* Think of these sets of libraries as two layers.
    * Microsoft.Extensions.AI is the foundation layer for basic chat and tool use.
    * Microsoft.Agents.AI (also known as Agent Framework) is the higher-level layer for agents and orchestration.

## The Development Experience
* using Azure OpenAI hosted models, AzureOpenAIClient.GetChatClient(), from the Azure.AI.OpenAI package. 

  ``` 
  <PackageReference Include="Azure.AI.OpenAI" Version="2.1.0" />
  
  using Azure.AI.OpenAI;
  using OpenAI.Chat;
  using System.ClientModel;
  
  var endpoint = "https://eus2.openai.azure.com/";
  var chatDeployment = "EPT-4.1";
  var apiKey = "sdffsdfsfsf";
  
  ChatClient aOaiClient = new AzureOpenAIClient(new Uri(endpoint), new ApiKeyCredential(apiKey))
  .GetChatClient(chatDeployment);
  
  var result = aOaiClient.CompleteChat("What is the capital of France?");
  
  foreach (var content in result.Value.Content)
  {
      Console.WriteLine(content.Text);
  }
  
  ```

* call AsIChatClient() from the Microsoft.Extensions.AI.OpenAI package and get back an IChatClient.
  * IChatClient is not specific to OpenAI or AzureOpenAI hosting or models.
  * IChatClient works the same way with an Azure Open AI Model as it does with a local Ollama model, for example.
    * Local model through Ollama library to create a different client and call .AsIChatClient().
    * OllamaSharp NuGet package recommended by MS is not currently working with the latest release candidate.
  * easier to write the response to the console, no loop through the result.Value.Content collection and specify the Text property anymore.

```
<PackageReference Include="Microsoft.Extensions.AI.OpenAI" Version="10.10.1" />

using Azure.AI.OpenAI;
using System.ClientModel;
using Microsoft.Extensions.AI;

IChatClient chatClient = new AzureOpenAIClient(
    new Uri(_azureOpenAiEndpoint),
    new ApiKeyCredential(_azureOpenAiApiKey)
)
.GetChatClient(_chatDeployment)
.AsIChatClient();

var result = await chatClient.GetResponseAsync(
    "What is the capital of France?"
);

Console.WriteLine(result);
```

  * want to stream the response instead of waiting for it to complete:
  ``` 
  await foreach (var content in chatClient
      .GetStreamingResponseAsync("What is the capital of France?"))
  {
      Console.Write(content);
  }
  ```

* use Agent Framework.
  * Agents are created with a set of instructions that help define how to act, and a name. And here's how to stream the response instead of waiting for it to complete:
```
    <PackageReference Include="Azure.AI.OpenAI" Version="2.1.0" />
    <PackageReference Include="Microsoft.Agents.AI.OpenAI" Version="1.22.0" />

using Azure.AI.OpenAI;
using Microsoft.Agents.AI.Workflows;
using OpenAI.Chat;
using System.ClientModel;

var agent = new AzureOpenAIClient(
    new Uri(endpoint),
    new ApiKeyCredential(apiKey)
)
.GetChatClient(chatDeployment)
.AsAIAgent(
   instructions: "You're a friendly assistant. Keep answers brief.",
    name: "MyAgent"
);

var response = await agent.RunAsync("Explain how to tie shoelaces");
Console.WriteLine(response.Text);
```


* A tool can be a method, a built-in tool, such as a code generator or web lookup, searching a semantic index, or calling an MCP server.
  * create my own tool in C# to retrieve weather information.
  * The [Description] attributes on both the method and the parameters are important because the LLM will use those descriptions to help it select tools and to know how to properly call its methods.
  * Now I can allow my agent to use this tool if it decides it would be helpful to accomplish its goals.
``` 
using Azure.AI.OpenAI;
using Microsoft.Agents.AI.Workflows;
using Microsoft.Extensions.AI;
using OpenAI.Chat;
using System.ClientModel;
using System.ComponentModel;

var agent = new AzureOpenAIClient(
    new Uri(endpoint),
    new ApiKeyCredential(apiKey)
)
.GetChatClient(chatDeployment)
.AsAIAgent(
    instructions: "You're a friendly assistant. Keep answers brief.",
    tools: [AIFunctionFactory.Create(WeatherTool.GetWeather)],
    name: "MyAgentWithTools"
);

Console.WriteLine(await agent.RunAsync(
    "I'm in Taos, NM. Should I take an umbrella today?"
));

public class WeatherTool
{
    [Description("Get the weather for a given location.")]
    public static string GetWeather(
        [Description("The location to get the weather for.")]
        string location)
    {
        return $"The weather in {location} is cloudy with a high of 19°C.";
    }
}
```

* create a session to store the interactions. In Agent Framework I can use the default in-memory provider
```
using Azure.AI.OpenAI;
using Microsoft.Agents.AI.Workflows;
using Microsoft.Extensions.AI;
using OpenAI.Chat;
using System.ClientModel;
using System.ComponentModel;

var agent = new AzureOpenAIClient(
    new Uri(endpoint),
    new ApiKeyCredential(apiKey)
)
.GetChatClient(chatDeployment)
.AsAIAgent(
    instructions: "You're a friendly assistant. Keep answers brief.",
    tools: [AIFunctionFactory.Create(WeatherTool.GetWeather)],
    name: "MyAgentWithTools"
);

AgentSession session = await agent.CreateSessionAsync();

Console.WriteLine(await agent.RunAsync("I'm in Taos, NM.", session));
Console.WriteLine(await agent.RunAsync("Should I take an umbrella today?", session));
```

<img width="861" height="122" alt="image" src="https://github.com/user-attachments/assets/fa7b4022-c8f2-4513-8f05-681d5f96f758" />
    
* Agent Framework now handles to retrieve some information about a book such as title, author, summary, and number of pages.
```
public class BookInfo
{
    public string? Title { get; set; }
    public string? Author { get; set; }
    public string? Summary { get; set; }
    public int? Pages { get; set; }
}

AgentResponse<BookInfo> response = await agent.RunAsync<BookInfo>(
    """Please provide information about Hitchhiker's Guide to the Galaxy, 
    by Douglas Adams. It's a humorous novel about an unsuspecting space traveler. 
    It has 413 pages.""");

Console.WriteLine(
    $"Title: {response.Result.Title}, 
      Author: {response.Result.Author}, 
      Summary: {response.Result.Summary}, 
      Pages: {response.Result.Pages}"
);
````

<img width="1215" height="88" alt="image" src="https://github.com/user-attachments/assets/b2e72911-e73a-4993-b08c-53cca5f088e3" />


# Building Multi-Agent Workflows in C# with Microsoft Agent Framework

* In this article, I'll build on those basics and introduce some more advanced concepts including how to orchestrate multiple agents into a workflow.

* Static Orchestrations
  * Sequential, Concurrent, Handoff, and Group Chat are all created with the static AgentWorkflowBuilder class. They're called static orchestrations or static workflows because the handoffs paths between agents are pre-determined.

### Sequential Workflow
  * The most basic orchestration represents a sequential workflow with a fixed set of linear, sequential steps.
  * For example, I want to use the above sample to retrieve my local weather, but configure the agent to always reply in Spanish.
  * One route I could take would be to add instructions to a single agent to reply in Spanish and rely on the agent to do both tasks.
  * In basic scenarios, if the model is powerful enough, this will work.
  * it would be more powerful to build two agents, one to retrieve local weather and another to translate text to Spanish.
```  
AIAgent agent1 = new AzureOpenAIClient(
    new Uri(_azureOpenAiEndpoint),
    new ApiKeyCredential(_azureOpenAiApiKey))
    .GetChatClient("gpt-4o")
    .AsAIAgent(
        instructions: "You're a friendly assistant. Keep answers brief.",
        tools: [AIFunctionFactory.Create(WeatherTool.GetWeather)],
        name: "MyAgentWithTools");

AIAgent agent2 = new OllamaSharp.OllamaApiClient(
    "http://localhost:11434", "llama3.1:8b")
    .AsAIAgent(
        instructions: "You are a translation assistant who only
        responds in Spanish.",
        name: "SpanishTranslator");

// Next, set up the sequential workflow with the agents.
var workflow = AgentWorkflowBuilder.BuildSequential(agent1, agent2);

// Run the workflow
var messages = new List<ChatMessage>
{
    new(ChatRole.User, "I'm in Taos, NM."),
    new(ChatRole.User, "Should I take an umbrella?")
};

await using StreamingRun run = await InProcessExecution.RunStreamingAsync(
    workflow,
    messages);

await run.TrySendMessageAsync(new TurnToken(emitEvents: true));
```
  * I can compose agents based on any provider, any model, and any tool set into a workflow.
  * the pre-built orchestration by calling AgentWorkflowBuilder.BuildSequential and passing a list (or any IEnumerable) of agents.
  * The agents will be called in the order they're listed with each agent handing off its output as input to the next agent.
```
//Show data from events as they stream in from the workflow execution.
string? lastExecutorId = null;
List<ChatMessage> result = [];

await foreach (WorkflowEvent evt in run.WatchStreamAsync())
{
    if (evt is AgentResponseUpdateEvent e)
    {
        if (e.ExecutorId != lastExecutorId)
        {
            lastExecutorId = e.ExecutorId;
            Console.WriteLine();
            Console.Write($"{e.ExecutorId}: ");
        }

        Console.Write(e.Update.Text);
    }
    else if (evt is WorkflowOutputEvent outputEvt)
    {
        result = outputEvt.As<List<ChatMessage>>()!;
    }
}
```
* Orchestrations and workflows emit a collection of events you can hook into including: WorkflowStartedEvent, WorkflowOutputEvent, WorkflowErrorEvent, WorkflowWarningEvent, ExecutorInvokedEvent, ExecutorCompletedEvent, ExecutorFailedEvent, AgentResponseEvent, AgentResponseUpdateEvent, SuperStepStartedEvent, SuperStepCompletedEvent, and RequestInfoEvent.
* can create your own custom events and emit them from your own executors (an agent is one type of executor that can exist in a workflow).
* I'm only looking for AgentResponseUpdateEvent (to watch as agents generate output for the next step) and WorkflowOutputEvent (to copy the output when the workflow is finished).
* Since we copied the output into a List<ChatMessage> named result, we can display the results like this:
```
foreach (var message in result)
{
    Console.WriteLine($"{message.Role}: {message.Text}");
}
```
<img width="1120" height="322" alt="image" src="https://github.com/user-attachments/assets/0a84077e-b252-4271-bc08-c964649bd73b" />

### Concurrent Workflow
* The Concurrent workflow is similar to the Sequential workflow except that instead of flowing in a linear fashion, multiple agents run in parallel.
* This is useful when there are several steps to complete and the steps don't depend on one another or on the order they're run in.
* For example, if after I receive a question for a user
    * and I have to both detect the language it's written in and whether it contains information I expect (such as the location I want to retrieve weather for),
    * I can create an agent for each task and run them in parallel to speed up processing.
* Aside from different instruction for agent2 to detect language instead of converting to Spanish, the code is the same except that I call BuildConcurrent instead of BuildSequential.
```
var workflow = AgentWorkflowBuilder.BuildConcurrent(new ChatClientAgent[]{agent1, agent2});
OR
var workflow = AgentWorkflowBuilder.BuildConcurrent([agent1, agent2]);
```
### Handoff Workflow
* The Handoff workflow is a little more complex in that you create an agent whose responsibility is to control the workflow and hand off tasks to other agents as necessary.
```
//Create the handoff agent which will manage the handoff between the agents.
//This is a special type of agent that has the ability to hand off 
//messages to other agents in the workflow, and decide when to do so based 
//on specified rules.
AIAgent handoffAgent = new AzureOpenAIClient(
    new Uri(_azureOpenAiEndpoint),
    new ApiKeyCredential(_azureOpenAiApiKey))
    .GetChatClient("gpt-4o")
    .AsAIAgent(
        instructions: "The user will ask you a question. If it's about 
        the weather, handoff to MyAgentWithTools. If it's about translation 
        or in Spanish, handoff to SpanishTranslator. ALWAYS handoff to another 
        agent, don't answer the user's question directly.",
        name: "HandoffAgent");

var workflow = AgentWorkflowBuilder.CreateHandoffBuilderWith(handoffAgent)
    .WithHandoffs(handoffAgent, [agent1, agent2])
    .WithHandoffs([agent1, agent2], handoffAgent)
    .Build();
```
  * As the developer, you statically determine the rules for when the handoff agent hands off to another agent.
  * The LLM doesn't reason about which agent to call next, it follows your rules. Figure 1 shows that the specialty agents can hand the process back to the handoff agent.

### Group Chat Workflow
* The Group Chat workflow is similar to Handoff workflow, but instead of having a dedicated agent in charge of controlling the handoffs, the handoffs are based on a strategy.
* The only strategy that currently comes out of the box is the RoundRobinGroupChatManager which calls agents sequentially in the order received, but unlike the Sequential workflow, the sequence can be looped through multiple times.
* Because agents could potentially keep talking amongst themselves for a very long time, delaying responses, eating up tokens and running up costs, this class has a MaximumIterationCount property which caps how many times each individual agent can be invoked.
* This pattern can work well for things like self-refinement where agents in the group judge the output of previous agents (e.g. "Is this statement correct?", or "Is this valid T-SQL?").
* In this scenario, multiple runs of the workflow often improve the final output. But the real power of the Group Chat Workflow is that you can implement your own handoff strategy by subclassing the abstract GroupChatManager class.

### Dynamic Workflows
* Magentic workflow is a new, improved version of the Magentic-One workflow pattern developed by the AutoGen team.
* It's an advanced pattern that dynamically selects agents, decomposes tasks to create plan for solving complex problems, monitors its own progress and issues, can include human-in-the-loop interactions, and more. It's basically the Cadillac of orchestrations.
* It differs from the static Handoff workflow where handoffs are determined by rules, not by reasoning, and no planning or monitoring takes place.
* Unfortunately, as of this writing, it's not available in C# yet (though it is available in Python).

### Custom Workflows—Agents, Executors, Edges, and Contracts
* Orchestrations are just pre-packaged workflows, but you can also create your own workflows from scratch.
* Workflows are built from agents, executors, edges, and contracts.
* Executors are coded classes that look like agents to Agent Framework.
* Like agents, executors handle strongly typed messages. Edges define the handoffs between agents and other executors, a.k.a. the workflow.
* And contracts are strongly typed messages that executors and agents “handle”.
```
internal sealed partial class OutputExecutor() : Executor("OutputExecutor")
{
    protected override ProtocolBuilder ConfigureProtocol(ProtocolBuilder 
    protocolBuilder)
    {
        return protocolBuilder
            .SendsMessage<List<ChatMessage>>()
            .SendsMessage<TurnToken>()
            .ConfigureRoutes(routes =>
            {
                routes.AddHandler<List<ChatMessage>>(HandleAsync);
            });
    }

    [MessageHandler]
    private async ValueTask HandleAsync(List<ChatMessage> messages, 
    IWorkflowContext context, CancellationToken cancellationToken = default)
    {
        //Send the chat message to the next agent executor
        await context.SendMessageAsync(messages, cancellationToken: 
        cancellationToken);

        //Send a turn token to signal the agent to process
        //the accumulated messages
        await context.SendMessageAsync(new TurnToken(emitEvents: true), 
        cancellationToken: cancellationToken);
    }
}
```
  * This example contains an extremely simple custom executor that does nothing but pass a message through.
  * In a real-world situation, you could add code to inspect, process, log, etc.
  * The OutputExecutor code shows that the contract (strongly typed message) being passed around is a simple List<ChatMessage>, which is pretty standard input and output for agents.
  * The OutputExecutor class inherits from the Executor base class and provides an implementation of the ConfigureProtocol method which defines that the executor will output a List<ChatMessage>, and it can also output TurnTokens which is a signal that a handoff should take place.
  * Next, it sets up a route with a new handler.
  * The handler definition is a class decorated with a [MessageHandler] attribute.
  * It handles calls made with a List<ChatMessge>.
  * Again, this custom executor is for demonstration purposes, and all it does is pass the List<ChatMessage> through, then it calls SendMessageAsync again with a TurnToken to let AF know that it needs to process the next step.
  * If you need to handle additional contract types as input, create additional handler methods that accept the contract type being passed.

* Now that we have a custom executor defined, the next step is to define the workflow.
* Edges describe the workflow.
* The most basic type of edge is the direct edge.
* In this example, the workflow is sequential: agent1 → customExecutor → agent2.
* Your workflow may have zero or more agents and zero or more custom executors.
* There are several types of edges in addition to direct, including conditional edges, switch-case edges, multi-selection (fan out 1-many) edges, and fan-in (many-1) edges.
```
AIAgent agent1 = new AzureOpenAIClient(
    new Uri(_azureOpenAiEndpoint),
    new ApiKeyCredential(_azureOpenAiApiKey))
    .GetChatClient(_chatDeployment)
    .AsAIAgent(
        instructions: "You are a friendly assistant. Keep your answers brief.",
        tools: [AIFunctionFactory.Create(WeatherTool.GetWeather)],
        name: "MyAgentWithToolsAndMemory");

//custom logic component
var customExecutor = new OutputExecutor();

AIAgent agent2 = new OllamaSharp.OllamaApiClient(
        "http://localhost:11434",
        "llama3.1:8b")
    .AsAIAgent(
        instructions: $"You are a translation assistant who only responds in 
        Spanish. Be brief",
        name: "SpanishTranslator");

//Simple, sequential workflow using Direct edges
var workflow = new WorkflowBuilder(agent1)
    .AddEdge(agent1, customExecutor)
    .AddEdge(customExecutor, agent2)
    .WithOutputFrom(customExecutor)
    .Build();
```
* You can also add features to your workflow such as human in the loop, state management, checkpoint and resume, and observability.
* Agent Framework provides the building blocks to build any workflow with any features.
* And since entire workflows (not just agents) can be treated as a step in another workflow, you can compose larger workflows from smaller ones.
