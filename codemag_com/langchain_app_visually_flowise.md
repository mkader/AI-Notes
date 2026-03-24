# Prototyping LangChain Applications Visually Using Flowise

* LangChain is a framework designed to simplify the creation of applications using LLMs. It “chains” together various components to create compelling AI applications that can query vast amounts of up-to-date data.

* Flowise is a low-code/no code drag-and-drop tool that makes it easy for people (programmers and non-programmers alike) to visualize and build LLM apps.
* Instead of writing code using the LangChain framework, you can just drag-and-drop components (known as nodes in Flowise) and connect them.

## Installing Flowise Locally
* There are a couple of ways to get Flowise up and running.
* 1.method to install Flowise on your machine. As Flowise is built using Node.js, you need to first install Node.js.
      * install Node.js is to install nvm (Node Version Manager) first.
      * nvm is a tool for managing different versions of Node.js. It:
          * Helps you manage and switch between different Node.js versions with ease.
          * Provides a command line where you can install different versions with a single command, set a default, switch between them and more.
      * For Windows, download and install the latest nvm-setup.exe file from https://github.com/coreybutler/nvm-windows/releases. 
      * Once nvm is installed, you can install Node.js. ``` nvm install node ```
      * To use the latest version of Node.js, use the following command: ``` nvm use node ```
* 2. Installing Flowise - use npm (Node Package Manager), a tool that comes with Node.js - ``` npm install -g flowise ```
      * Once the installation is done, start up Flowise - ``` npx flowise start ```
* 3. Installing Flowise Using Docker
    ```
    $ mkdir flowise
    # cd flowise
    
    Dockerfile
    FROM node:18-alpine
    
    USER root
    
    RUN apk add --no-cache git
    RUN apk add --no-cache python3 py3-pip make g++
    # needed for pdfjs-dist
    RUN apk add --no-cache build-base cairo-dev pango-dev
    
    # Install Chromium
    RUN apk add --no-cache chromium
    
    ENV PUPPETEER_SKIP_DOWNLOAD=true
    ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser
    
    # You can install a specific version like: 
    #   flowise@1.0.0
    RUN npm install -g flowise
    
    WORKDIR /data
    
    CMD ["flowise","start"]
    ```
    * build a Docker image named flowise: ``` docker build --no-cache -t flowise . ```
    * run a Docker container  ``` docker run -d --name flowise -p 3000:3000 flowise ```
    * The Flowise app internally listens on port 3000.
    * -p option - the container will be configured to listen on port 3000 externally (the first 3000) and forward that traffic to port 3000 internally, aligning with the port where Flowise is actively listening.

* 4.Launching Flowise - http://localhost:3000/ - https://flowiseai.com/ 
     * <img width="600" height="350" alt="image" src="https://github.com/user-attachments/assets/55ab4625-35ae-499b-b0dd-a2c0afad18cf" />
 
## Creating a Simple Language Translator
1. Click the Add New button to create a new Flowise project.
     * <img width="600" height="664" alt="image" src="https://github.com/user-attachments/assets/162b0276-ebea-41c0-a949-b28f2d21eb41" />
2. To build your LLM-based applications, you add nodes to the project. Nodes are the building blocks of your Flowise application.
     * To add a node to the canvas, click the + button to display all the available nodes. All of the available nodes are organized into groups, such as Agents, Cache, Chains, Chat Models, etc.
     * <img width="300" height="600" alt="image" src="https://github.com/user-attachments/assets/bded00d8-2a2e-4ca9-a179-f18a36330317" />
3. Add OpenAI node, drag and drop into the canvas
     * <img width="200" height="300" alt="image" src="https://github.com/user-attachments/assets/d4f770c9-baac-4779-b00a-acf4a80e8fa6" />
4. Add Prompt Template node. create the prompt to instruct the LLM to perform the translation from English to Chinese and Japanese. Type the following sentences into the Template textbox
          ``` 
          Translate the provided {sentence} from 
          English to Chinese as well as Japanese.
          Answer:
          ```
    * <img width="150" height="250" alt="image" src="https://github.com/user-attachments/assets/d661d215-925f-43e2-bff8-252d087daaca" />
5. Add LLM Chain node. This node takes in an LLM as well as a prompt template (as well as some other optional nodes). Connect the three nodes that you've added
    * <img width="350" height="600" alt="image" src="https://github.com/user-attachments/assets/0cc0a3ad-d59c-41bd-9389-15e7306ef8dd" />
6. Save the project. Click the Chat button to bring up the chat window, to test it.
    * <img width="150" height="250" alt="image" src="https://github.com/user-attachments/assets/625dc593-ac64-491b-be9d-cccc6adf9124" />
    * <img width="500" height="450" alt="image" src="https://github.com/user-attachments/assets/2ef14ab0-dc3c-4c6f-aaf5-b6e1e78d38a4" />
7. Downloading the Project, Load it back to Flowise later on. Click on the Project Settings button -> click Export Chatflow
8. Programmatically call your Flowise project using languages such as Python or JavaScript. Click on the button labelled </> and you'll see the list of options shown
     * <img width="500" height="350" alt="image" src="https://github.com/user-attachments/assets/49a1fad2-ad88-4dcf-b3a5-29b52a12b906" />
     
## Creating a Conversational Chatbot (like ChatGPT)
* Use ChatOpenAI, Buffer Memory (remember your conversation), Conversation Chain ( that takes in a LLM and memory with the prompt template already configured for chatting)
     * <img width="350" height="500" alt="image" src="https://github.com/user-attachments/assets/174a9467-cf99-4666-931b-80bc040cb8c3" />
     * chat with the OpenAI LLM and follow up with questions. As the conversation chain is connected to the buffer memory, you can maintain a conversation with the LLM.

* build a chat application without paying for the LLM, use the HuggingFace Inference node.
     * HuggingFace Inference, Prompt Template, LLM Chain
     * <img width="250" height="450" alt="image" src="https://github.com/user-attachments/assets/cda52a8f-c560-492c-b086-17d70be8769c" />
     * HuggingFace Inference node, there are two ways to use the model:
          *  specify the model name, tiiuae/falcon-7b-instruct in this example, the model will be downloaded to your computer and run locally.
          *  If you try too large model, you will get an error
          * use a larger LLM (such as mistralai/Mixtral-8x7B-v0.1), use HuggingFace Inference Endpoints, which runs the model on the cloud (by HuggingFace).

## Querying Local Documents
* in the real world, businesses are more interested in whether they are able to make use of AI to query their own data. Well, using a technique known as vector embedding
* Vector embedding, also known as word embedding or vector representation, is a technique used in NLP and ML to represent words or phrases as numerical vectors.
* The idea behind vector embedding is to capture the semantic relationships and contextual information of words in a continuous vector space.
* build an application that allows you to query your own PDF document.
* Need the following nodes:
     * Character Text Splitter: Use this node to split a long document into smaller chunks that can fit into your model's context window.
     * PDF File: Loads a PDF document for processing. upload a local PDF document for querying
     * HuggingFace Interface (OpenAI) Embeddings: Use this node to perform embedding.
            * Embedding refers to the representation of words or sentences as vectors in a high-dimensional space.
            * It's a way to represent words and sentences in a numerical manner.
            * In contrast, the HuggingFace Interface Embeddings node uses the embedding model from Hugging Face, which is free.
     * In-Memory Vector Store: Use this node to store embeddings in-memory and it performs an exact, linear search for the most similar embeddings.
     * OpenAI: Use this node to make use of an LLM from OpenAI to perform querying of your local data.
     * Conversational Retrieval QA Chain: Use this node to create a retrieval-based question answering chain that is designed to handle conversational context.
     * <img width="600" height="500" alt="image" src="https://github.com/user-attachments/assets/a5f09066-bea3-403c-8930-f12091b43668" />
* Before you can run the project, you need to click the Upsert button
     * <img width="100" height="150" alt="image" src="https://github.com/user-attachments/assets/4384cda2-1d75-4ec6-9d69-b762fbba66f5" />
     * <img width="500" height="350" alt="image" src="https://github.com/user-attachments/assets/64f71972-d118-4df9-b6dc-c3165acb3f94" />
     * upsert refers to an operation that inserts rows into a database table if they don't already exist, or updates them if they do.
     * Clicking the Upsert button performs a few operations: text splitting on the PDF document, creating embeddings using the HuggingFace Inference Embeddings, and then storing the vectors in-memory.
* click on the Chat button to start the chatbot

## Using Agents for Analyzing Data
* LLMs are designed primarily for generating responses related to natural language understanding.
* for example, when presented with a CSV file and asked for a summary of its contents, LLMs often demonstrate limited capabilities in providing a satisfactory answer.
* To use LLMs for analytical tasks, a common approach involves employing the LLM to generate the necessary code for the query and subsequently executing the code independently.
* In LangChain, there is a feature known as agents. An agent is a system that decides what action is to be taken by the LLM, and it tries to solve the problem until it reaches the correct answer.
* Agents are designed to perform well-defined tasks, such as answering questions, generating text, translating languages, summarizing text, etc.
* In short, an agent helps you accomplish your tasks without you needing to worry about the details.
* make use of the CSV Agent in LangChain (and in Flowise) to perform analytical tasks on a CSV file. The CSV Agent node operates by reading a CSV file in the background. It employs the Pandas DataFrame library and uses the Python language to execute Python query code generated by an LLM.
* For the CSV file, using the Titanic training dataset (https://www.kaggle.com/datasets/tedllh/titanic-train
* create a new Flowise project
     * ChatOpenAI: Remember to enter your OpenAI API key.
     * CSV Agent: Click on the Upload File button to select the Titanic CSV file Titanic_train.csv.
     * <img width="350" height="300" alt="image" src="https://github.com/user-attachments/assets/fdefd1b6-c45c-4114-989a-4a5879f87afc" />

* Save, chat, now ask analytical questions pertaining to the CSV file
     * <img width="500" height="400" alt="image" src="https://github.com/user-attachments/assets/9aaae07c-b25f-4a10-a918-fda20f387204" />
