# Exploring LangChain: A Practical Approach to Language Models and Retrieval-Augmented Generation (RAG)

## LongChain Intro
* LangChain is a powerful framework for building applications that incorporate LLMs.
* It simplifies the process of embedding LLMs into complex workflows, enabling the creation of conversational agents, knowledge retrieval systems, automated pipelines, and other AI-driven applications.
* At its core, LangChain follows a modular design that allows developers to build “chains,” or sequences of actions, with customizable components like prompt templates, model settings, response parsing, and memory management.
* It also supports integration with external data sources such as document databases, search indices, APIs, and more—a feature commonly referred to as Retrieval-Augmented Generation (RAG).
* This flexibility empowers developers to create tailored solutions for diverse tasks, from customer support bots to data analysis tools that extract insights from extensive datasets.
* LangChain is a top choice for developers aiming to leverage LLMs for dynamic, data-driven, and interactive applications.
* It offers extensive customization options and is actively maintained to support the latest advancements in LLMs and AI.

## LangChain Example
1. Install libraries
```
pip install langchain
pip install langchain-openai
```
2. Using LLMs from OpenAI
```
import os

# replace with your own API Key
os.environ['OPENAI_API_KEY'] = "OpenAI API Key"
```

### Components In LangChain
* In a LangChain application, components are connected or “chained” to create complex workflows for NL processing.
* Each component in the chain serves a specific purpose, like prompting the model, managing memory, or processing outputs, and they pass information to each other to enable more sophisticated applications.
* By chaining these components, you can build systems that not only generate responses but also retrieve information, maintain conversational context, summarize content, and much more.
* This modular approach allows flexibility, letting you create pipelines that can adapt to various tasks and data inputs based on the needs of your application.
* Using the following components:
    * PromptTemplate: It helps create a structured template for the prompt. It allows you to specify placeholders that can be dynamically filled with specific inputs (like a question or context) each time the prompt is used. This makes it easier to standardize prompts while customizing them for each query.
    * ChatOpenAI: It interfaces with OpenAI's chat models, enabling the generation of responses based on the provided prompt and any contextual information. It acts as the core of the application, where the language model generates responses to the user's inputs.
    * StrOutputParser: It processes the raw output from the model into a usable format, such as extracting only the text content. It simplifies the response so that it can be easily displayed or further processed in your application.
* By chaining these components, you can build a streamlined flow from prompt creation to response parsing, providing a solid foundation for more advanced LangChain applications.
* <img width="450" height="100" alt="image" src="https://github.com/user-attachments/assets/bd72d3e1-bd7f-4214-8d22-139665bd13b4" />

3. Create the first component: PromptTemplate:
    * the PromptTemplate contains a string template that specifies the structure of the prompt, containing a Question field where {question} acts as a placeholder.
    * When the template is used, this placeholder will be replaced with the actual question input.

  ```
    from langchain import PromptTemplate
    
    template = '''
    Question: {question}
    Answer: '''
    
    prompt = PromptTemplate(
        template = template,
        input_variables = ['question']
    )
  ```
   
4. Next component ChatOpenAI:
```
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model="gpt-4o-mini")
Here, you're making use of the “gpt-4o-mini” model from OpenAI.
```
5. 3rd component StrOutputParser:
   * It's used to handle the output from your model and parse it as a straightforward string.
   * This is useful when working with responses that don't require complex parsing or structuring.
   * StrOutputParser will ensure that the model's output is returned as a raw string.
```
from langchain_core.output_parsers \
import StrOutputParser

output_parser = StrOutputParser()
```
6. Chaining the Components into a single chain.
   * Allows a streamlined pipeline where you can input a question, have it processed through each component, and receive a parsed response.
   * the | operator is used to combine multiple components into a chain.
   * This “piping” operator allows you to create a seamless workflow where the output of one component is automatically passed as the input to the next.
``` 
# create the chain
chain = prompt | model | output_parser
```
7. Invoking the Chain
   * Call invoke() method and pass in a dictionary containing the question key and setting its value to the question.
   * The invoke() method returns the final processed output after it flows through each component in the chain.
   * This invoke() method simplifies the interaction with your LangChain application by handling all components in sequence and directly providing the final answer.
```
chain.invoke({"question": "Who is Steve Jobs?"})
```

* Complete Code with Azure OPEN AI
```
import os
from langchain_core.prompts import PromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import AzureChatOpenAI

azure_endpoint = "https://egptus2.openai.azure.com"
azure_deployment = "EGPT-4.1"
azure_api_key = "UM0NJQQJ9PP"
azure_api_version= "2024-09-01-preview"

# azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
# azure_api_key = os.getenv("AZURE_OPENAI_API_KEY")
# azure_api_version = os.getenv("AZURE_OPENAI_API_VERSION")
# azure_deployment = os.getenv("AZURE_OPENAI_DEPLOYMENT")

template = '''
Question: {question}
Answer: '''

prompt = PromptTemplate(
    template = template,
    input_variables = ['question']
)

model = AzureChatOpenAI(
    azure_endpoint=azure_endpoint,
    api_key=azure_api_key,
    api_version=azure_api_version,
    azure_deployment=azure_deployment,
    temperature=0,
)

output_parser = StrOutputParser()

chain = prompt | model | output_parser

response = chain.invoke({"question": "Who is Steve Jobs?"})
print(response)

response= chain.invoke({"question":  "What company did he found?"}) 
print(response)
```

### Maintaining Conversations with Memory
* ChatGPT understand and can handle follow-up questions seamlessly because it uses memory to keep track of the conversation.
* For example, after asking, “Who is Steve Jobs?” follow up with, “Companies he founded?” ChatGPT understands that “he” refers to Steve Jobs and can provide relevant information.
* <img width="200" height="100" alt="image" src="https://github.com/user-attachments/assets/5c20b85e-9899-4159-a07b-333ab524139d" />
* To maintain a conversation with the model, use the ConversationBufferMemory component in LangChain.
   * The ConversationBufferMemory component helps store the ongoing conversation's context, allowing the model to remember previous inputs and responses.
   * This memory buffer enables the model to refer back to earlier parts of the conversation, making follow-up questions and references more coherent.

8. To maintain a conversation effectively, modify the prompt template to include two placeholders: one for the conversation history and one for the current question.
```
# Define the prompt template
template = '''
Previous conversation: {history}
Question: {question}
Answer: '''

# Create the PromptTemplate with history
prompt = PromptTemplate(template = template,
    input_variables = ['history', 'question']
)
```
9. To store the history of the conversation, create an instance of the ConversationBufferMemory class:
```
# Set up conversational memory
memory = ConversationBufferMemory()
```
10. To retrieve the history of the conversation whenever you ask the model a question, you can use the following statement:
``` 
memory.load_memory_variables({})["history"]}
```
   * Here's how the above statement works:
      * memory.load_memory_variables({}): It retrieves the current memory variables stored in the ConversationBufferMemory.
      * By passing an empty dictionary, you are requesting all memory variables without any filters.
      * [“history”]: This accesses the specific history variable from the retrieved memory. It provides the entire context of the conversation up to that point.
11. So now when you ask a question, you create a dictionary with two keys: question and history:
   * Whenever you ask a question, you are also passing back the history of the conversation to the model so that it can provide the context for the current question.
```
response = chain.invoke(
    {"question" : question, 
      "history" : memory.load_memory_variables({})["history"]} )
print(response)
```
12. When the model returns a response, save the context to the ConversationBufferMemory instance using the save_context() method. It allows to store both the question and the answer
```
memory.save_context(
    {"question": question}, 
    {"answer": response} )
```

### Complete code with Azure OPEN AI
```
import os
from langchain_classic.prompts import PromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import AzureChatOpenAI
from langchain_classic.memory import ConversationBufferMemory

azure_endpoint = "https://egptus2.openai.azure.com"
azure_deployment = "EGPT-4.1"
azure_api_key = "UM0NJQQJ9PP"
azure_api_version= "2024-09-01-preview"

# azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
# azure_api_key = os.getenv("AZURE_OPENAI_API_KEY")
# azure_api_version = os.getenv("AZURE_OPENAI_API_VERSION", "2024-09-01-preview")
# azure_deployment = os.getenv("AZURE_OPENAI_DEPLOYMENT", "EnservioGPT-4.1")

# Define the prompt template
template = '''
Previous conversation: {history}
Question: {question}
Answer: '''

# Create the PromptTemplate with history
prompt = PromptTemplate(template = template,
    input_variables = ['history', 'question']
)

# Set up conversational memory
memory = ConversationBufferMemory()

memory.load_memory_variables({})["history"]

model = AzureChatOpenAI(
    azure_endpoint=azure_endpoint,
    api_key=azure_api_key,
    api_version=azure_api_version,
    azure_deployment=azure_deployment,
    temperature=0,
)

output_parser = StrOutputParser()

chain = prompt | model | output_parser

# Invoke the chain with a question and the memory 
# will track history
question = "Who is Steve Jobs?"
response = chain.invoke({"question" : question, 
     "history" : memory.load_memory_variables({})["history"]})
print(response)

memory.save_context({"question": question}, {"answer": response} )

# Ask another question to continue the conversation
question = "What company did he found?"
response = chain.invoke({"question": question,
                        "history": memory.load_memory_variables(
                          {})["history"]})
print(response)

memory.save_context({"question": question}, {"answer": response})
```

### Complete Code that can maintain a conversation with the model.
```
from langchain.memory import ConversationBufferMemory
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI
from langchain import PromptTemplate

# Define the prompt template
template = '''
Previous conversation:
{history}
Question: {question}
Answer: '''

# Create the PromptTemplate with history
prompt = PromptTemplate(
    template = template,
    input_variables = ['history', 'question']
)

# Set up conversational memory
memory = ConversationBufferMemory()

chain = prompt | \
        ChatOpenAI(model="gpt-4o-mini") | \
        StrOutputParser()

# Invoke the chain with a question and the memory 
# will track history
question = "Who is Steve Jobs?"
response = chain.invoke({"question" : question, 
     "history" : memory.load_memory_variables({})["history"]})
print(response)

memory.save_context(
    {"question": question}, 
{"answer": response} )

# Ask another question to continue the conversation
question = "What company did he found?"
response = chain.invoke({"question": question,
                        "history": memory.load_memory_variables(
                          {})["history"]})
print(response)

memory.save_context({"question": question}, 
                    {"answer": response})
```
* The ConversationBufferMemory class provides two ways to access the chat history:
   * memory.load_memory_variables({})[“history”] provides a formatted and concise view of the conversation history, ideal for use in prompts.
   * memory.chat_memory.messages gives direct access to the raw messages in a structured format, suitable for deeper inspection or manipulation.
      * Observe that each question contains two objects: HumanMessage (question asked by the user) and AIMessage (response from the model).
     ```
     [HumanMessage(content='Who is Steve Jobs?'),
     AIMessage(content='Steve Jobs was an American entrepreneur, ''' today.'),

      HumanMessage(content='What company did he found?'),
      AIMessage(content='Steve Jobs co-founded Apple Inc. in 1976.')]
     ```
     
## Sticking within the LLM Context Size
* using a ConversationBufferMemory object, there is one potential problem: memory overload or context length limitations.
* Most LMs (including the GPT), have a maximum token limit for the input they can process at one time.
* If the conversation history becomes too lengthy, you may exceed this token limit.
* Context grows, the computational load increases. This can lead to slower responses and increased resource consumption, affecting the performance of your application.
* Couple of techniques to prevent context length limitations. Here are the two most common ways to resolve this issue:
    * 1st Approach: Truncating the chat history: Limit the conversation history to the most recent exchanges.
    * 2nd Approach: Summarizing the past interactions: Instead of including the full conversations in the past, summarize them to keep the context.
* 1st approach, send in only the most recent two messages by filtering the last two messages, like this:
   * Each conversation has two components HumanMessage and AIMessage. Hence you need to multiply by two in the above statement.
      ```
      response = chain.invoke(
          {"question": question,
           "history" : memory.chat_memory.messages[-2 * 2:]})
      ```
* 2nd approach, the idea is that once the conversation history becomes too lengthy, you should summarize the previous interactions into a more concise format.
     * This helps manage memory usage and maintain a relevant context without overwhelming the model with excessive detail.
     * Use of the Hugging Face transformers' pipeline (``` !pip install transformers ```) object to perform the summarization
     * "summarization" is not working, facebook removed use different text
     ```
         from transformers import pipeline
             
         def summarize_history():
             long_history = memory.load_memory_variables({})["history"]
         
             # load the model to perform summarization
             summarizer = pipeline("summarization",  model="facebook/bart-large-cnn")
             summary = summarizer(long_history, max_length=150,  min_length=30,  do_sample=False)
         
             # clear the memory after summarizing
             memory.clear()
         
             # Save summarized context
             memory.save_context(
                 { "summary": summary[0]['summary_text'] },
                 { "answer": "" })
      ```
13. Code to summarize the chat history if it has more than four history entries
```
while True:
    question = input('Question: ')
    if question.lower() == 'quit': break
    # Invoke the chain with a question and the
    # memory will track history    
    response = chain.invoke(
      {"question" : question, 
       "history" : memory.load_memory_variables({})["history"]})
    print(response)
    
    memory.save_context({"question": question}, 
                        {"answer": response})
    
    # if more then 4 messages, summarize the history
    if len(memory.chat_memory.messages) > 8: summarize_history()
```

## Asking Multiple Questions
* The invoke() method allows you to pass a question to the chain.
* Instead of passing this method a single dictionary, you can pass it a list of dictionaries if you want to ask multiple questions in one go.
```
from langchain import PromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers \
import StrOutputParser

template = '''
Question: {question}
Answer: '''

# create the three components
prompt = PromptTemplate(
    template = template,
    input_variables = ['question'])
model = ChatOpenAI(model="gpt-4o-mini")
output_parser = StrOutputParser()

# create the chain
chain = prompt | model | output_parser

# set fof questions to ask
qs = [
    {'question': 'What is the population of Singapore?'},
    {'question': 'Which comes first? Egg or Chicken?'},
]

# ask multiple qns
res = chain.invoke(qs)
print(res)
```

## Prompt for Language Translation
* Enhance the functionality of the prompt template to facilitate task-oriented requests.
* Modify the prompt to instruct the LLM to perform specific tasks, such as translating a sentence from one language to another.
* Create a translation chain, which integrates a prompt template, a language model, and an output parser to facilitate translating sentences between languages.
```
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI
from langchain import PromptTemplate

template = '''
Translate the following sentence from {source_language} to 
{target_language}:{sentence}
Translation:
'''

prompt = PromptTemplate(template = template,
    input_variables = ['source_language','target_language', 'sentence']
)

chain = prompt |  ChatOpenAI(model="gpt-4o-mini") | StrOutputParser()

chain.invoke(
    {
        'source_language':'English',
        'target_language':'Chinese',
        'sentence':'How are you'
    })
```
## Exploring Hugging Face - Alternatives to OpenAI LLMs
* Used OpenAI's LLMs, it deliver excellent results, they also involve operational expenses.
* Hugging Face, which can provide similar capabilities without the associated costs.
```
!pip install langchain_community
!pip install langchain-huggingface
```
* Use the tiiuae/falcon-7b-instruct model to answer questions, featuring approximately 7 billion parameters designed for instruction-based tasks.
* It suitable for various natural language processing applications such as answering questions, generating text, summarizing content, and engaging in dialogue.
```
import os
from langchain import PromptTemplate
from langchain_huggingface import HuggingFaceEndpoint
from langchain_core.output_parsers import StrOutputParser

os.environ['HUGGINGFACEHUB_API_TOKEN'] = 'Hugging Face token'

template = '''
Question: {question}
Answer: 
'''

prompt = PromptTemplate(template = template, 
                        input_variables = ['question']
)

hub_llm = HuggingFaceEndpoint(repo_id = 'tiiuae/falcon-7b-instruct',
    # lower temperature makes the output more deterministic
    temperature = 0.1
)

chain = prompt | hub_llm | StrOutputParser()
chain.invoke({"question": "Who is Steve Jobs?"})
```

## Implementing Retrieval-Augmented Generation (RAG) with LangChain
* RAG is a robust technique in natural language processing that synergizes the retrieval of relevant information with the generation of contextually appropriate responses.
* This combination enhances tasks such as question answering, dialogue generation, and content creation, allowing organizations to deliver more accurate and pertinent answers to user queries.
* In the previous examples, the LLMs used were pre-trained on a static dataset, which restricts their knowledge to the information available at the time of training.
* This limitation can hinder their ability to provide up-to-date or specific answers, especially when dealing with rapidly changing information or niche topics.
* RAG addresses this challenge by integrating real-time data retrieval, enabling models to access and incorporate fresh information into their responses.

* In a real-world scenario, this example could be expanded to handle documents stored in various formats, such as PDF, Word, or plain text.
* By integrating document loaders and retrieval mechanisms, the application could process and extract relevant information from these files, enabling the language model to answer questions based on a much broader range of sources.
* This extension makes the RAG approach especially useful for applications in knowledge management, customer support, and research, where information often exists in diverse document formats.
1. Install Library
```
!pip install langchain docarray tiktoken
```
2. import the relevant modules
```
from langchain_community.vectorstores import DocArrayInMemorySearch
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnableParallel, RunnablePassthrough
from langchain_openai import OpenAIEmbeddings
from langchain_openai import ChatOpenAI
import os

os.environ['OPENAI_API_KEY'] = "OpenAI API Key"
```
### Defining the Text 
3. Use the below diabletes paragraph as the reference content for the RAG application, allowing the model to answer questions based on this specific text.
```
text = '''
Diabetes mellitus is a chronic metabolic disorder 
'''
ssociated with the disease.
'''
```
### Steps to Performing RAG 
   * To get a LLM to answer questions based on a specific document, follow these steps:
   * Perform word vector embeddings on the document: Word vector embeddings convert the document text into a numerical representation that captures the semantic meaning of the words and sentences.
        * This allows the model to understand relationships between concepts in the document, making it easier to retrieve relevant information.
        * Each sentence or passage is transformed into a vector, which helps locate information relevant to the question.
   * Store the embeddings in a vector database: After creating embeddings for each segment of the document, store them in a vector database (e.g., Pinecone, Weaviate, or FAISS).
        * This database indexes the embeddings, enabling quick and efficient retrieval based on semantic similarity.
   * Retrieve relevant document sections: Search the vector database for sections of the document with embeddings similar to the question's embedding.
        * This retrieves the most contextually relevant parts of the document.
   * Pass retrieved sections to the LLM for answer generation: Finally, pass the retrieved sections along with the question to the LLM.
        * By doing this, the model can generate an informed answer based on the content of the document rather than relying solely on its pre-trained knowledge.
   * <img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/40d19c41-8a43-477e-96fd-1e21c706e345" />

### Chunking with Overlaps
   * Before performing word vector embeddings, it's important to break down the documents into chunks because of:
   * Context Size Limits: LLMs and vector databases have constraints on the length of text they can process at once.
         * Chunking ensures that each segment stays within these limits, preventing issues with model input size and vector database compatibility.
   * Improved Semantic Representation: Smaller, focused chunks allow for more precise embeddings that capture specific meanings, enhancing the accuracy of similarity searches.
         * Larger segments may combine too many ideas, making it difficult for the model to retrieve the most relevant information.
   * Efficient Retrieval: Chunking enables a more targeted retrieval process.
         * When a user asks a question, smaller segments can be selectively retrieved based on relevance.
         * This makes retrieval faster and prevents overwhelming the model with unnecessary information.
   
   * <img width="600" height="250" alt="image" src="https://github.com/user-attachments/assets/2a93711b-7d3a-4313-8a52-4986918b0418" />
   * Document is typically broken down into chunks with overlap, which involves dividing a block of text into segments that share a portion of their content.
   * This method is particularly beneficial for maintaining context across adjacent chunks, ensuring that critical information is not lost during processing.
   * By including overlapping sections, the model can better understand relationships between sentences and provide more accurate responses to queries, especially when dealing with complex topics that span multiple segments.

4. Splits a block of text into a specific chunk size with a specific number of sentence to overlap.
```
def split_text_with_overlap(text, chunk_size, overlap_size):
    # Split the text into sentences
    sentences = text.split('. ')
    chunks = []
    current_chunk = ""

    for sentence in sentences:
        # Check if adding this sentence exceeds the chunk size
        if len(current_chunk) + len(sentence) + 1 <= chunk_size:
            if current_chunk:  # If it's not the first sentence
                current_chunk += ". "
            current_chunk += sentence
        else:
            # Store the current chunk
            chunks.append(current_chunk.strip())
            # Create a new chunk with the overlap
            # Add the last `overlap_size` sentences from 
            # the current chunk
            overlap_sentences = \
                current_chunk.split('. ')[-overlap_size:]
            current_chunk = '. '.join(overlap_sentences) + \
                            ". " + sentence

    # Add any remaining chunk
    if current_chunk:
        chunks.append(current_chunk.strip())

    return chunks

# Define chunk size and overlap size
chunk_size = 300  
overlap_size = 1  # Number of sentences to overlap

# Split the text into chunks with overlap
text_chunks = split_text_with_overlap(
                  text, chunk_size, overlap_size)

# Print the resulting chunks
for i, chunk in enumerate(text_chunks):
    print(f"Chunk {i+1}:\n{chunk}\n")
```
   * <img width="250" height="400" alt="image" src="https://github.com/user-attachments/assets/15a135f9-db33-4d9e-8c30-f42fb0f56bac" />
5. DocArrayInMemorySearch class to store document embeddings in memory for efficient similarity search:
``` 
# creates an DocArrayInMemorySearch store and 
# insert data
vectorstore = DocArrayInMemorySearch.from_texts(
    text_chunks,
    embedding = OpenAIEmbeddings(),
)
```
### Use the chunks and perform word vector embeddings using the OpenAIEmbeddings class.
   * OpenAIEmbeddings is a class provided by LangChain that allows you to generate vector embeddings for text using OpenAI's models.
   * These embeddings are vector representations that capture the semantic meaning of the text, enabling efficient similarity searches, document retrieval, and other natural language processing (NLP) tasks where understanding the meaning of text is crucial.

### Creating a Retriever Object
6. Convert the vector store into a retriever object, which can be used to search and retrieve relevant documents based on a query:
   * A retriever object is a component designed to fetch relevant information from a dataset, document collection, or knowledge base based on a given query or context.
```
retriever = vectorstore.as_retriever()
```
7. Create a LangChain application using the following components: RunnableParallel, PromptTemplate, ChatOpenAI, StrOutputParser
```
template = """Answer the question based only on the 
following context: {context}
Question: {question}
"""

# uses a model from OpenAI
model = ChatOpenAI(model = "gpt-4o-mini")

# creates the prompt
prompt = ChatPromptTemplate.from_template(template)

# creats the output parser
output_parser = StrOutputParser()

# RunnableParallel is used to run multiple processes or operations in parallel
setup_and_retrieval = RunnableParallel(
    { 
        "context": retriever, 
        "question": RunnablePassthrough()
    }
)

# creating the chain
chain = setup_and_retrieval | prompt | model | output_parser
```
   * The component of interest here is the RunnableParallel component. RunnableParallel is a class in LangChain that allows you to execute multiple tasks or operations in parallel:
   * In this implementation, the setup_and_retrieval object is designed to handle two parallel tasks: retrieving context from a retriever and passing through a question without any modifications.
   ```
   setup_and_retrieval = RunnableParallel(
       { 
           "context": retriever, 
           "question": RunnablePassthrough()
       }
   )
   ```
8. start asking questions pertaining to the block of text:
```
chain.invoke('What is Type 2 diabetes?')
chain.invoke('What causes diabetes?')
```
### Complete code with aZURE OPENAI
```
from langchain_openai import AzureChatOpenAI
from langchain_community.vectorstores import DocArrayInMemorySearch
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import  ChatPromptTemplate
from langchain_core.runnables import RunnableParallel, RunnablePassthrough
from langchain_openai import  AzureOpenAIEmbeddings

text = '''
Diabetes mellitus is a chronic metabolic disorder 
characterized by high blood sugar levels, which can lead
to serious health complications if not effectively 
managed. There are two primary types of diabetes: Type 
1 diabetes, which is an autoimmune condition where the 
immune system mistakenly attacks insulin-producing beta 
cells in the pancreas, leading to little or no insulin 
production; and Type 2 diabetes, which is often 
associated with insulin resistance and is more prevalent 
in adults, though increasingly observed in children and 
adolescents due to rising obesity rates. Risk factors 
for developing Type 2 diabetes include genetic 
predisposition, sedentary lifestyle, poor dietary 
choices, and obesity, particularly visceral fat that 
contributes to insulin resistance. When blood sugar 
levels remain elevated over time, they can cause damage 
to various organs and systems, increasing the risk of 
cardiovascular diseases, neuropathy, nephropathy, and 
retinopathy, among other complications. Management of 
diabetes requires a multifaceted approach, which 
includes regular monitoring of blood glucose levels, 
adherence to a balanced diet rich in whole grains, 
fruits, vegetables, and lean proteins, and engaging in 
regular physical activity. In addition to lifestyle 
modifications, many individuals with Type 2 diabetes 
may require oral medications or insulin therapy to help 
regulate their blood sugar levels. Education about the 
condition is crucial, as it empowers individuals to make 
informed decisions regarding their health. Furthermore, 
the role of technology in diabetes management has grown 
significantly, with continuous glucose monitors and 
insulin pumps providing real-time feedback and improving 
the quality of life for many patients. As research 
continues to advance, emerging therapies such as 
glucagon-like peptide-1 (GLP-1) receptor agonists and 
sodium-glucose cotransporter-2 (SGLT2) inhibitors are 
being explored for their potential to enhance glycemic 
control and reduce cardiovascular risk. Overall, with 
appropriate management strategies and support, 
individuals living with diabetes can lead fulfilling 
lives while minimizing the risk of complications 
associated with the disease.
'''
    
azure_endpoint = "https://egptus2.openai.azure.com"
azure_api_key = "UM0NJQQJ9PP"
azure_embedding_deployment = "text-embedding-3-small"
azure_embedding_api_version= "2024-12-01-preview"
azure_chat_deployment = "EGPT-4.1"
azure_chat_api_version= "2024-09-01-preview"

def split_text_with_overlap(text, chunk_size, overlap_size):
    # Split the text into sentences
    sentences = text.split('. ')
    chunks = []
    current_chunk = ""

    for sentence in sentences:
        # Check if adding this sentence exceeds the chunk size
        if len(current_chunk) + len(sentence) + 1 <= chunk_size:
            if current_chunk:  # If it's not the first sentence
                current_chunk += ". "
            current_chunk += sentence
        else:
            # Store the current chunk
            chunks.append(current_chunk.strip())
            # Create a new chunk with the overlap
            # Add the last `overlap_size` sentences from 
            # the current chunk
            overlap_sentences = current_chunk.split('. ')[-overlap_size:]
            current_chunk = '. '.join(overlap_sentences) +  ". " + sentence

    # Add any remaining chunk
    if current_chunk:
        chunks.append(current_chunk.strip())

    return chunks

# Define chunk size and overlap size
chunk_size = 300  
overlap_size = 1  # Number of sentences to overlap

# Split the text into chunks with overlap
text_chunks = split_text_with_overlap( text, chunk_size, overlap_size)

# Print the resulting chunks
for i, chunk in enumerate(text_chunks):
    print(f"Chunk {i+1}:\n{chunk}\n")

# creates an DocArrayInMemorySearch store and 
# insert data
vectorstore = DocArrayInMemorySearch.from_texts(text_chunks,
    embedding = AzureOpenAIEmbeddings (
        azure_endpoint=azure_endpoint,
        api_key=azure_api_key,
        api_version=azure_embedding_api_version,
        azure_deployment=azure_embedding_deployment,
    ),
)

retriever = vectorstore.as_retriever()

template = """Answer the question based only on the 
following context: {context}
Question: {question}
"""

model = AzureChatOpenAI(
    azure_endpoint=azure_endpoint,
    api_key=azure_api_key,
    api_version=azure_chat_api_version,
    azure_deployment=azure_chat_deployment,
    temperature=0,
)

# creates the prompt
prompt = ChatPromptTemplate.from_template(template)

# creats the output parser
output_parser = StrOutputParser()

# RunnableParallel is used to run multiple processes or
# operations in parallel
setup_and_retrieval = RunnableParallel(
    { 
        "context": retriever, 
        "question": RunnablePassthrough()
    }
)

# creating the chain
chain = setup_and_retrieval | prompt | model | output_parser

resp = chain.invoke('What is Type 2 diabetes?')
print(resp)

resp = chain.invoke('What causes diabetes?')
print(resp)
```
## Changing the Embedding Model
* Word vector embeddings: OpenAI's models have been employed to convert text documents into numerical representations (embeddings) that capture the semantic meaning of the text. This allows you to effectively compare and retrieve relevant information based on user queries.
* LLM: used OpenAI's LLM to generate responses and answer questions based on the retrieved context. This model leverages its training on vast amounts of text to provide coherent and contextually appropriate answers.
* OpenAI's models are efficient, but concerning data privacy, This is because of:
   * Data transmission: Queries and documents must be transmitted over the internet to OpenAI's servers, which could expose them to interception or unauthorized access.
   * Data storage: Depending on the terms of service, the data you send might be stored by OpenAI for training or improvement purposes, which raises concerns about how that data is used and who has access to it.
   * Compliance: Organizations handling sensitive information, especially in regulated industries, may face compliance challenges when using cloud-based solutions, as they need to ensure that they meet data protection regulations.
* For those concerned about privacy, alternative solutions, such as local or self-hosted models (like those from Hugging Face), can be considered.
* These models allow you to maintain control over your data, ensuring that sensitive information remains within your own infrastructure.
* Use Hugging Face,  “BAAI/bge-small-en-v1.5” model for embedding:
   * The model is a pre-trained LM developed by the BAAI (Beijing Academy of Artificial Intelligence).
   * It's part of the BGE (BERT-based generative embedding) series and is designed for various NL processing tasks, including embedding generation.
```
from langchain.embeddings import HuggingFaceEmbeddings
embedding_model = HuggingFaceEmbeddings(model_name="BAAI/bge-small-en-v1.5")
 
from langchain_community.vectorstores import DocArrayInMemorySearch

vectorstore = DocArrayInMemorySearch.from_texts(text_chunks,
    embedding = embedding_model,
)

retriever = vectorstore.as_retriever()
```
* Use a LLM from Hugging Face to perform the response generation. This allows you to keep the entire pipeline local or within the Hugging Face ecosystem, enhancing data privacy and reducing dependency on external APIs.
* Use of the facebook/bart-large model via the pipeline object in the transformers library:
``` 
# Load a Hugging Face pipeline for text generation
generator = pipeline('text2text-generation', 
                     model='facebook/bart-large',
                     max_length=500,
                     device=device) 

# Create a LangChain LLM wrapper
model = HuggingFacePipeline(pipeline=generator)
```

```
# Changing the LLM to a model hosted by Hugging Face
from langchain_core.runnables import RunnableParallel, RunnablePassthrough
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain.llms import HuggingFacePipeline
from transformers import pipeline
import torch

# determine the device
if torch.backends.mps.is_available(): device = torch.device("mps")
else:
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

# Load a Hugging Face pipeline for text generation
generator = pipeline('text2text-generation', 
                     model='facebook/bart-large',
                     max_length=500,
                     device=device) 

# Create a LangChain LLM wrapper
model = HuggingFacePipeline(pipeline=generator)

template = """Answer the question based only on the 
following context: {context}
Question: {question}
"""

# creates the prompt
prompt = ChatPromptTemplate.from_template(template)

# creates the output parser
output_parser = StrOutputParser()

setup_and_retrieval = RunnableParallel(
    { 
        "context": retriever, 
        "question": RunnablePassthrough()
    }
)

# creating the chain
chain = setup_and_retrieval | prompt | model | output_parser
```
* Processing to the GPU (for Windows; if you have a supported NVIDIA GPU) or MPS (if you have an Apple Silicon Mac):
``` 
# determine the device
if torch.backends.mps.is_available():
    # for Apple Silicon Mac
    device = torch.device("mps")  
else:
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

# Load a Hugging Face pipeline for text generation
generator = pipeline('text2text-generation', 
                     model='facebook/bart-large',
                     max_length=500,
                     device=device) 
```
* Ask a question where inference happens locally on your computer. This set up ensures that both the embedding retrieval and the LLM processing occur on your own hardware, reducing reliance on external servers and improving data privacy:
``` 
chain.invoke('What is diabetes?')
```
* The output from Hugging Face models can vary significantly based on several factors, such as model type, configuration, and input parameters.
   * For example, generation models may produce different styles or lengths of responses based on settings like temperature, max_length, or top_k/top_p sampling parameters.
   * Tweak these settings or use output parsers to ensure consistency in responses, especially in tasks like question answering or summarization, where stable and contextually relevant outputs are important.
to implementing Retrieval-Augmented Generation (RAG) for document-based querying. With clear steps on chunking, creating retriever objects, and customizing embeddings, I hope this article provides you with a solid starting point for using LangChain in various NLP tasks.
