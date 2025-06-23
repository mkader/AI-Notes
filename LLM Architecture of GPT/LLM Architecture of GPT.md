* LLM Architecture from scratch - GPT-2 [124M]

* “Build a Large Language Model From Scratch book”* by *Sebastian Raschka*
  
* Briefly outline my learning curve:

* Stage 1 - Embedding:
  1. Tokenization - Embedding, Byte-Pair Encoding
  2. Dataset Loader: DataSet, DataLoader from PyTorch (create input_datasets, target_datasets) [batch_size, context_length]
  3. Token Embedding + Positional Embedding [batch, context_length, embed dim]

* Stage 2 - MultiHead Attention (first understand Self-Attention) :
  1. Transformer Blocks (n_layers):
    1.1 Attention Scores [query * key]
    1.2 Masked Attention scores (Only allow earlier tokens in the calculation)
    1.3 Attention weights (softmax masked attention scores)
    1.4 Dropout Layer (mitigate overfitting) [attention_weights * value]
  2. Residual Connection (mitigate vanishing gradients)
  3. Layer Normalization (first understand batch normalization)[scale and shift]
  4. Feed Forward (Add GELU as activation layer)
    4.1 Linear Layer
    4.2 GELU
    4.3 Linear Layer
  5. Dropout Layer (mitigate overfitting)

* Stage 3 - Output Layers
  1. Layer Normalization
  2. Linear Output Layer (logits) [Changed as per business requirements]
  3. Softmax Layer (covert logits to prob)

* Stage 4 - Training
  1. Create a Training and Validation Dataset
  2. Loss Function - Cross-entropy (negative log likelihood)
  3. Optimizer - Calculate gradients (Adam optimizer)
  4. Save the model and optimizer (torch.state_dict())

* Stage 5 - Inference
  1. Give a prompt (starting context)
  2. Generate the next index 
  3. Add the generated index to the prompt and continue generation upto max_new_tokens
  4. Generation Configurations:
    4.1 Using torch.argmax() is greedy approach of generation.
    4.2 Utilize the torch.multinomial() to add randomness to generated text
    4.3 Temperature Sampling - change the prob distribution of the vocabs.
    4.4 Top-k Sampling - sample from the top k highest probs or logits.
    4.5 Top-p Sampling - sample from the highest probs summed to p.

<a href="LLM Architecture from scratch - GPT-2 [124M].pdf">LLM Architecture from scratch - GPT-2 [124M].pdf</a>
