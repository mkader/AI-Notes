# Running Large Language Models (LLMs) Locally Using Ollama
* LLMs are no longer limited to cloud environments.
* With Ollama tools, developers can run powerful LM directly on their own machines, gaining full control over data, costs, and performance.

### Run LLM Locally
* Running LLMs locally is offline access or low-latency responses, all without relying on external APIs.
* Data privacy is a key sensitive information, such as proprietary code, customer data, or internal documents, can be processed without leaving the local machine or network.
* Running LLMs locally gives developers finer control over model versions, updates, and performance tuning, allowing experimentation with different models and configurations without external constraints.
* These factors make local LLM deployment increasingly attractive for learning, prototyping, and building real-world AI applications.

### Run Cloud based Run LLM
* Cloud-based services (OpenAI) have made LLMs widely accessible, but they require sending prompts and data to external servers, which can raise concerns around privacy, compliance and data ownership.
* Cloud-based LLM services are convenient and scalable.
* Cloud APIs typically charge per token or request, which can quickly become expensive during experimentation, development, or high-volume usage. 

### Hardware Requirements for Running LLMs Locally
* Hardware depend on the size of the model, the precision used and the type of workload (experimentation, inference, or fine-tuning).
* At a minimum, a modern CPU with sufficient RAM can run smaller LLMs (such as 3B–7B parameter models), especially when they are quantized.
  * What Is a Quantized Model? - is a ML model whose numerical parameters (weights and sometimes activations) have been converted from high-precision representations—typically 32-bit floating point (FP32)—to lower-precision formats such as 16-bit (FP16), 8-bit (INT8), or even 4-bit (INT4).
* Roughlt a quantized 7B model typically requires 6–8 GB of RAM, while larger models scale up quickly beyond that.
* Solid-state storage (SSD) is also recommended to reduce model load times and improve overall responsiveness.
* For better performance, particularly lower latency and higher throughput, a GPU or Apple Silicon accelerator is highly desirable.
  * GPUs with sufficient VRAM can handle LMs and longer context windows more efficiently than CPUs.
  * On NVIDIA hardware, VRAM capacity often becomes the limiting factor,
  * on Apple Silicon (M-series chips), the unified memory architecture allows models to share memory efficiently between the CPU and GPU cores.
  * In practice, this makes Apple Silicon well-suited for running moderately sized LLMs locally.

### What Is Ollama?
* Ollama is a platform that provides local deployment and management of LLMs on your own machine.
* Ollama allows to run models locally, which means your data doesn't need to leave your device—useful for privacy, speed, and offline use.
* Ollama comes with two key components:
  * A desktop application that resembles ChatGPT, allowing you to chat and ask questions
  * A command line application (CLI) that you can use in Terminal (macOS) or Command Prompt (Windows)
* From Ollama.com, download (OllamaSetup.exe) and install Desktop.
   
### Finding Available Models
1. From Ollama.com or installed tool
2. Search the model (desktop or website) - llama3.2, it's relatively small (around 2GB).
   * llama3.2:latest – This is the same as the one listed as llama:3.2:3b. Default, if no tag specify (e.g. 1b or 3b)
   * llama3.2:1b – This model has 1 billion parameters, and its size is 1.3GB
   * llama3.2:3b – This model has 3 billion parameters, and its size is 2GB
   * llama3.2 model - summarize text, translate content, generate code samples, or answer questions based on a specific block of text.
3. Models with more parameters are often more capable, but they are larger and more computationally demanding.
4. Download and run automatically the model onto your computer ``` ollama run llama3.2 ```
   * <img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/f091839a-eba1-454b-a3c6-4950673998f4" /> 
   * <img width="500" height="125" alt="image" src="https://github.com/user-attachments/assets/f8e20754-f313-446c-91e2-c59ff7426f01" />

### Using the Ollama CLI
1. To start, you can check if Ollama is installed by running ``` ollama ```
   * <img width="300" height="200" alt="image" src="https://github.com/user-attachments/assets/77a02bbb-f4ef-42f1-bde5-ebb9cc3c5d48" />
2. See the list of available options ``` ollama help ```
   * <img width="350" height="300" alt="image" src="https://github.com/user-attachments/assets/324ce448-4434-4b34-9743-4a9613e627dd" />
3. Download a model without running it, use the pull command ``` ollama pull llama3.2 ```
4. Download and run automatically the model onto your computer ``` ollama run llama3.2 ```
5. Start chatting with the model.
   * When you are done, just type ``` /bye ``` to return to the Terminal.
   * <img width="200" height="30" alt="image" src="https://github.com/user-attachments/assets/5967937c-49f6-4688-9138-64d89b58ad81" />
   * <img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/2292fc56-7c71-415b-ba21-af9910f50742" />
6. View the list of downloaded models onto your computer ``` ollama list ```         
7. Remove a model ``` ollama rm llama3.2 ```
8. Start Ollama manually ``` ollama serve ```
   * By default, Ollama runs as a background service, listening on port 11434, which allows your applications to communicate with the models it hosts.
   * Error message, this means the Ollama backend is already running:
   * <img width="1198" height="56" alt="image" src="https://github.com/user-attachments/assets/4da6e293-db49-41b1-a09b-1f144f0a556d" />

### Generating Text Using the Ollama API
* Use curl or Postman to test it, sends a prompt “Tell me a joke” to the llama3.2 model:
  ```
    $ curl http://localhost:11434/api/generate -d '{
      "model": "llama3.2",
      "prompt": "Tell me a joke",
      "stream": false
    }'
  ```
   * <img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/36f6def5-acba-4e29-b095-321c779469cf" />
  ```
  {
      "model": "llama3.2",
      "created_at": "2026-03-11T16:34:24.0673662Z",
      "response": "Why don't eggs tell jokes?\n\nBecause they'd crack each other up!",
      "done": true,
      "done_reason": "stop",
      "context": [
          128006,
          9125,
          ...
          709,
          0
      ],
      "total_duration": 3762366300,
      "load_duration": 2528385500,
      "prompt_eval_count": 29,
      "prompt_eval_duration": 535122000,
      "eval_count": 16,
      "eval_duration": 688958700
  }
  ```
* Set the stream parameter to true (or simply leave it out altogether)
  * This streaming format allows your application to receive partial tokens as they are generated, enabling real-time display of the model's output rather than waiting for the full response to complete.
  * the responses:
   * <img width="200" height="400" alt="image" src="https://github.com/user-attachments/assets/26bf992f-b1ca-496d-95b5-d135ac078c68" />
  ```
    {
        "model": "llama3.2",
        "created_at": "2026-03-11T16:39:47.5727622Z",
        "response": "Here",
        "done": false
    }
    {
        "model": "llama3.2",
        "created_at": "2026-03-11T16:39:47.6180924Z",
        "response": "'s",
        "done": false
    }
    {
        "model": "llama3.2",
        "created_at": "2026-03-11T16:39:47.667321Z",
        "response": " a",
        "done": false
    }
    ...
    {
        "model": "llama3.2",
        "created_at": "2026-03-11T16:39:48.4639517Z",
        "response": ".",
        "done": false
    }
    {
        "model": "llama3.2",
        "created_at": "2026-03-11T16:39:48.5091369Z",
        "response": "",
        "done": true,
        "done_reason": "stop",
        "context": [
            128006,
  ```
 
## Running Models on the Cloud
* Ollama is too large to fit within your computer's available memory?
* OpenAI gpt-oss Model, which was designed for powerful reasoning, agentic tasks, and versatile developer use cases
  * 2 main variants:
     * gpt-oss:20b – 14GB in size
     * gpt-oss:120b – 65GB in size
  * Most people with 16-24GB of RAM can run the 20b model, but the 120b variant is beyond the reach of most users.
  * 2 more variants:
    * gpt-oss:20b-cloud – this is the 20b model running on the cloud
    * gpt-oss:120b-cloud – this is the 120b model running on the cloud
  * These two variants let you run the models on Ollama.com's servers rather than on your local machine.
  * <img width="400" height="450" alt="image" src="https://github.com/user-attachments/assets/c8cafd20-9843-4ef8-8b5f-8a6767335565" />
* Not all models are supported on Ollama's cloud.
* Only models with the -cloud suffix can be run on the cloud; all others are local-only.
* Smaller models, such as 3B-parameter versions, are designed to run locally on your machine, allowing for full control over data and privacy.
* Larger or optimized models may offer cloud variants to reduce hardware requirements and improve performance, but these come with the trade-off of sending data over the internet.
* Running models on Ollama's cloud means your data is no longer fully private, but it allows you to access much more powerful models than you could run locally.

### Accessing Ollama from the Network
1. By default, Ollama can only be accessed on the computer where it is installed.
1. To allow other machines on your local network to connect, you need to bind it to all network interfaces by setting the environment variable OLLAMA_HOST to 0.0.0.0:11434.
1. After making this change, restart the Ollama server.
1. Once restarted, the Ollama server will be accessible to other devices on the same subnet.

### To run an Ollama model on the cloud, follow the steps outlined here.
* Running Ollama's model in the cloud, your data is sent to a third party.
* Choose a model. ``` ollama run gpt-oss:120b-cloud ```
   * if error, signin first
   * <img width="300" height="100" alt="image" src="https://github.com/user-attachments/assets/b10677f0-383d-4deb-b1b2-6ab7b52ce164" />
* Signin ``` ollama signin ``` or signin by link
   * <img width="578" height="202" alt="image" src="https://github.com/user-attachments/assets/7e954744-a9fc-4b13-b2b1-4c33d997c4f8" />
* Once connected, you can now run the model on the cloud
   * <img width="300" height="200" alt="image" src="https://github.com/user-attachments/assets/25c46a68-0a50-40f7-9857-e5fb408847fa" />

## Using the Ollama Desktop App
* For non-technical users, the Ollama desktop app provides a much easier way to interact with Ollama.
   * <img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/9b36156a-fa72-453c-8aae-1e0bd7dc2b4f" />
* Simple conversation with the gpt-oss:120b-cloud model using the Ollama desktop app.
   * <img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/69139e7e-e0f0-477a-a87d-56561f36958c" />
* Select the model's response length:
   * Short – brief, concise answers
   * Medium – balanced detail (default)
   * Long – more detailed, verbose responses
* Upload image using "+ icon", if the model supports image input.

## Using Hugging Face Models in Ollama
* Run a specific model (not available on Ollama.com) from Hugging Face in Ollama.
* Fortunately, Ollama supports running Hugging Face models that are in the GGUF format.
* 2 ways to run Hugging Face models in Ollama

### What Is GGUF (GPT-Generated Unified Format)?
* GGUF is a binary file format for LLMs that is designed to make models efficient, portable, and easy to run locally, especially on consumer hardware.
* It is most commonly used with inference engines like llama.cpp, Ollama, LM Studio, and similar tools.
* GGUF is a way to package a language model's weights and metadata so that it can be loaded quickly, use less memory, and support features like quantization (e.g., 4-bit, 5-bit, or 8-bit weights) to dramatically reduce model size while still retaining good performance.
* Unlike older formats (such as GGML), GGUF stores rich metadata inside the file itself—including tokenizer info, architecture details, and special tokens—making models more self-contained and less error-prone to load.

### Method 1 — Run a Model Directly from Hugging Face
   * Find the model directly on Hugging Face's website (https://huggingface.co). Filter by “GGUF”.
   * <img width="600" height="450" alt="image" src="https://github.com/user-attachments/assets/6f0c3231-550f-4b08-8860-a24074ef4c07" />
2. Use the unsloth/QwQ-32B-GGUF model located at: https://huggingface.co/unsloth/QwQ-32B-GGUF
   * Since this model is in GGUF format, you can directly pull and run it using the ollama app in Terminal
3. Download and start chatting ``` ollama run huggingface.co/unsloth/QwQ-32B-GGUF ```

### Run Hugging Face quantized versions models
1. Some models on Hugging Face are available in quantized versions.
2. For instance, the unsloth/QwQ-32B-GGUF model offers multiple quantized variants. You can explore them on the model's page.
   * <img width="750" height="450" alt="image" src="https://github.com/user-attachments/assets/0eced311-5a49-4084-b6c3-32e41d6c8e07" />
3. Download and run a quantized model by appending the quantized variant name to the model name ``` ollama run huggingface.co/unsloth/QwQ-32B-GGUF:Q4_K_M ```
   * Running a quantized model reduces memory usage, enabling it to run efficiently on CPUs and low-VRAM GPUs while also improving inference speed.
   * This makes quantized models ideal for real-time applications, edge devices, and deployment on resource-constrained hardware.
   * They also consume less power, making them more energy-efficient and cost-effective for large-scale or battery-powered applications.
   * By reducing computational requirements, quantization allows users to work with large models without needing high-end hardware, making AI more accessible and practical for a wider range of use cases.

### Method 2 — Importing a Model with a Modelfile
* Modelfile to define and import a Hugging Face model into Ollama.
* A Modelfile lets you specify the model's source, system settings, and other configurations in a structured, easy-to-manage format.
* Download the model from Hugging Face in GGUF format, Locate the "File and versions" tab, like https://huggingface.co/unsloth/QwQ-32B-GGUF/tree/main
* For instance, download the QwQ-32B-Q4_K_M.gguf file by clicking the download icon.
* <img width="750" height="350" alt="image" src="https://github.com/user-attachments/assets/059b282c-fbab-4ce6-8270-9a8d1aed2b92" />
* Advantage: if a GGUF version of the model not available, you can still download the model from Hugging Face using the standard Python approach and then convert it to GGUF using llama.cpp. Refer to the section “Converting a Hugging Face Model to GGUF Format” for detailed instructions on how to do this.
* Once the GGUF file is downloaded, create a file named Modelfile (no extension) and populate it as follows:
  ```
    FROM ./downloads/QwQ-32B-Q4_K_M.gguf
    SYSTEM "You are a helpful AI assistant."
    PARAMETER temperature 0.7
  ```
* Ceate a new model in Ollama based on the configuration defined in the Modelfile ``` ollama create my-model -f Modelfile ```
* To confirm and see the model named my-model:latest   ``` ollama list ```
* Run: ``` ollama run my-model ```
  
## Where Are the Models Saved?
* Download a model using the pull command, it is stored by default in the following locations: ``` macOS: ~/.ollama ``` ``` Windows: C:\Users\<username>\.ollama\ ```
* Each downloaded model is split into multiple components to manage its files efficiently. The example below shows the contents of the ~/.ollama folder after downloading four different models:
   ```
   ~/.ollama
       |__models
            |__blobs
                  |__sha256-6e4c3...4a4e4
                  |__sha256-34bb5...e242b
                  |__sha256-38bad...3cdda
                  |__sha256-819c2...39c3d
                  |__ ....
                  |__ ....
            |__manifests
                  |__registry.ollama.ai
                         |__library
                               |__deepseek-r1
                                    |__1.5b
                                    |__7b
                               |__llama3.2
                                    |__latest
                               |__mxbai-embed-large
                                    |__latest
   ```
* Content of the latest file from the mxbai-embed-large model
  ```
  {
      "schemaVersion": 2,
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "config": {
          "mediaType": "application/vnd.docker.container.image.v1+json",
          "digest": "sha256:38bad...3cdda",
          "size": 408
      },
      "layers": [
          {
              "mediaType": "application/vnd.ollama.image.model",
              "digest": "sha256:819c2...39c3d",
              "size": 669603712
          },
          {
              "mediaType": "application/vnd.ollama.image.license",
              "digest": "sha256:c71d2...d0ab4",
              "size": 11357
          },
          {
              "mediaType": "application/vnd.ollama.image.params",
              "digest": "sha256:b8374...5d089",
              "size": 16
          }
      ]
  }
  ```
* Ollama employs the OCI (Open Container Initiative) image specification format, which Docker also uses, to distribute and manage its models.
* Latest file is a manifest file describing an OCI image, complete with layers, digests (SHA256 hashes), and media types.
* Layers key contains the model weights (application/vnd.ollama.image.model), licensing info (application/vnd.ollama.image.license), and parameters (application/vnd.ollama.image.params).
* These types are specified by the mediaType key.
* The digest key contains unique identifiers for each layer, ensuring integrity and reproducibility.
* The values of the digest key maps to the respective files in the blobs folder.
* For example, the value of the digest key in the config key maps to the sha256–38badd946f91096f47f2f84de521ca1ef8ba233625c312163d0ad9e9d253cdda file located in the blobs folder:
  ```
  {
      "config": {
          "mediaType": "application/vnd.docker.container.image.v1+json",
          "digest": "sha256:38bad...3cdda",
          "size": 408
      }
  }
   
  ~/.ollama
      |__models
           |__blobs
                 |__sha256-6e4c3...4a4e4
                 |__sha256-34bb5...e242b
                 |__sha256-38bad...3cdda
                 |__sha256-819c2...39c3d
                 |__ ....
  ```
* For the mxbai-embed-large model, it has a total of four files (each specified in the digest key within the config and layers keys) located in the blobs folder:
* Once you understand the models folder structure, it is now easy to move specific models from the original directory to a new one.
* Python utility (https://github.com/weimenglee/MigrateOllamaModels.) that helps users migrate local Ollama models from one folder to another.
* This is especially useful if you want to transfer models downloaded on another computer to your current system without having to redownload them.
  
## Changing the Model Locations
* Ollama stores downloaded models in a default directory.
* Ollama desktop app: Setting.  specify a new directory for storing your models.
   * <img width="400" height="80" alt="image" src="https://github.com/user-attachments/assets/a90bf096-c09f-4a66-b0ac-47a93169796c" />
* 2nd optin: to change the model directory via the Terminal (macOS) or Command Prompt (Windows).
   * <img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/91450432-e1e7-4acb-85c2-ebd02761590c" />
   *  First, uninstall Ollama. Then, create a new environment variable named OLLAMA_MODELS and set its value to the directory where you want your models to be stored.
   * Once set, reinstall Ollama, and it will use this directory for all downloaded models.
* With this setup, all Ollama models you download will be in the directory you specified in the environment variable.

## Using Prompts to Customize the Behavior of a Model
* Customize, use Modelfile - Shape a model's behavior, tailoring it to a specific role and save that configuration.
* A Modelfile in Ollama is a configuration file that lets you customize how a language model behaves.
* You can tweak its personality, set its knowledge base, adjust its tone, or even fine-tune its responses for specific tasks.
* Using a Modelfile to customize an LLM to consistently respond with sarcasm (file name mysarcasticmodelfile.) 
  ```
    FROM llama3.2
    SYSTEM "You are a sarcastic IT assistant who reluctantly helps users with 
            tech problems. Use dry humor and witty remarks, but always provide 
            accurate advice."
  ```
    * 1st line specifies the base model that you want to use
    * 2nd line is a system instruction, which defines the assistant's personality and behavior. It tells the model how to respond to user inputs.
* Create a new customized model ``` ollama create my_sarcastic_model -f Mysarcasticmodelfile ```
* Ollama is using the existing layers from the llama3.2 base model and adding a new layer to it.
* This will create a customized model based on the llama3.2 base model. 
* Run the created model ``` ollama run my_sarcastic_model ```

## RAG - Retrieval-Augmented Generation
* https://www.codemag.com/Article/2411031/AI-with-No-Internet-Connection

 <img width="827" height="538" alt="image" src="https://github.com/user-attachments/assets/01a9b5ec-5e70-45d9-803f-9375e2083234" />

* the standard llama3 and llama3.2 text models do not support embedding generation inside Ollama.
* RAG is the process of optimizing the output of a LLM, so it references an authoritative knowledge base outside of its training data sources before generating a response.

* LLMs have been trained on vast volumes of data and use billions of parameters to generate original output for tasks.
  * They can do many things, like answering questions, translating languages, and completing sentences.
  * But they can't understand your domain and your data out of the box.

```
requirements.txt 
 
langchain
langchain-core      
langchain-ollama
pypdf
docarray
chromadb

pip install -r requirements.txt
```

* 1. imports
``` 
index.py
 
from langchain_ollama import OllamaLLM,  OllamaEmbeddings
```
  * langchain_ollama - package establishes a commonality between several 3rd integrations via a common set of base interfaces.
    * I'm importing llms.OllamaLLM. I could easily import llms.OpenAI or llms.openai.AzureOpenAI,...
     
  * OllamaEmbeddings - Embeddings in AI is a way of representing high-dimensional data as a set of vectors in a lower-dimensional space.
    * These vectors, or embeddings capture the relationships between different data points in the original high-dimensional space.

2. uing this model and embeddings & run to see output
``` 
MODEL="llama3.2:latest"
model = OllamaLLM(model=MODEL)
embeddings = OllamaEmbeddings(model=MODEL)
 
out = model.invoke("What came first, chicken or egg? 
                    Give me a funny and short answer.")
print(out)
```
<img width="935" height="98" alt="image" src="https://github.com/user-attachments/assets/d98ab5c8-6e42-4a1e-af85-402bc0a7cbdf" />

* I want to always see the output as a string, so I can print it out.
```
from langchain_core.output_parsers import StrOutputParser 
 
parser = StrOutputParser()
chain = model | parser
out = chain.invoke("Give me some puns using the word egg")
print(out)
```
<img width="631" height="277" alt="image" src="https://github.com/user-attachments/assets/48b01925-cff8-453a-93ce-898f40254ad4" />

* Add a prompt, with the help of prompts, get the model to not use its existing knowledge, but instead provide it some context and the answer must be given based on that input context.
* If the model has no idea how to answer a given question based on the context given, the model should simply say “I don't know!”

```
template = """
Answer the question based on the context below. 
If you can't answer the question, say "I don't know".
Context: {context}
Question: {question}
"""

prompt = PromptTemplate.from_template(template)
prompt.format(context="Here is some context", 
   question="Here is a question")

parser = StrOutputParser()
chain = prompt | model | parser

out = chain.invoke({
    "context": "Glyphosates can often cause deadly  cancers which can kill people.",
    "question": "Who is Abraham Linclon?"
})

out = chain.invoke({
    "context": "Glyphosates can often cause deadly  cancers which can kill people.",
    "question": "What are Glyphosates?"
})

print(out)
```
<img width="577" height="226" alt="image" src="https://github.com/user-attachments/assets/4811d75a-b4c7-40a0-ae88-7a712a9d59ef" />
<img width="600" height="301" alt="image" src="https://github.com/user-attachments/assets/b18336a1-5331-4aeb-bbe5-4d7413714ee9" />

* Provide the last issue of CODE Magazine(PDF) as input to my model, ask questions.

```
not working

loader = PyPDFLoader("CodeMagJulAug2024.pdf")
pages = loader.load_and_split()
vectorstore = DocArrayInMemorySearch.from_documents(pages, embedding=embeddings)
retriever = vectorstore.as_retriever()

docs = retriever.invoke("programming")
print(docs)

template = """
Answer the question based on the context below. 
If you can't answer the question, say "I don't know".

Context: {context}
Question: {question}
"""
prompt = PromptTemplate.from_template(template)
parser = StrOutputParser()

chain = (
    {
        "context": itemgetter("question") | retriever,
        "question": itemgetter("question"),
    }
    | prompt     | model     | parser
)

exit = False
while not exit:
    question = input("Ask a question: ")
    if question == "bye":
        exit = True
    else:
        print(f"Answer: {chain.invoke({'question': question})}")

* PyPDFLoader to load up an input PDF and creating a vector store using an in-memory search object.
  * PyPDFLoader is one of the many document loaders available in langchain_community.
  * Each of these document loaders takes the task of converting a given input into documents that can be used to create a vector store.
  * explore the various other document loaders available in langchain_community.document_loaders.
```
```
working
from langchain_ollama import OllamaLLM,  OllamaEmbeddings
from langchain_core.documents import Document
from langchain_core.output_parsers import StrOutputParser 
from langchain_core.prompts import PromptTemplate
from langchain_core.vectorstores import InMemoryVectorStore
from operator import itemgetter
from pypdf import PdfReader

MODEL="llama3"
EMBEDDING_MODEL="embeddinggemma"
model = OllamaLLM(model=MODEL)
embeddings = OllamaEmbeddings(model=EMBEDDING_MODEL)

pdf_path = "24022_CODE_4-2024_Web.pdf"
reader = PdfReader(pdf_path)
pages = [
    Document(
        page_content=page.extract_text() or "",
        metadata={"source": pdf_path, "page": page_number},
    )
    for page_number, page in enumerate(reader.pages)
]
vectorstore = InMemoryVectorStore.from_documents(pages, embedding=embeddings)
retriever = vectorstore.as_retriever()

#docs = retriever.invoke("programming")
#print(docs)

template = """
Answer the question based on the context below. 
If you can't answer the question, say "I don't know".

Context: {context}
Question: {question}
"""
prompt = PromptTemplate.from_template(template)
parser = StrOutputParser()

chain = (
    {
        "context": itemgetter("question") | retriever,
        "question": itemgetter("question"),
    }
    | prompt | model | parser
)

exit = False
while not exit:
    question = input("Ask a question: ")
    if question == "bye":
        exit = True
    else:
        print(f"Answer: {chain.invoke({'question': question})}")
```
<img width="927" height="346" alt="image" src="https://github.com/user-attachments/assets/01467b00-4ca7-4df1-b239-2741f83ae0e7" />

<img width="923" height="507" alt="image" src="https://github.com/user-attachments/assets/376fb65f-ea74-4c49-9367-4718302bfef0" />

*  For instance, create a vector store directly from a website.
```
from langchain_community.document_loaders import WebPageLoader

loader = WebPageLoader()
doc = loader.load("https://www.codemag.com")
```

* Check out the langchain_community.document_loaders.blob_loaders.YoutubeAudioLoader.

* vectorstore = DocArrayInMemorySearch.from_documents(pages, embedding=embeddings)
   * For a simple PDF this is fine, but for larger sets of data you'll want to use something persistent.
   * This means that every time you run the program, it starts at zero. And it may take a few minutes to ingest a PDF document, so this can get really annoying.
   * For the sample application this is fine, but in the real world, you'll probably want to save the vector store, maybe remove documents from it, or add to it, without having to recalculate everything.
   * to achieve persistence, langchain_community.vectorstores that help you target alternate storage locations.
   * For example, you can use Chroma DB.
```
pip install chromadb 

from langchain_community.vectorstores import Chroma
 
vectorstore = Chroma.from_documents(pages, embedding=embeddings, persist_directory="./codemag")

retriever = vectorstore.as_retriever()
```

* 1st time  - take some time to crunch up the PDF. Once it's done with it, it will save all its work in a directory called “codemag”.
  * Next time you run the program, you can simply check for the existence of the codemag folder, and if it exists, simply load up the vector store, as shown below:

``` vectorstore = Chroma(persist_directory="./codemag",  embedding_function=embeddings) ```

* 1st time ran, it took me around 7 minutes, 2nd time, the load was nearly instantaneous and the results were the same.

```
import os
from langchain_ollama import OllamaLLM,  OllamaEmbeddings
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import PromptTemplate
from langchain_core.documents import Document
from langchain_chroma import Chroma
from operator import itemgetter
from pypdf import PdfReader

MODEL = "llama3"
EMBEDDING_MODEL="embeddinggemma"
model = OllamaLLM(model=MODEL)
embeddings = OllamaEmbeddings(model=EMBEDDING_MODEL)
folder_path = "./codemag"

if os.path.isdir(folder_path):
    vectorstore = Chroma(persist_directory=folder_path, embedding_function=embeddings)
else:
    pdf_path = "24022_CODE_4-2024_Web.pdf"
    reader = PdfReader(pdf_path)
    pages = [
        Document(
            page_content=page.extract_text() or "",
            metadata={"source": pdf_path, "page": page_number},
        )
        for page_number, page in enumerate(reader.pages)
    ]
    vectorstore = Chroma.from_documents(pages, embedding=embeddings, persist_directory=folder_path)
    
retriever = vectorstore.as_retriever()
docs = retriever.invoke("VSCode")
print(docs)

template = """
Answer the question based on the context below. 
If you can't answer the question, say "I don't know".
Context: {context}
Question: {question}
"""
prompt = PromptTemplate.from_template(template)
parser = StrOutputParser()

chain = (
    {
        "context": itemgetter("question") | retriever,
        "question": itemgetter("question"),
    }
    | prompt | model | parser
)

exit = False
while not exit:
    question = input("Ask a question: ")
    if question == "bye":
        exit = True
    else:
        print(f"Answer: {chain.invoke({'question': question})}")
```
  * Once you've created the vector store with the given embeddings, you create a retriever from it.
  * Once you have a retriever, you can choose to invoke it.
  * When you invoke it, you can give it some input context and it returns the top four documents from the input source for the specified input query to the retriever.invoke function.

* “VS Code” as an input parameter

<img width="1007" height="292" alt="image" src="https://github.com/user-attachments/assets/82fb1597-0bfb-45cf-b465-181cd99998db" />

```
Questions

Who is a huge fan of VS Code?
How do you hide files in VS Code?
How do I create the CustomerController class?
```
<img width="1161" height="63" alt="image" src="https://github.com/user-attachments/assets/d86acbb9-409c-496d-bf67-5a02012bbf2c" />

<img width="292" height="316" alt="image" src="https://github.com/user-attachments/assets/fcf528f0-b27c-4e3e-88f7-9c2ddec95fe9" />
