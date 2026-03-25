## Text to Speech and Speech to Text - Automatic Speech Recognition
* not going to rely on the cloud to build the model for you. 
* need a powerful local GPU compute capability, either a higher-end Windows/Linux or Macs. 
* AI involves a lot of calculations and to speed things up, a lot of them are offloaded to the GPU.

### The Main Components
  * ability to listen and convert my spoken text into ASCII text.
  * When I speak into my mic, my program should be able to transcribe the text
  * need a LLM that takes my spoken text, transcribed to plain text, as inputs, and returns a sensible response.
  * ability to take the LLM's response and convert it to audio, which I can then hear through my speakers.

### Enter Hugging Face (http://huggingface.co)
  * It's a popular open-source AI community and platform focused on NLP and transformer-based models.
  * It has a pretty impressive transformers library, a number of pre-trained models, a model hub (find or contribute models), a large number of datasets for your own experimentation and more.
  * bunch of models available under the Audio section in Hugging Face - text-to-speech models, text-to-audio, automatic speech recognition, audio-to-audio, audio classification and voice activity detection.
  * For my needs - text-to-speech and automatic speech recognition useful.

## Automatic Speech Recognition
  * speak to the computer, hopefully in any language, and it should be able to transcribe the texts with decent accuracy.
  * Whisper model (https://github.com/openai/whisper) is a general-purpose speech recognition model.
      * It's trained on a large dataset of diverse audio and is also a multitasking model that can perform multilingual speech recognition, speech translation, and language identification.
      * to use Whisper on my Mac, needed to install FFmpeg first. ``` brew install ffmpeg ``` or  ``` winget install --id Gyan.FFmpeg -e ```
  * requirements.txt
    ```
    soundfile
    pyaudio
    SpeechRecognition
    git+https://github.com/openai/whisper.git
    ```
  * launch.json in .vscode folder that allowed for debugging
    ```
    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python Debugger: Current File",
                "type": "debugpy",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            }
        ]
    }
    ```
  * audio.py - Whisper transcribed accurately into text
    ```
      import whisper
      model = whisper.load_model("base")
      result = model.transcribe("audio.mp3")
      print(result["text"])
    ```
  * Notice, open-source models show a lot of errors and warnings, worth paying heed to. 
    ```
      import logging, warnings
      
      warnings.filterwarnings('ignore')
      
      for name in logging.Logger.manager.loggerDict.keys():
          logging.getLogger(name).setLevel(logging.CRITICAL)
    ```
  * code allows me to speak into the microphone, and Whisper can detect it.
  * “device_index=7” - it's the index of the microphone I wish to listen to. To list all microphones on your computer
    ```
    import speech_recognition as sr
    from speech_recognition import Microphone, Recognizer, UnknownValueError
    
    r = sr.Recognizer()
    
    with sr.Microphone(device_index=7) as source:
        print("Say something!")
        audio = r.listen(source)

    # OR try below code
    '''
    PREFERRED_DEVICE_INDEX = None
    with sr.Microphone(device_index=PREFERRED_DEVICE_INDEX) as source:
        print("Say something!")
        audio = r.listen(source)
    '''
    
    try:
        print("You said:" + r.recognize_whisper(audio, language="english"))
    except sr.UnknownValueError:
        print("Didn't understand")
    except sr.RequestError as e:
        print(f"Could not request results; {e}")
     
    for index, name in enumerate(sr.Microphone.list_microphone_names()):
        print("Microphone with name \"{1}\" found for `Microphone(device_index={0})`".format(index, name))
    ```
  * <img width="450" height="150" alt="image" src="https://github.com/user-attachments/assets/2cc32f3f-5976-4d82-b658-457cf03b9e1f" />
  * speak in any language, detect the language, and use that detected language to both chat with my LLM and use it for audio transcription. This can be done using the code
    ```
      import whisper
      
      model = whisper.load_model("base")
      audio = whisper.load_audio("audio.mp3")
      audio = whisper.pad_or_trim(audio)
      mel = whisper.log_mel_spectrogram(audio).to(model.device)
      
      _, probs = model.detect_language(mel)
      print(f"Detected language: {max(probs, key=probs.get)}")
      
      options = whisper.DecodingOptions()
      result = whisper.decode(model, mel, options)
      print(result.text)
    ```
  * audio transcribing to work continuously. until I say a catch phrase like “Goodbye” or "bye".
    ```
      import os
      import speech_recognition as sr
      from speech_recognition import Microphone, Recognizer, UnknownValueError
      
      def audio_callback(recognizer, audio):
          try:
              prompt = recognizer.recognize_whisper(audio, model="base", language="english")
              print(prompt)
              if "bye" in prompt.lower():
                  stop_listening(wait_for_stop=False)
                  os._exit(0)
          except UnknownValueError: 
              print("There was an error processing the audio.")
      
      recognizer = Recognizer()
      microphone = Microphone(device_index=7)
      
      '''
      PREFERRED_DEVICE_INDEX = None
      recognizer = Recognizer()
      microphone = Microphone(device_index=PREFERRED_DEVICE_INDEX)
      '''
      
      with microphone as source:
          recognizer.adjust_for_ambient_noise(source)
      
      stop_listening = recognizer.listen_in_background(microphone, audio_callback)
      input()  # wait to exit
      stop_listening(wait_for_stop=False)
    ```

### Connecting My Text to a Large LLM
  * A LLM is a type of AI designed to process and understand human language, typically using deep learning techniques.
  * There are many LLMs on Hugging Face (like Llama, Gemma and Phi, etc.,), specialized for various needs.
  * take any of these models and fine tune them also.
  * Fine-tuning a LLM involves adjusting the model's weights and parameters to better perform a specific task or adapt to a particular domain.
  * By fine tuning, you can improve performance for a specific task, you could adopt to your specific domain, you can reduce bias, or you might improve model generalizability, as need be.
  * Gemma (Generative Expert Memory Model Architecture) is an AI model developed by Google.
      * They've trained it on 45 terabytes of data, and it's incredible and knowledgeable in so many fields.
      * great for conversational AI and with minimal prompt engineering,
      * Not saying Llama is bad; to be honest, all of these models are quite comparable to each other.
      * running everything locally, went with the 2-billion parameter version of Gemma.
      * The more parameters, the better your accuracy, but the more beefy computer you're going to need to run this.
      * use this model, go https://huggingface.co/google/gemma-2-2b-it, on the right hand top, click on the “Use this model”, select “Using Transformers” and Try example code. Accept acknowledgement to use
  * code for the conversational bot. 1) create a pipeline object
    * The “mps” is for Mac. PC with an NVIDIA card, use  “cuda”.
    ```
    from huggingface_hub import login
    login("yourtoken")
     
    import torch
    from transformers import pipeline
    
    pipe = pipeline(
        "text-generation",
        model="google/gemma-2-2b-it",
        model_kwargs={"torch_dtype": torch.bfloat16},
        device="mps",
    )
    ```
  * A transformer pipeline in AI refers to a sequence of processing stages that use transformer architectures to perform specific tasks.
      * a pipeline takes a model to make predictions from inputs, and a tokenizer for mapping raw text inputs to a token.
      * A tokenizer is simply a component that splits text into individual words, phrases, or sub words, called tokens.
  * Using the model, create a pipeline. show how simple to build sentiment analysis. code for sentiment analysis using a model. matter of having a model and tokenizer, building a pipeline and using it.
    ```
      import pandas as pd
      from transformers import pipeline, AutoTokenizer, AutoModelForSequenceClassification
      
      # Load pre-trained model and tokenizer
      model_name = "distilbert-base-uncased-finetuned-sst-2-english"
      tokenizer = AutoTokenizer.from_pretrained(model_name)
      model = AutoModelForSequenceClassification.from_pretrained(model_name)
      
      # Create pipeline
      classifier = pipeline("sentiment-analysis", model=model, tokenizer=tokenizer)
      
      # Example text
      text = "I loved the new movie!"
      
      # Run pipeline
      result = classifier(text)
      print(result)
    ```
  * With my pipeline set up, input as “prompt” and the LLM returns me the output 
    ```
      import torch
      from transformers import pipeline
      
      pipe = pipeline(
          "text-generation",
          model="google/gemma-2-2b-it",
          model_kwargs={"torch_dtype": torch.bfloat16},
          device="mps",
      )

      # OR in win
      '''
        USE_CUDA = torch.cuda.is_available()
        PIPELINE_DEVICE = 0 if USE_CUDA else -1
        PIPELINE_DTYPE = torch.bfloat16 if USE_CUDA else torch.float32
        
        pipe = pipeline(
            "text-generation",
            model="google/gemma-2-2b-it",
            model_kwargs={"dtype": PIPELINE_DTYPE},
            device=PIPELINE_DEVICE,
        )
      '''
      
      def generate_text(prompt, previousResponses):
          prompt = prompt + ". Answer in brief."
          allPrevResponses = ""
          for previousResponse in previousResponses:
              allPrevResponses += previousResponse + "\n"
          messages = [
              {"role": "user", "content": allPrevResponses + "\n" + prompt},
          ]
          outputs = pipe(messages, max_new_tokens=256)
          assistant_response = outputs[0]["generated_text"][-1]["content"].strip()
          return assistant_response
      
      previousResponses = []
      while True:
          user_input = input("\033[92m >> You: \033[0m")
          response = generate_text(user_input, previousResponses)
          previousResponses.append(response)
          print("\033[93m >> AI:", response, "\033[0m")
    ```
      * <img width="1250" height="500" alt="image" src="https://github.com/user-attachments/assets/b67352ac-7733-4ff3-a06b-bb95d06ca504" />

### Putting It All Together
  * my fully functional chatbot that I can speak with.
    ```
      import os
      import speech_recognition as sr
      from speech_recognition import Microphone, Recognizer, UnknownValueError
      
      import torch
      from transformers import pipeline
      
      import logging
      import warnings
      
      warnings.filterwarnings('ignore')
      for name in logging.Logger.manager.loggerDict.keys():
          logging.getLogger(name).setLevel(logging.CRITICAL)
      
      pipe = pipeline(
          "text-generation",
          model="google/gemma-2-2b-it",
          model_kwargs={"torch_dtype": torch.bfloat16},
          device="mps",
      )

      # win code
      '''
        USE_CUDA = torch.cuda.is_available()
        PIPELINE_DEVICE = 0 if USE_CUDA else -1
        PIPELINE_DTYPE = torch.bfloat16 if USE_CUDA else torch.float32
        
        pipe = pipeline(
            "text-generation",
            model="google/gemma-2-2b-it",
            model_kwargs={"dtype": PIPELINE_DTYPE},
            device=PIPELINE_DEVICE,
        )
      '''
    
      def AskAI(prompt, previousResponses):
          prompt = prompt + ". Answer in brief."
          allPrevResponses = ""
          for previousResponse in previousResponses:
              allPrevResponses += previousResponse + "\n"
      
          messages = [
              {"role": "user",
               "content": allPrevResponses + "\n" + prompt},
          ]
          outputs = pipe(messages, max_new_tokens=256)
          assistant_response = outputs[0]["generated_text"][-1]["content"].strip()
          return assistant_response
      
      previousResponses = []
      
      def audio_callback(recognizer, audio):
          try:
              prompt = recognizer.recognize_whisper(audio, model="base", language="english")
              print("\033[92m >> You: " + prompt + " \033[0m")
              print("\r Thinking ")
              response = AskAI(prompt, previousResponses)
              previousResponses.append(response)
              print("\r\033[93m >> AI:", response, "\033[0m\n")
      
              if "bye" in prompt.lower():
                  stop_listening(wait_for_stop=False)
                  os._exit(0)
          except UnknownValueError:
              print("There was an error processing the audio.")
      
      recognizer = Recognizer()
      microphone = Microphone(device_index=7)

      # win code
      '''
        PREFERRED_DEVICE_INDEX = None

        recognizer = Recognizer()
        microphone = Microphone(device_index=PREFERRED_DEVICE_INDEX)
      '''
      with microphone as source:
          recognizer.adjust_for_ambient_noise(source)
      
      stop_listening = recognizer.listen_in_background(microphone, audio_callback)
      
      print("\n ------------------------------  \n I am your friendly AI, what do you wanna chat about today? \n ")
      input()  # wait to exit
      stop_listening(wait_for_stop=False)
    ```
  * question to AI is: Teach me how to cook.
    * <img width="450" height="150" alt="image" src="https://github.com/user-attachments/assets/71c5214d-182a-4b48-8ce6-b9c94e016b74" />

### Text to Audio
  * Hugging Face, 2noise/chatts model as the most popular text-to-audio model. https://chattts.com
      ```
        import sounddevice as sd
        import ChatTTS
        
        chat = ChatTTS.Chat()
        chat.load(compile=True)
        
        texts = [
            "how are you?"
        ]
        
        params_infer_code = ChatTTS.Chat.InferCodeParams(
            temperature=0.3,
            top_P=0.7,
            top_K=20,
        )
        
        wavs = chat.infer(
            texts,
            params_infer_code=params_infer_code
        )
        
        sd.play(wavs[0][0], 24000, blocking=True)
      ```
