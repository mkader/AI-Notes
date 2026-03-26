# Introduction to OpenAI Services

* OpenAI is an American AI research laboratory consisting of the non-profit OpenAI Incorporated and its for-profit subsidiary corporation, OpenAI Limited Partnership.

## ChatGPT and LLMs
  * The GPT in ChatGPT gets its name from Generative Pre-trained Transformer.
  * GPT is a family of language models known as LLMs.
  * LLMS are deep learning models that are trained using vast amounts of text data, such as books, articles, websites, and more.
  * LLMs are able to process and generate human-like responses, and can perform sophisticated tasks, such as language translation, generating new content/code, debugging code, providing suggestions and recommendations, and much more.
  * LLMs are often used in NLP tasks, such as chatbots and virtual assistants, as they are able to hold intelligent conversations with human beings.

* OpenAI pre-trained models and the services it provides
  * Services: This is for end-users who want to make use of AI to do their daily work.
  * APIs: This is for developers who want to integrate OpenAI's services into their applications.
  * Python packages: This is specifically for Python developers to use OpenAI's services.
  * <img width="750" height="450" alt="image" src="https://github.com/user-attachments/assets/1be274d8-3f25-4a73-9a0e-e848ee8e09b0" />

## Using OpenAI Services
1. ChatGPT -https://chat.openai.com
    * ChatGPT ability to provide the answers to questions across many domains of knowledge.
    * ChatGPT has been known to provide factually incorrect responses.
    * ask ChatGPT - Write code for you, Debug a block of code, Provide advice, Explain things, Write a book summary, Create a course outline and content
    * ChatGPT Plus is monthly fee $20, get faster responses, priority access to new features, and the service is always available even when the demand is high.
    * more interesting prompts use ChatGPT, check out https://github.com/f/awesome-chatgpt-prompts/.

2. Using DALL-E 2 to Generate Images -https://labs.openai.com
    * It's a neural network-based image generation system, it's trained on massive amounts of images and text data.
    * The training set consists of image-text pairs, where each image has an accompanying textual description of the image.
    * The system then learns how to generate new images based on the data that it learned from the training set.
    
3. Stable Diffusion (similar DALL-E), ML model developed by Stability AI. Unlike DALL-E, Stable Diffusion can be accessed through various websites.
   
4. Using OpenAI APIs - for developers to access OpenAI's services.
   * Perform a number of tasks - Listing the models available for use, Creating chats/edits to prompts, Generating images, Creating variations of images/embeddings of a given input/transcription of audio
    * Check out https://platform.openai.com/docs/api-reference/ for more details on OpenAI's API offerings.
    * Accessing Through HTTP - Get OpenAI API key - https://platform.openai.com/account/api-keys
    * Getting All the Models ``` $ curl https://api.openai.com/v1/models -H "Authorization: Bearer $OPENAI_API_KEY" ```
        ```
        {
            "object": "list",
            "data": [
                {
                    "id": "babbage",
                    "object": "model",
                    "created": 1649358449,
                    "owned_by": "openai",
                    "permission": [
                        {
                            "id": "modelperm-49FUp5v084tBB49tC4z8LPH5",
                            "object": "model_permission",
                            "created": 1669085501,
                            "allow_create_engine": false,
                            "allow_sampling": true,
                            "allow_logprobs": true,
                            "allow_search_indices": false,
                            "allow_view": true,
                            "allow_fine_tuning": false,
                            "organization": "*",
                            "group": null, 
                            "is_blocking": false
                        }
                  ],
                  "root": "babbage",
                  "parent": null
                },
                {
                    "id": "davinci",
                    "object": "model",
                ...
            ]
        }
        ```
    * The list of models supported by the OpenAI API
        ```
         "babbage"  "davinci"    "text-davinci-edit-001"    "babbage-code-search-code"  "text-similarity-babbage-001"  "code-davinci-edit-001"  "text-davinci-001"  "text-embedding-ada-002"
         "ada"  "babbage-code-search-text" "babbage-similarity"        "code-search-babbage-text-001" "text-curie-001"  "code-search-babbage-code-001"  "text-ada-001"  "text-similarity-ada-001"
         "text-search-ada-query-001" "davinci-search-document" "curie-instruct-beta"          "ada-code-search-code"  "ada-similarity" "gpt-3.5-turbo-0301"  "code-search-ada-text-001"
         "code-search-ada-code-001"     "ada-search-query"  "gpt-3.5-turbo"  "ada-code-search-text"  "text-search-ada-doc-001" "davinci-instruct-beta"    "text-similarity-curie-001"
        "text-search-curie-query-001" "whisper-1" "text-search-babbage-doc-001" "text-search-davinci-query-001" "curie-search-query" "davinci-search-query" "babbage-search-document" "ada-search-document"
         "curie-search-document"   "text-search-curie-doc-001" "babbage-search-query"  "text-babbage-001" "text-search-davinci-doc-001" "text-search-babbage-query-001" "curie-similarity" "curie"
        "text-davinci-003" "text-similarity-davinci-001" "text-davinci-002" "davinci-similarity" "cushman:2020-05-03" "ada:2020-05-03" "babbage:2020-05-03"  "curie:2020-05-03" "davinci:2020-05-03"
        "if-davinci-v2" "if-curie-v2" "if-davinci:3.0.0" "davinci-if:3.0.0" "davinci-instruct-beta:2.0.0"         "text-ada:001"         "text-davinci:001" "text-curie:001" "text-babbage:001"
        ```
    * Chat - the most exciting features in OpenAI is its chat feature. Latest (GPT-5.3–based)
       *  use the gpt-3.5-turbo model to ask it to tell us a joke.   The joke returned by the model is encapsulated in the choices key. 
          ```
            $ curl https://api.openai.com/v1/chat/completions -H "Content-Type: application/json" -H "Authorization: Bearer $OPENAI_API_KEY" \
             -d '{
                    "model": "gpt-3.5-turbo",
                    "messages": [{"role": "user", 
                    "content": "Tell me a joke!"}],  
                    "temperature": 0.7
              }'
            
            {
                "id":"chatcmpl-7BuXX0OgtQvg32UgU7lD3jELqPgNm",
                "object":"chat.completion",
                "created":1683073303,
                "model":"gpt-3.5-turbo-0301",
                "usage":{
                    "prompt_tokens":13,
                    "completion_tokens":14,
                    "total_tokens":27
                },
                "choices":[
                    {
                        "message":{
                            "role":"assistant",
                            "content":"Why don't scientists 
                                       trust atoms? \n\nBecause they make 
                                       up everything."
                        },
                    "finish_reason":"stop",
                    "index":0}
                ]
            }
          ```
    * Generating Images. generate an image of the traditional Chinese lion dance:
        ```
        $ curl https://api.openai.com/v1/images/generations  -H "Content-Type: application/json"  -H "Authorization: Bearer \
          $OPENAI_API_KEY" \
          -d '{
            "prompt": "Lion dance",
            "n": 1,
            "size": "1024x1024"
          }'
        
        {
          "created": 1683075354,
          "data": [
            {
              "url": "https://oaidalleapiprodscus.blob.core.
                 windows.net/private/org-
                 t5oD1gil8GcrGd2igJn9YYK9/user-
                 ...
                 06&sig=xpN8HlaJQI6/12yoJGTfJqjtKjAsY4Byxbl
                 wLl85P9M%3D"
            }
          ]
        }
        ```
6. Using OpenAI Python Packages
   * Using GTP-3 for Chatting. integrate ChatGPT into your Python application.
      ```
      # install the openai packag
      !pip install openai
      
      # specify your OpenAI API key:
      import openai
      openai.api_key = "YOUR_API_KEY"
      
      # Using the openai package, you can list the models available:
      models = openai.Model.list()
      [model['id'] for model in models['data']]
      
      # use the “gpt-3.5-turbo” for integrating ChatGPT into a Python application:
      completion = openai.ChatCompletion.create(
          model="gpt-3.5-turbo",
          messages = [{"role": "user", "content": " What is Python?"}],
          max_tokens = 1024,
          temperature = 0.8)
      print(completion)
      message = completion.choices[0].message.content
      print(message)
      ```
    * The ChatCompletion.create() function takes in the following arguments:
        * Model: The model to use
        * Messages: The message to send to the chat bot, which must be packaged as a list of dictionaries
        * max_tokens: The maximum number of tokens to generate in the “completion.” If you set this to a small number, the response returned may not be complete.
        * Temperature: A value between 0 and 2. Lower value makes the output more deterministic.
           * If you set it to a higher value like 0.8, the output is more likely to be different when you call the function multiple times.
    * Tokens is a pieces of words. Before the API processes your prompt, it's broken down into tokens. Approximately a 1500-word sentence is equivalent to about 2048 tokens.
        * For more information on tokens, refer to: https://help.openai.com/en/articles/4936856-what-are-tokens-and-how-to-count-them
    * Response
       ``` 
        {
            "choices": [
                {
                    "finish_reason": "stop",
                    "index": 0,
                    "message": {
                         "content": "Python is a high-level,
                          interpreted programming language that is
                          ...
                          making it accessible to everyone.",
                    "role": "assistant"
                    }
                }
            ],
            "created": 1683003427,
            "id": "chatcmpl-7BcMVmIotmFcAFa4Wc9KyLB9Rp0gz",
            "model": "gpt-3.5-turbo-0301",
            "object": "chat.completion",
            "usage": {
                "completion_tokens": 94,
                "prompt_tokens": 12,
                "total_tokens": 106
            }
        }
       ```
   * ChatGPT doesn't remember your previous questions. So in order for you to have a meaningful conversation with it, you need to feed the previous conversation back to the API.
      * <img width="350" height="150" alt="image" src="https://github.com/user-attachments/assets/9be43a0e-94f4-4375-9856-91d5b8c71e59" />
      * updated code to allow the user to have a meaningful conversation with ChatGPT:
      ``` 
       Messages = []
       while True:
           prompt = input('\nAsk a question: ')    
           messages.append(
               {
                   'role':'user',
                   'content':prompt
               })    
        
           # creating a chat completion
           completion = openai.ChatCompletion.create(model="gpt-3.5-turbo",  messages = messages)
           
           # extract the response from GPT
           response = completion['choices'][0]['message']['content']
           print(response)    
           # append the response from GPT
           messages.append(
               {
                   'role':'assistant',
                   'content':response
               })
      ```
7. Whisper API - is a general-purpose speech recognition model offered by OpenAI.
   * It's trained on a large dataset of diverse audio and is also a multitasking model that can perform multilingual speech recognition, speech translation, and language identification.
   * install the Whisper API ``` !pip install -U openai-whisper ```
   * API offers 5 main pre-trained models that you can use for your transcription.  ``` whisper.available_models() ```
   * 5 main models: tiny, base, small, medium, and large: ``` ['tiny.en', 'tiny', 'base.en', 'base', 'small.en', 'small', 'medium.en', 'medium', 'large-v1', 'large-v2', 'large'] ```
   * the details of the various models, such as their number of trainable parameters, required memory, and relative execution speed.
      * <img width="350" height="150" alt="image" src="https://github.com/user-attachments/assets/0a809681-01e6-4f19-9ef8-95ab7ee6e1ad" />
  * Creating a Model -  first time, the weights of the model are downloaded onto your computer. It will be saved in the ~/.cache/whisper directory.
      ```
      import whisper
      model = whisper.load_model("base")
      ```
  * Transcribing Audio and Translating Text - either load local the audio file or URL
     ```
     result = model.transcribe('https://www.voiptroubleshooter.com/open_speech/american/OSR_us_000_0015_8k.wav')
     print (result["text"])
     ```
   * warning like “UserWarning: FP16 is not supported on CPU; using FP32 instead”, that means your model isn't able to make use of the GPU; instead it makes use of the CPU.
      * FP16 (half-precision floating point) uses 16-bits for storing floating point numbers while FP32 (single-precision floating point) uses 32-bits.
      * Although FP32 allows higher precision and accuracy, it comes with a cost in terms of larger memory footprints.
      * In general, for DL, FP16 is preferred over FP32 due to its faster computation times and also tasks like image classification and object detection do not require a lot of precision.
   * The result returned by the transcribe() function contains the transcription (text) and a list of all the transcription segments (segments):
     ``` 
     {'text': " The first can be slid on the 
     smooth planks. Glue the sheet to the dark 
     ...
     is hard to sell.",
      'segments': [{'id': 0,
        'seek': 0,
        'start': 0.0,
        'end': 6.5200000000000005,
        'text': ' The first can be slid on the smooth planks.',
        'tokens': [50364,
         440,
         ...
         13,
         50690],
        'temperature': 0.0,
        'avg_logprob': -0.29290932700747535,
        'compression_ratio': 1.5105263157894737,
        'no_speech_prob': 0.024191662669181824},
       {'id': 1,
        'seek': 0,
        'start': 6.5200000000000005,
        'end': 10.32,
        'text': ' Glue the sheet to the dark blue background.',
      ...  
      'language': 'en'}
     ````
  * each segment contains info like start time, end time, text, and more. You can load the values of the segments key as a Pandas DataFrame for easier inspection:
     * <img width="500" height="200" alt="image" src="https://github.com/user-attachments/assets/8a69b57b-1b51-4b22-8b90-7211ec87b4f5" />
    ```
    import pandas as pd
    df = pd.DataFrame.from_dict(result['segments'])
    ```
  * As the transcription can take a while to perform, you can set the verbose parameter to True so that the transcription segments can be displayed as and when it is ready:
     ```
     result = model.transcribe('https://www.voiptroubleshooter.com/open_speech/american/OSR_us_000_0015_8k.wav', verbose = True)
     ```
     * Here it the output when you set the verbose parameter to True:
       ``` 
        Detecting language using up to the first 30 
        seconds. Use `--language` to specify the language
        Detected language: English
        [00:00.000 --> 00:06.520]  The first can be slid on the smooth planks.
        [00:06.520 --> 00:10.320]  Glue the sheet to the dark blue background.
        ...
        [00:37.480 --> 00:39.800]  A large size of stockings is hard to sell.
       ```
    * translate the result into English from French:
      ```
       result = model.transcribe('https://www.voiptroubleshooter.com/open_speech/french/OSR_fr_000_0043_8k.wav', verbose = True)
       result = model.transcribe('https://www.voiptroubleshooter.com/open_speech/french/OSR_fr_000_0043_8k.wav', verbose = True, task = 'translate')
      ```

## Training ChatGPT Using Your Custom Data
  * ChatGPT could answer specific questions based on your own training data.
  * For example, 20 years PDF of CODE Magazine provides a very useful database of coding knowledge.
  * ChatGPT could learn from this set of magazine content and be able to answer questions that you throw at it.
  * Install packages  ```  !pip install gpt_index==0.4.24 gradio langchain==0.0.107 PyPDF2 ```
     * aIndex (gpt_index) is a project that provides a central interface to connect your LLMs with external data.
     * Gradio is a Python package that displays UI for interacting with AI chatbot.
     * LangChain is a framework for developing applications powered by language models.
     * PyPDF2 is a Python package for reading PDF files.
  * Preparing the Training Data - use 3 PDF recent CODE Magazine.
     * create a folder "training documents", place the pdf
  * Training ChatGPT code
     ```
       from gpt_index import SimpleDirectoryReader, GPTListIndex, GPTSimpleVectorIndex, LLMPredictor, PromptHelper
       
       # the ChatOpenAI class allows you to use the OpenAI's  models at https://platform.openai.com/docs/models
       from langchain.chat_models import ChatOpenAI 
       import os 
       
       os.environ["OPENAI_API_KEY"] = "YOUR_OPENAI_API_KEY"
       
       # index the documents in the specified path
       def index_documents(folder):
           max_input_size    = 4096
           num_outputs       = 512
           max_chunk_overlap = 20
           chunk_size_limit  = 600
       
           # helps us fill in the prompt, split the text, and fill in context information according to necessary token limitations
           prompt_helper = PromptHelper(max_input_size, 
                                        num_outputs, 
                                        max_chunk_overlap, 
                                        chunk_size_limit =
                                        chunk_size_limit)
           
           # the LLMPredictor class is a wrapper around 
           # an LLMChain from Langchain
           llm_predictor = LLMPredictor(
               llm = ChatOpenAI(temperature = 0.7, 
                                model_name = "gpt-3.5-turbo", 
                                max_tokens = num_outputs))
       
           # load the documents from the documents folder
           documents = SimpleDirectoryReader(folder).load_data()
       
           # The GPTSimpleVectorIndex is a data structure where nodes are keyed by embeddings, and those embeddings are stored within a simple dictionary.
           # During index construction, the document texts are chunked up, converted to nodes with text; they are then encoded in document embeddings stored within the dict.
           index = GPTSimpleVectorIndex(documents, llm_predictor = llm_predictor, prompt_helper = prompt_helper)
           index.save_to_disk('index.json') 
       
       index_documents("training documents")
     ```
      * After the training, the model is saved into the index.json file. When you run the code snippet, you'll see the following output:
         ```
          INFO:root:> [build_index_from_documents] 
            Total LLM token usage: 0 tokens
          INFO:root:> [build_index_from_documents] 
            Total embedding token usage: 218071 tokens
         ```
  * Asking Questions
    ```
    def Code_Mag_KB(input_text):
        index = \ GPTSimpleVectorIndex.load_from_disk('index.json')    
        response = index.query(input_text, response_mode = "compact")
        return response.response
    
    Code_Mag_KB('Summarize what a smart contract is?')
    ```
    ```
    INFO:root:> [query] Total LLM token usage: 641 tokens
    INFO:root:> [query] Total embedding token usage: 9 tokens
    
    “\n A smart contract ... and immutably.”
    ```

## Wrapping ChatGPT Using a Web-Based UI
  * using a web-based UI so that users can directly interact with ChatGPT. code wraps ChatGPT using the Gradio package:
  * Gradio is a Python package that creates a web-based UI for interacting with your machine learning/deep learning models.
    ```
    import gradio as gr
    
    # display the UI
    interface = gr.Interface(fn = Code_Mag_KB,
        inputs = gr.components.Textbox(
        lines = 5, 
        label = 'Enter your question'),
        outputs = 'text',
        title = 'CODE Magazine Knowledge Base')
    
    interface.launch(share=False)
    ```
     * <img width="750" height="350" alt="image" src="https://github.com/user-attachments/assets/e6f51862-4cff-4563-9b2b-ac431324fc0b" />

## Integrating ChatGPT with Jupyter Notebook
  * integrating ChatGPT into your Jupyter Notebook, you can get ChatGPT to - Add Docstrings for your Python functions, Provide an explanation/Debug/Complete/Review for your code, Ask questions.
  * install the ChatGPT - Jupyter - AI Assistant Chrome extension in your Chrome browser (https://chrome.google.com/webstore/detail/chatgpt-jupyter-ai-assist/dlipncbkjmjjdpgcnodkbdobkadiejll/related).
  * <img width="750" height="350" alt="image" src="https://github.com/user-attachments/assets/1d468630-a1fc-4407-ad71-e92973bfd226" />
  * Once the extension is added, you need to configure it - you need to enter your OpenAI key
  * <img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/0332d54d-7f07-4882-87bb-01f11c2d9c2f" />
  * Format: Adding Docstrings to Your Python Function. Suppose I have the following code snippet in a cell:
     ```
     import pandas as pd
     
     from sklearn.linear_model import LinearRegression
     
     def calculate_vif(df, features): vif, tolerance = {}, {}
     
         # all the features that you want to examine 
         for feature in features:
             # extract all the other features you will regress against
             X = [f for f in features if f != feature]        
             X, y = df[X], df[feature]
             
             # extract r-squared from the fit
             r2 = LinearRegression().fit(X, y).score(X, y)                
             
             # calculate tolerance
             tolerance[feature] = 1 - r2
             
             # calculate VIF
             vif[feature] = 1/(tolerance[feature])
      
         # return VIF DataFrame
         return pd.DataFrame({'VIF': vif, 'Tolerance': tolerance})
     ```
  * Clicking on the Format button makes ChatGPT add the Docstrings to the function. Here's what ChatGPT has added and modified (shown in bold):
     ``` 
     import pandas as pd
     
     from sklearn.linear_model import LinearRegression
     
     def calculate_vif(df: pd.DataFrame, features: list) -> pd.DataFrame:    
         """
         Calculate Variance Inflation Factor (VIF) and Tolerance for each feature in a pandas DataFrame
         
         Parameters:
         df (pd.DataFrame): DataFrame containing data for all features features (list): 
         List of strings of all column names in the DataFrame that needs to be calculated
         
         Returns:
         pandas.DataFrame: Returns DataFrame containing VIF and Tolerance for each feature
         
         """
         vif, tolerance = {}, {}
         
         # Iterate over all the features that need to be examined 
         for feature in features:        
             # Extract all the other features 
             # you will regress against
             X = [f for f in features if f != feature]        
             X, y = df[X], df[feature]
             
             # Extract R^2 from the fit
             r2 = LinearRegression().fit(X, y).score(X, y)                
             
             # Calculate tolerance
             tolerance[feature] = 1 - r2
             
             # Calculate VIF
             vif[feature] = 1/(tolerance[feature])
         
         # Return VIF DataFrame
         return pd.DataFrame({'VIF': vif, 'Tolerance': tolerance})
     ```
  * click on the Explain button, ChatGPT attempts to explain the code in the currently selected cell. For my function, it explains it very well.
     * <img width="450" height="150" alt="image" src="https://github.com/user-attachments/assets/724da194-3c1b-4967-8ea6-a29b5d032b13" />
  * Debug: Fixing the Bug in the Code, Remove the df parameter from the calculate_df() function in my sample code snippet:
     * ``` def calculate_vif(features): vif, tolerance = {}, {} ```
     * click on the Debug button, ChatGPT attempts to debug the code. It rightfully pointed out that there is no df defined in the function. And it even offers a solution to fix the code.
     * <img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/7bee9da1-c3d7-46ea-ac58-b01da759cb45" />
  * Complete: Writing the Code for You. Suppose you want to write the VIF function but aren't sure how to do it. click on the Complete button:
    ```
    def calculate_vif(df,features):
    ```
  * Based on the function name, ChatGPT attempts to write the code for you
     * <img width="400" height="250" alt="image" src="https://github.com/user-attachments/assets/e9cdb4a3-8750-4ac2-9a5c-eb01c03d2558" />
  * Review: Performing a Code Review and Suggesting Improvements. Click Review button, ChatGPT shows me the suggestions
     * <img width="400" height="200" alt="image" src="https://github.com/user-attachments/assets/db830f82-b961-4b59-8dec-6badce5e601d" />
  * Questions, directly ask in Jupyter Notebook instead of going to https://chat.openai.com to ask a question. click Question button
