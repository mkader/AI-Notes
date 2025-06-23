* LLM Architecture
 
* How Do Large Language Models Actually Work?
* Let’s break it down, step-by-step:

🔹 1. Tokenization :  First, the model slices language into "tokens"—like words or parts of words—turning human input into something it can read.

🔹 2. Embedding :  Tokens become vectors—mathematical representations that let the model understand meaning and context.

🔹 3. Attention :  The model decides what to focus on. Using “self-attention,” it learns which words relate to each other, across the entire sequence.

🔹 4. Feed-Forward Layers :  Each token’s meaning gets refined through dense layers that introduce depth and non-linear understanding.

🔹 5. Normalisation :  Layer norms + residuals keep everything stable while Dropout prevents overfitting. Clean, efficient learning.

🔹 6. Prediction :  Finally, the model generates output by assigning probabilities to possible next tokens. Softmax, temperature, and sampling strategies come into play.

<img src="https://github.com/mkader/AI-Notes/blob/main/How%20Do%20Large%20Language%20Models%20Actually%20Work.gif">
