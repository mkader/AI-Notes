# Serverless Azure Functions

* Traditionally, building and maintaining applications requires managing servers, scaling infrastructure, and handling operational concerns.

* Azure Functions enabling to execute various triggers - HTTP requests, timers, handling Azure Storage events, working with messaging services like Service Bus or events from other Azure services, and enabling you to build versatile, scalable applications.

* Serverless computing offers several key advantages:
    1. No infrastructure management: Just foucs on writing code without worrying about servers or infrastructure.
    2. Automatic scaling: Applications automatically scale based on incoming traffic and workload demands.
    3. Cost-efficiency: Pay for the actual execution time of your functions rather than maintaining always-on servers.
    4. Focus on code: Focus on business logic while the platform manages infrastructure tasks like scaling and patching.

* Common uses for Azure Functions include:
    1. Microservices: Break down complex applications into smaller, independent services.
    1. APIs: Quickly deploy and scale APIs without managing servers.
    1. Event-driven workflows: Respond to events such as file uploads, database updates, or queue messages.
    1. Background processing: Run long-running tasks like image processing or sending notifications.

## Setting Up a Basic Azure Function

* Requirements
    1. VSC, VSC extenstion install - Azure Functions extension (create, manage and deploy directly from VSC), Azurite (emulator for local Azure Storage)
    1. <a href="https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=windows%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-csharp">Azure Functions Core Tools</a> (run functions locally for testing)

* Create a simple HTTP-triggered function.
    1. Install the Azure Functions extension
    2. Create a new project: VSC -> Command Palette -> Type Azure Functions -> Create New Project -> Choose a directory -> Select language C# 
    3. Select a trigger: HTTP Trigger (most common triggers for APIs and event-driven functions).
    4. Configure the function: the function Name "SimpleHttpFunction" -> Namespace name "Test.Function" -> access level to Anonymous (public access). The function template will generate boilerplate code.
    5. Run azurite "azurite -s -l c:\azurite -d c:\azurite\debug.log"
    6. Test locally: Press F5. VSC starts a local server, test the function by sending HTTP requests to its URL (http://localhost:7071/api/SimpleHttpFunction)
    7. Deploy to Azure: Right-click the function project -> select "Deploy to Function App".

```
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

public static class SimpleHttpFunction
{
    [Function("SimpleHttpFunction")]
    public async Task<IActionResult> Run([HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequest req)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string name = req.Query["name"];

        if (string.IsNullOrEmpty(name))
        {
            using (var reader = new StreamReader(req.Body))
            {
                var body = await reader.ReadToEndAsync();
                dynamic data = JsonConvert.DeserializeObject(body);
                name = data?.name;
            }
        }

        return name != null? (ActionResult)new OkObjectResult(
          $"Hello, {name}!") : new BadRequestObjectResult(
             "Please pass a name on the query string or in the request body.");
    }
}
```
## Understanding Triggers and Bindings
* Triggers and bindings dictate how functions are invoked and how they interact with other services.
* Triggers initiate function execution. Each function must have one trigger that specifies the type of event that starts the function.
* Common trigger types include:
    1. HTTP trigger: Initiates the function via HTTP requests - suitable for building APIs and webhooks.
    1. Timer trigger: Runs on a schedule - ideal for cron-job-like tasks such as database cleanup or report generation.
    1. Blob trigger: Monitors Azure Storage containers for new or updated blobs, which is great for file processing scenarios.
    1. Queue trigger: Responds to new messages in Azure Storage queues, enabling message-based workflows.
* Bindings allow functions to connect seamlessly with other Azure services for input and output operations.
    * Bindings simplify coding by handling the plumbing needed to integrate with various data sources, so you don't have to write extensive connection logic.
    * Types of bindings include the following:
        1. Input bindings allow a function to read data from an external source, like a database or blob storage.
        1. Output bindings allow a function to send data to a service, such as writing to a Cosmos DB collection or sending a message to a queue.
    * For instance, you can create a function that processes data from an HTTP request and writes it directly to an Azure Storage table, all within a few lines of code.
    * Example binding configuration: In the function.json file, you can define bindings like:
        ``` 
        {
            "bindings": [
                {
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "methods": ["get", "post"]
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                }
            ]
        }
        ```
    
## Integrating Azure Functions with Other Azure Services
* some powerful ways to integrate functions with other services:
    1. Azure Logic Apps: Use Azure Logic Apps to orchestrate workflows that include Azure Functions as steps. This combination enhances automation and complex workflow creation without extensive code.
    1. **Azure Event Grid: **Integrate Azure Functions with Event Grid to create event-driven architectures that respond to events from other services such as Blob Storage or custom sources. This approach enables real-time data processing.
    1. **Azure Cosmos DB: **Combine Azure Functions with Cosmos DB bindings to automatically read and write data to a globally distributed database. This set up allows you to build scalable, data-centric applications.

* Event-Driven Function with Event Grid
    * The Function handles Event Grid events for newly created blobs in Azure Storage.
    * It logs the event details, checks if the event type is BlobCreated, extracts the blob URL, and provides a placeholder for further blob processing.
    ```
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json.Linq;
    
    public static class EventGridFunction
    {
        [FunctionName("EventGridFunction")]
        public static void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation($"Event received: {eventGridEvent.EventType}");
            var data = ((JObject)eventGridEvent.Data).ToString();
            log.LogInformation($"Event Data: {data}");
        }
    }
    ```

## Error Handling and Best Practices
* Error handling is crucial in building robust serverless applications.
* Azure Functions provides several mechanisms for gracefully handling errors to ensure reliability and maintainability.
* Key practices for error handling include:
    1. Structured exception handling: Use try-catch blocks in your code to catch exceptions and log meaningful error messages. This helps diagnose issues quickly.
    1. Retry policies: Configure retry policies for triggers like Queue and Event Hub to automatically reprocess failed executions.
    1. Custom logging: Use context.log for custom log entries and integrate with Application Insights for advanced monitoring and error tracking.
    1. Dead-lettering: For functions triggered by queues, enable dead-lettering to capture messages that fail after multiple retries for further analysis.
* Example: Enhanced Error Handling - if the input is invalid or an error occurs, Azure function logs the error and returns an error response with status 500.
    ```
    using System;
    using System.IO;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    
    public static class ErrorHandlingExample
    {
        [FunctionName("ErrorHandlingExample")]
        public static async Task<IActionResult> Run([HttpTrigger(AuthorizationLevel.Function, "post")] HttpRequest req, ILogger log)
        {
            try
            {
              log.LogInformation("C# HTTP trigger function processed a request.");
    
                string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
                dynamic data = JsonConvert.DeserializeObject(requestBody);
    
                if (data?.value == null)
                {
                    throw new ArgumentNullException("value", "The 'value' field is required in the request body.");
                }
    
                int result = ProcessData((int)data.value);
                return new OkObjectResult($"Processed value: {result}");
            }
            catch (ArgumentNullException ex)
            {
                log.LogError($"Input error: {ex.Message}");
                return new BadRequestObjectResult($"Missing input: {ex.ParamName}");
            }
            catch (Exception ex)
            {
                log.LogError($"Unexpected error: {ex.Message}");
                return new StatusCodeResult(StatusCodes.Status500InternalServerError);
            }
        }
    
        private static int ProcessData(int value)
        {
            if (value < 0)
            {
                throw new InvalidOperationException("Value must be non-negative.");
            }
    
            // Simulate data processing
            return value * 2;
        }
    }
    ```
## Accessing Azure SQL Database and Returning Data via API
* example where an Azure Function retrieves data from an Azure SQL Database and exposes it through an HTTP API.
* Function App -> Under Configuration -> add a new application setting, Name: SqlConnectionString and Value
      ``` 
      CREATE TABLE Products (
          Id INT PRIMARY KEY,
          Name NVARCHAR(100),
          Price DECIMAL(10, 2)
      );
      
      INSERT INTO Products (Id, Name, Price) VALUES
      (1, 'Laptop', 899.99),
      (2, 'Smartphone', 699.99),
      (3, 'Tablet', 299.99);
      ```
    ```
    using System;
    using System.Data.SqlClient;
    using System.IO;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using System.Collections.Generic;
    
    public static class GetProductsFunction
    {
        [FunctionName("GetProducts")]
        public static async Task<IActionResult> Run([HttpTrigger(AuthorizationLevel.Function, "get", Route = "products")]  HttpRequest req, ILogger log)
        {
            string connectionString = Environment.GetEnvironmentVariable("SqlConnectionString");
    
            var products = new List<Product>();
            try
            {
                using (SqlConnection conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    var query = "SELECT Id, Name, Price FROM Products";
                    using (SqlCommand cmd = new SqlCommand(query, conn))
                    {
                        using (SqlDataReader reader = await 
                          cmd.ExecuteReaderAsync())
                        {
                            while (reader.Read())
                            {
                                products.Add(new Product
                                {
                                    Id = reader.GetInt32(0),
                                    Name = reader.GetString(1),
                                    Price = reader.GetDecimal(2)
                                });
                            }
                        }
                    }
                }
    
                return new OkObjectResult(products);
            }
            catch (Exception ex)
            {
                log.LogError($"Error fetching products: {ex.Message}");
                return new StatusCodeResult(StatusCodes.Status500InternalServerError);
            }
        }
    
        public class Product
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public decimal Price { get; set; }
        }
    }
    ```

## Consume the Azure Function in a Console App
```
using System;
using System.Net.Http;
using System.Threading.Tasks;
using System.Collections.Generic;
using Newtonsoft.Json;

class Program
{
    static async Task Main(string[] args)
    {
        string functionUrl = 
          "https://<your-function-app-name>.azurewebsites.net/api/products";
        string functionKey = "<your-function-key>"; // If required, add 
                                                    // the Function key
        using (HttpClient client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("x-functions-key", functionKey); // Add key if needed
            HttpResponseMessage response = await client.GetAsync(functionUrl);

            if (response.IsSuccessStatusCode)
            {
                string responseData = await response.Content.ReadAsStringAsync();
                var products = JsonConvert.DeserializeObject<List<Product>>(responseData);
                foreach (var product in products)
                {
                    Console.WriteLine($"ID: {product.Id}, Name: {product.Name}, Price: {product.Price:C}");
                }
            }
            else
            {
                Console.WriteLine($"Error: {response.StatusCode}");
            }
        }
    }

    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

## Understanding Azure Functions Pricing
  1. Consumption Plan: pay only for the resources your functions consume. This plan includes a monthly free grant of 1 million requests and 400,000 GB-s of resource consumption per subscription.
  1. Premium Plan: It offers enhanced performance, including features like virtual network connectivity and no cold starts. Unlike the Consumption Plan, there is no execution charge, but at least one instance must be allocated at all times per plan.
  1. Flex Consumption Plan (Preview): It provides high scalability with compute choices and virtual networking. It operates in two modes:
      1. On Demand: Instances scale based on configured per-instance concurrency, with billing only for the time instances are executing functions. This mode includes a monthly free grant of 250,000 executions and 100,000 GB-s of resource consumption per subscription.
      1. Always Ready: You can configure instances to be always enabled and assigned to specific triggers and functions. Billing includes the total amount of memory provisioned for baseline and execution time, as well as the total number of executions. There are no free grants in this mode.
  1. Dedicated (App Service) Plan: functions run on dedicated virtual machines under an App Service Plan. Billing follows standard App Service Plan rates, making it suitable for scenarios requiring predictable costs and dedicated resources.

## Advanced Use Cases for Azure Functions
* some advanced use cases that showcase the versatility of Azure Functions:
    1. Real-time data processing: Pair with Event Hub or IoT Hub to handle real-time high-velocity data streams. This set-up is ideal for real-time monitoring, live data analytics, or IoT data ingestion.
    1. Microservices architecture: Build serverless microservices that communicate using Azure Service Bus or Event Grid. This approach promotes modularity, allowing you to create loosely coupled services that can scale independently.
    1. Workflow automation: Automate business processes triggered by changes in data or application events. Azure Functions can act as workflow steps, streamlining processes like document approvals, report generation, or CRM updates.
    Integration with third-party services: Create Azure Functions that connect with external APIs and services to extend application capabilities. This use case supports building middleware for payment processing, customer relationship management (CRM) integrations, or social media interactions.
These advanced scenarios illustrate Azure Functions' extensive capabilities and how they can be leveraged to build complex, cloud-native solutions.
