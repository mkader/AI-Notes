# The Azure Service Bus

* Azure Service Bus is for developing message-based applications.
 
## What's a Message-Based Architecture For? 
  * It' to decouple the components of a system. To see what decoupling is all about, let's first look at the opposite: a synchronous, tightly coupled system.

  * A Tightly Coupled System 
      * In vending machine, put in some money, push a button and got snack. This is a traditional, tightly coupled call sequence.
      * <img width="200" height="150" alt="image" src="https://github.com/user-attachments/assets/eb6fb8fb-f853-477b-9993-f229131bba60" />
      * In terms of software, a web service (vending machine) that exposes an “OrderSnack” endpoint. Customer interaction with the web service is tightly coupled and synchronous. POST a request message to the service and get back a response (snack or maybe a “400: Snack not found” error).

  * A Loosely Coupled, Message-Based System
      * From favorite cafe, go to the counter and order a full breakfast. Back in the kitchen, someone prepares your meal and omeone brings it out to you. In this sequence, the process of placing an order and preparing the order are decoupled and are connected by a message.
      * <img width="450" height="200" alt="image" src="https://github.com/user-attachments/assets/2b151823-355d-4e45-a4e7-6c6ce9bae1aa" />
 
          * The key feature of this diagram is the presence of the Order Queue. This queue decouples the Counter and the Kitchen.
          * The Counter doesn't cook your food or even interact with the Kitchen directly. Instead, the Counter enqueues the order and later a cook in the Kitchen dequeues it and starts preparing it.
          * This decoupling allows the Counter and the Kitchen to be implemented and scaled separately.
      * In the real world, the Counter and the Kitchen people do totally different jobs, different skills and different equipment (implemet).
          * Likewise, if the Kitchen is falling behind, you can “scale” it, i.e., hire more cooks, without having to hire more counter clerks.
          * Overall, the system is much more flexible and scalable and can efficiently serve a nice hot meal to many more people than a set of vending machines.

  * Software systems are filled with these kinds of queue-based systems.
      * Think of an e-commerce platform: the place order, pick-and-pack, ship, and invoice functions are all going to be chained together with messages, not tightly coupled.
      * Azure Service Bus allows you to develop this kind of architecture. It provides the plumbing—the queues and messages—that make a messaging-based architecture possible.

## Requirments
1. Create an Azure Service Bus Namespace is a container that holds one or more Queues, Topics and Subscriptions.
    * ``` az servicebus namespace create --resource-group rg1 --name TestBusNS --location westus ```
3. Create an Azure Service Bus Queue
    * ``` az servicebus queue create --resource-group rg1 --namespace-name TestBusNS --name MyFirstQueue ```
    * the queue is immediately active and has no messages. 
    * <img width="500" height="75" alt="image" src="https://github.com/user-attachments/assets/de96bf38-8ace-4d5e-a3fc-06d07df5ffdf" />
5. Install the Azure.Messaging.ServiceBus NuGet Package in VS, using statements ``` using Azure.Messaging.ServiceBus; ```
6. Connect to the Service Bus Namespace using a ServiceBusClient instance.
   * For both sending and receiving messages, need to instantiate a ServiceBusClient that manages the authentication and access to resources within a Service Bus Namespace. 
   * DefaultAzureCredentials is for a prod environment with sophisticated Azure authentication or Azure Service Bus connection string
   * ``` (Settings > Shared access policies > RootManagerSharedAccessKey > Primary Connection String) ```
   * ``` az servicebus namespace authorization-rule keys list --resource-group rg1 --namespace-name "TestBusNS " --name "RootManageSharedAccessKey" --query "primaryConnectionString" --output tsv ```
   ``` 
    string nameSpace = "TestBusNamespace.servicebus.windows.net";
    DefaultAzureCredential creds = new();
    ServiceBusClientOptions options = new()
    {
        TransportType = ServiceBusTransportType.AmqpWebSockets
    };
    ServiceBusClient client = new(nameSpace, creds, options);

    //OR
    //string constr = ConfigurationManager.AppSettings["ConStr"];
    //ServiceBusClient client = new(constr);
   ```

## Send and Receive
5. Send a Message - create a sender, create a message, send. Once you run this code, the message should show up as an Active message in the Azure Portal
  ``` 
    ServiceBusClient client = new(constr);
    string queue = "MyFirstQueue";
    ServiceBusSender sender = client.CreateSender(queue);
    ServiceBusMessage msg = new("This is my first message!");
    await sender.SendMessageAsync(msg);
  ```
  * <img width="400" height="50" alt="image" src="https://github.com/user-attachments/assets/9ae3b70d-9051-4a6b-85b4-1314c0c02d81" />

6. Receive a Message - create a receiver, receive, complete. Run the code, the Active message count in the queue returns to zero and you can see both the send and receive activity recorded in a handy graph
  ```
    ServiceBusClient client = new(constr);
    string queue = "MyFirstQueue";
    ServiceBusReceiver receiver = client.CreateReceiver(queue);
    ServiceBusReceivedMessage msg = await receiver.ReceiveMessageAsync();
    if (msg != null)
    {
        await receiver.CompleteMessageAsync(msg);
    }
  ```

## Message Delivery Order
  * What happens if there's more than one message in the queue? Which one does the receiver get?.
  * A queue is a first-in-first-out (FIFO) data structure. That means that if you send multiple messages to a queue, your receiver will receive them in the order they were sent.
  * <img width="500" height="100" alt="image" src="https://github.com/user-attachments/assets/795fe25a-37fb-44f2-9be2-bc25d4f04702" />
  * Azure Service bus does contain features like peeking and deferring that you can use to get around strict FIFO ordering. But as long as you stick with typical receiver patterns, it's easy to preserve strict FIFO if you want to.

## Topics and Subscriptions (“pub-sub” or publish-subscribe or publisher-subscriber)
  * The idea with a pub-sub architecture is that a publisher can publish a message once and multiple subscribers can then get the message.
  * Azure Service bus supports this pattern very naturally with Topics and Subscriptions.

7. A Topic is an Azure Service Bus Queue that has been configured to allow multiple receivers, which are called Subscriptions. Create a topic
  * ``` az servicebus topic create --resource-group rg1 --namespace-name TestBNS --name MyFirstTopic ```
  * Once a Topic is created, it shows up in the Topics list of your Namespace
  * Note that unlike a Queue, your Topic does not have Message count property, but it does have a Subscription count.
  * <img width="500" height="75" alt="image" src="https://github.com/user-attachments/assets/32c7551d-21d3-4267-8dfe-83b8fd86e0c5" />
 
8. Once your Topic is set up, you can create multiple Subscriptions.
  * ``` az servicebus topic subscription create  --resource-group rg1 --namespace-name TestBusNS --topic-name MyFirstTopic --name Subscription1 ```
  * Subscription looks just like a Queue, complete with Message count and all the other Queue properties.
  * <img width="450" height="33" alt="image" src="https://github.com/user-attachments/assets/74c5e9fe-029f-49cc-8259-9f358f8658e4" />
  * Why? Send a message to a Topic, Copy of that message gets sent to each Subscription, so each Subscription functions like an independent queue.
  * <img width="400" height="250" alt="image" src="https://github.com/user-attachments/assets/d1b16729-b316-472c-8fd9-d0a7176c2aec" />
  * The Topic itself doesn't really hold any messages. It has no message counts but the Subscriptions do. This pattern continues as you start to receive messages.
  * the Sender posted a second message. Meanwhile, Subscription 1 receives the first message.
  * <img width="550" height="225" alt="image" src="https://github.com/user-attachments/assets/03e85d1c-9b4a-47f7-a7e8-78b22eab2b82" />
  * 3 Subscriptions function as totally independent message queues. Receiving from one has no effect on the others, but sending to the Topic always sends to all Subscriptions.
9. code to send a message to a Topic same like queue
    ``` 
    string topic = "MyFirstTopic";
    ServiceBusSender sender = client.CreateSender(topic);
    ServiceBusMessage msg = new("Hello to all my subscribers!");
    await sender.SendMessageAsync(msg);
    ```
10 .code to receive from a Subscription same like queue
    ```
    string topic = "MyFirstTopic";
    string sub = "Subscription1";
    ServiceBusReceiver receiver = client.CreateReceiver(topic, sub);
    ServiceBusReceivedMessage msg = await receiver.ReceiveMessageAsync();
    ```

## Message Payload
* A heavy message is one where the body of the message contains everything the receiver needs to process the message. Consider a real-world example.
    * you're a sales representative and you're working on a bid for a customer. Your company has a strict approval process.
    * Now imagine that it's 30 years ago, so to get your bid approved you need to print it out, put a sticky note on top saying “Please review”, and place it in your manager's inbox
    * <img width="350" height="100" alt="image" src="https://github.com/user-attachments/assets/bd972511-d2a6-4833-9bd8-e0262abeefbb" />
    * In this case, the queue is the physical inbox and the message is the printed bid with your post it.
    * This message contains everything the manager needs to review the bid. They don't have to ask you for a copy or pull it from a file cabinet. It's all right there. This is a “heavy” message.
    * Downside - heavy messages are stateful and if the real-world state of the data changes, they become stale, which can create data consistency issues, especially if you're not strict with message ordering.
    * In addition, these messages contain the only copy of the data, you need to be extra careful not to lose the messages because there's no way to recover the lost information. 

* Light Message
    * Present day, modern CRM system to prepare your bids. As you work on the bids, the data is saved in a db.
    * Click a “Submit for Approval” and a message gets posted to a collaboration channel saying Bid 123 for Customer X is ready for your approval.
    * To review and approve the bid, the manager needs to retrieve the item from the database
    * <img width="350" height="150" alt="image" src="https://github.com/user-attachments/assets/4e0e1352-4127-4d5f-b04e-43b5369480ee" />
    * In this case, the queue is the manager's collaboration feed and the message is a simple post notifying them that they have something to approve.
    * The message is light. It does not contain everything needed for the review. Instead, it acts as a signal telling the manager that something happened.
    * Light messages are simple, easy to compose and support an event-based architecture where one component can signal another about an event and then the other can decide what action to take.
    * The downside is that component systems may need to share some form of access to a repository that can tightly re-couple the systems.

## JSON Serialized and Binary Content
  * use a simple model class and JSON serialization. This works for both light and heavy messages.
    ```
    public class Bid
    {
        public int BidId { get; set; }
        public DateTime BidDate { get; set; }
        public string Customer { get; set; }
        public string Description { get; set; }
        public decimal Amount { get; set; }
    }
     
    Bid bid = new()
    {
        BidId = 1000,
        BidDate = DateTime.UtcNow,
        Customer = "ACME Widgets",
        Description = "Plastic Pellets",
        Amount = 25000M
    };
    
    ServiceBusMessage msg = new(JsonSerializer.Serialize(bid));
    // or
    ServiceBusMessage msg = new() { Body = bid };
    ```

  * For a heavy message, use a much larger and more complex data model like BinaryData. Just know your message size limitations that depend on subscription and protocol.
  * A Service Bus Message Body is actually BinaryData, This means if you wanted to go with a really heavy message where a complete binary document is in the message
    ``` 
    byte[] docBytes = await File.ReadAllBytesAsync(bidDocumentPath);
    ServiceBusMessage msg = new()
    {
        Subject = JsonSerializer.Serialize(bid),
        Body = new BinaryData(docBytes)
    };
    ```
  * On the Premium Tier using AMQP Web Sockets, messages can be up to 100 MB.

## Correlation IDs
* Correlation IDs are one of the most important and useful features of any message-based or microservices architecture.
* In any decoupled system where there are multiple independent components that handle part of a process, there's often the need to trace the journey of a single request across the entire chain.
* Consider the example of an e-commerce application.
    * When you click “Submit Order” in your favorite shopping app, a whole series of actions happens.
    * A credit card service processes your payment.
    * An email service sends you a confirmation.
    * A fulfillment service starts picking and packing your order.
    * A shipping service ships your order.
    * The email service notifies you of the shipment.
    * The billing service may send you a statement.
    * How do you map out the journey of a single order across all these systems, each of which may have its own repository and its own log mechanism?
* The answer is to use Correlation IDs. A Correlation ID is an identifier (often a GUID), assigned by the first system in the chain, and then passed through in every subsequent message. 
``` 
ServiceBusMessage msg = new()
{
    Subject = JsonSerializer.Serialize(bid),
    Body = new BinaryData(docBytes),
    CorrelationId = Guid.NewGuid().ToString()
};
```

## Receiver Modes and Options
* Receiving Azure Service Bus messages (queue or topics or subscriptiopn), lot of options.

### 1. Receive Timeout
* What happens if there are no more messages in the queue when you call ReceiveAsync? The answer is that your code will wait for sixty seconds (the default timeout) and then return null.
* You can customize the receive timeout by passing a TimeSpan to ReceiveMessageAsync.
* Regardless of the timeout you use, it's possible that ReceiveMessageAsync may return null, so before processing your message, you should always check that you did indeed get one:
  ```
  TimeSpan timeout = TimeSpan.FromSeconds(5);
  ServiceBusReceivedMessage msg = await receiver.ReceiveMessageAsync(timeout);
  if (msg != null)
  {
      // process the message...
  }
  ```

### 2. Receive-and-Delete
* Azure Bus supports two different receive modes: Peek-Lock and Receive-and-Delete.
* Understanding these receive modes is one of the most important concepts in the world of Azure Service Bus.
    * Simple, Receive-and-Delete.
        * the act of receiving a message causes it to be immediately and permanently deleted from the queue.
        * There's no need to do any additional processing, like completing the message.
        * In fact, if you call CompleteMessageAsync() when using Receive-and-Delete mode, you'll get an exception.
        * It's not the default receive mode. To use it, you need to explicitly configure your receiver as shown:
        ```
          ServiceBusReceiverOptions options = new()
          {
              ReceiveMode = ServiceBusReceiveMode.ReceiveAndDelete
          };
          ServiceBusReceiver receiver = client.CreateReceiver(queue, options);
          ServiceBusReceivedMessage msg = await receiver.ReceiveMessageAsync(timeout);
        ```
    * Peek-Lock
        * the act of receiving a message is more complicated and potentially more confusing.
        * When you receive the message, the server sets up a peek-lock on your message. T
        * his means that the message is marked as locked and no other receiver can receive it while it's locked.
        * Then the server starts a timer. Your client code needs to take some sort of completion action before that timer expires.
        * You have four options, Choosing the correct completion option is critical.
            * If you processed the message successfully, you want to Complete it.
            * But what if something goes wrong? This gets tricky and your choice depends a lot on the nature of the problem and the logic of your application.
            * If you're dealing with a “poison message” (i.e., one that will never be able to be processed) it's best to just Dead Letter it right away, otherwise you risk clogging up your queue with bad messages.
            * However, if the message failed but you might be able to recover and process it on a retry, then maybe Abandon is the right move.
        
### 3. Peek-Lock Expiration
* What happens if your peek-lock expires? On the server side, if a peek-lock expires, the server treats it like an Abandon.
* This means the message is unlocked and the retry counter is incremented.
* On the client side, if your peek-lock expires as soon as you call one of the completion methods, you'll receive an exception.
* There are a couple of techniques available to you to help manage Peek-Lock expirations.
    1. Configure the Message lock duration. Azure Portal -> queue -> overview -> settings section -> access the Message lock duration. Click Change to adjust the duration
        * <img width="350" height="50" alt="image" src="https://github.com/user-attachments/assets/dc942f7f-e470-4fc2-ab84-d6147ac47666" />
        *  This approach can be helpful if, on a routine basis, message processing takes a bit longer than one minute.
        *  By simply expanding the lock, you can save yourself from experiencing unwanted timeouts.
    2. renew the lock using RenewMessageLockAsync().
        * Conceptually, this is simple, but implementing it is a little tricky because your code needs to somehow keep track of how much time has expired while you're processing your batch of messages.
        * This involves some parallel processing code, which is beyond the scope of this article.

## Receiver Patterns
* A real application is going to receive and process multiple messages over a long period of time. There are several ways you can approach this.
    1. A Message Pump - is a long-running loop that processes messages as they come in, like this:
      ``` 
      while (true)
      {
          // long running loop
          var msg = await receiver.ReceiveMessageAsync(timeout);
          if (msg != null)
          {
              // process the message
              await receiver.CompleteMessageAsync(msg);
          }
      }
      ```
        *  For production code, add exception handling and a way to gracefully exit the loop with something like a CancellationToken.
    2. Batch Processing - create a Service Bus Receiver that works in a more batch-based fashion by receiving a set of messages:
        ``` 
        IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(10, timeout);
        foreach (ServiceBusReceivedMessage msg in messages)
        {
            // process the message
            await receiver.CompleteMessageAsync(msg);
        }
        ```
        * In this example, you passed the number 10 to ReceiveMessagesAsync. This is the maximum number of messages you want returned in the batch.
        * If there were 25 messages in the queue, you'd get the first 10. If there were only seven messages in the queue, you'd get all seven.
        * Interestingly, you can combine the Message Pump pattern with Batch Processing and write a long-running loop that pulls batches of messages instead of single messages.
        * When you receive a batch of messages, the peek-lock timer starts for all of the messages in the batch. This can lead to unexpected lock timeouts if you're not careful.
            * Let's say that your lock timeout has the default of one minute. Then, let's say you know that it takes about 10 seconds to process each message.
            * Doing the math, you calculate that you can realistically pull five messages at a time because 5 * 10 = 50 seconds, which is comfortably under your timeout of 60 seconds.
            * for some reason, messages are moving slowly. so now, your messages are taking 20 seconds each, rather than 10. Here's what happens:
                  * Message 1: Total elapsed time 20 seconds,
                  * Message 2: Total elapsed time 40 seconds
                  * Message 3: Total elapsed time 60 seconds
                  * Message 4: Total elapsed time: Error–Peek-Lock Timeout
                  * Message 5: Total elapsed time: Error–Peek-Lock Timeout
            * You have several ways to manage this situation, such as lengthening the peek-lock timeout, reducing your batch size, and tracking elapsed time, so you can renew locks if needed.
### Event-Based Message Receiver
* The Azure Service Bus namespace contains a useful class called the ServiceBusProcessor.
* This class implements a message pump for you and provides an elegant, event-based programming model.
* Under the hood, ServiceBusProcessor is, in fact, a wrapper around one or more ServiceBusReceivers.
    ```
    ServiceBusProcessor processor = client.CreateProcessor(queue);
    processor.ProcessMessageAsync += OnMessageAsync;
    processor.ProcessErrorAsync += OnErrorAsync;
    await processor.StartProcessingAsync();
    ```
    * In this code, you create a ServiceBusProcessor, wire up two event handlers, and then start the processor.
    *  2 event handlers look like:
        ``` 
        async Task OnMessageAsync(ProcessMessageEventArgs args)
        {
            // process message here
        }
        
        async Task OnErrorAsync(ProcessErrorEventArgs args)
        {
            // process error here
        }
        ```
    * By default, the ServiceBusProcessor is using Peek-Lock as the receive mode and it's pulling one message at a time.
    * It's also using a feature called autocomplete, which means that at the end of your ProcessMessageAsync handler, the message is automatically completed for you.
    * You don't need to call CompleteMessageAsync. Likewise, ProcessErrorAsync handler automatically calls AbandonMessageAsync for you.
    * The ServiceBusProcessor class implements a message pump for you and provides an elegant, event-based programming model.
    * You can control all these behaviors by using ServiceBusProcessorOptions. For example, let's say you wanted to use Retrieve-and-Delete with a batch-based receiver that can pull ten messages at a time.
        ```
        ServiceBusProcessorOptions options = new()
        {
            ReceiveMode = ServiceBusReceiveMode.ReceiveAndDelete,
            PrefetchCount = 10
        };
        
        ServiceBusProcessor processor = client.CreateProcessor(queue, options);
        ```

## Errors and Fault Tolerance
* <img width="550" height="150" alt="image" src="https://github.com/user-attachments/assets/7b2d9915-a961-4759-97f2-9cb5ca0c0794" />
* Weather Data collection application. There are weather stations all over that periodically send weather reports to an input queue. From there, a report processor receives the messages, validates them, and writes them to a SQL Server database
* With this application in mind, look 2 mos important kinds of error conditions to plan for: poison messages and transient error conditions.
* 1. Poison Messages - is a message that can never be processed by your application.
    * No matter how many times you retry it, it will never succeed.
    * Imagine that the Weather Report application uses a ServiceBusProcessor and the ProcessMessageAsync event handler looks like this:
        ``` 
        async Task OnMessageReceived(ProcessMessageEventArgs args)
        {
            string json = args.Message.Body.ToString();
        
            WeatherData report = JsonSerializer.Deserialize<WeatherData>(json); // poison?
            // more message processing code...
        }
        ```
    * What happens if the message body isn't valid JSON? When you attempt to deserialize it, the call to Deserialize throws a System.Text.Json.JsonException.
    * This is a poison message. No matter how many times you try to deserialize it, it will never work.
    * If you leave the code above as is (without any exception handling), that exception will cause your ProcessErrorAsync event handler to fire. Let's say this is your handler:
        ``` 
        async Task OnMessagError(ProcessErrorEventArgs args)
        {
            await LogError(args.Exception.Message);
        }
        ```
    * What's the end result? If you're using Peek-Locks and the default Max Delivery Count of 10, this poison message is going to retry 10 times and then, after the tenth try, it ends up in the Dead Letter Queue.
    * This is not a great behavior. Now imagine that instead of one bad message, you have thousands. Maybe it's a bad actor intentionally sending poison messages.
    * Your Service Bus Processor will dutifully retry them all (10 times by default) and then they will all end up in the Dead Letter Queue.
    * In the best case, your app churns and it costs you money on your Azure subscription. In the worst case, valid requests start getting choked off.
    * The solution is to identify poison messages on the first attempt to process them and Dead Letter them immediately. One way to do this in the specific example is with a typed exception handler like this:
        ``` 
        try
        {
            // all the same code to process a message
        }
        catch (JsonException) // poison message
        {
            await args.DeadLetterMessageAsync(args.Message);
        }
        ```
    * Now you can eliminate this kind of Poison Message more efficiently.

* 2. Transient Error Conditions
    * Let's consider a different kind of error. Let's say that after deserializing the weather report, you need to write to SQL. That code might look like this:
        ```    
        WeatherData report = JsonSerializer.Deserialize<WeatherData>(json);
        using SqlConnection con = new(sqlConStr);
        await con.OpenAsync(); // what if SQL is down?
        await WriteToSql(con, report);
        ```
    * What happens if the SQL Server is unavailable for a while? every single message that comes through is going to fail and retry 10 times and then end up in the Dead Letter Queue.
    * In practical terms, you might suddenly find thousands or tens of thousands of messages piled up in the Dead Letter Queue just because you had a SQL Server service interruption.
    * This is a big mess. Your server was churning for hours doing nothing but logging errors, and now you have a pile of viable messages to somehow replay.
    * The trick to handling these kinds of transient error conditions is to implement a “stand-off and try later” approach.
    * Once you know that SQL Server is unavailable, there's no point processing any more messages. You want to pause your service bus processor and only re-enable it once you have verified SQL availability:
        ``` 
        try
        {
            // all the same code to process a message
        }
        catch (SqlException)  // stand off and try later
        {
            await args.AbandonMessageAsync(args.Message);
            await PauseTheProcessor();
        }
        ```
    * The PauseTheProcessor might look something like this:
        ``` 
        async Task PauseTheProcessor()
        {
            await processor.StopProcessingAsync();
            while (true)
            {
                if (await TestTheSqlConnection())
                {
                    await processor.StartProcessingAsync();
                    break;
                }
                await Task.Delay(60000);
            }
        }
        ```
## Option	Method	Description
    1. Complete -	CompleteMessageAsync(msg); -	The message is removed from the queue. This is used for normal, successful message processing.
    2. Dead Letter - 	DeadLetterMessageAsync(msg); -	The message is removed from the queue and placed in a special Dead Letter Queue. This is generally used when a message is not processable. It's possible to "replay" messages in the Dead Letter queue using the Azure Portal or CLI.
    3. Abandon -	AbandonMessageAsync(msg); - 	The message is unlocked and its retry counter is incremented. This is used when the message might be able to be processed in the future, so you want to put it back in the queue and retry later. The number of allowed retries is configurable. When a message hits this maximum number of allowed deliveries, the next Abandon will send it to the Dead Letter Queue instead.
    4. Defer -	DeferMessageAsync(msg); -	The message technically stays in the main queue but is marked as deferred. In order to receive this message again in the future, you need to save its SequenceNumber and receive it using the special `ReceiveDeferredMessageAsync` method. This approach creates complex message ordering and access issues and should be used with caution.
