## What Is Gemma 4 12B?
* It is a 12-billion parameter model from Google DeepMind
* It is small enough to run on a standard laptop with 16GB RAM, yet smart enough to reason through complex Python logic and system architecture.

* Google explicitly highlights coding and agentic workflows as core targeted use cases in the official Gemma 4 model card.

* Gemma is built for agents. Google explicitly optimized Gemma for agentic tool use and multi-step reasoning. 

* Gemma has a massive 256K context window. Vibe coding relies heavily on feeding entire codebases, file structures, and error logs into the model.
    * With a 256K token context, you don't have to meticulously trim your files before handing them over to the model.

## Setting Up Your Workspace
* Download and install Gemma via llama.cpp or Ollama.com
* Ollama is an open-source framework that allows you to run LLMs (Llama 3 or Mistral) directly on your computer.
  
* CLI cmd ``` ollama run llama3 ``` - it will download, setup and processing right on your local hardware.
* see models list https://ollama.com/search
* downlaod Gemma 4 ``` ollama run gemma4:12b ```

* Create folder 'VibeStudio', requirements.txt ``` ollama rich ```, index.py

## Building the Application
* This article will show you how to write an application that helps you with vibe coding.
```
index.py

import os
import ollama
from rich.console import Console
from rich.markdown import Markdown

# The Console object helps us print
# beautiful text to the terminal
console = Console()


class VibeStudio:
    def __init__(self):
        self.model = "gemma4:12b"

        # Tell Gemma 4 to be accurate and literal
        self.options = {
            "temperature": 0.2,
            "top_p": 0.9,
        }

```
* rich - allows to display styled text, colors, tables and cleanly formatted Markdown text.
* console - show you a colorful message, a loading spinner, a nicely formatted block of code
* the temperature low (0.2) because code requires precision, not creative poetry.
    * A temperature of 1.0 is highly creative (great for writing stories).
* “top_p”: 0.9: works alongside the temperature to discard highly unlikely words, ensuring that Gemma 4 sticks to the most reliable, standard coding patterns.

### Reading Your Project
* AI crawls your folders incurrent workspaceand ignores the “trash” (like __pycache__ or .git folders).
* find every Python file, open them up, read everything written inside them and bundle it all up into a neat list so the AI can look at your entire project at once.

```
def get_workspace_files(self):
    files_to_read = []

    for root, dirs, files in os.walk("."):
        # Filter out junk
        if any(x in root for x in [".git", "venv", "__pycache__"]): continue

        for name in files:
            if name.endswith(".py"): path = os.path.join(root, name)
                with open(path, "r") as f:
                    files_to_read.append({"name": name, "content": f.read()})

    return files_to_read
```

### The “Vibe” Prompt
* core logic of the application, take your NL request (the “vibe”) and give it to Gemma along with your code.
```
def process_vibe(self, user_input):
    code_context = self.get_workspace_files()

    # We build a 'System Prompt' to tell Gemma 4 its job
    system_instructions = "You are a Vibe Coding assistant. 
        Your goal is to take high-level user requests and 
        provide perfect Python code improvements 
        based on the current workspace."

    # We bundle the code files into a single string
    context_string = "\n".join(
        [f"FILE {f['name']}:\n{f['content']}" for f in code_context]
    )

    full_prompt = f"CONTEXT:\n
    {context_string}\n\nUSER REQUEST: {user_input}"

    # Call Gemma 4 locally
    response = ollama.generate(
        model=self.model,
        system=system_instructions,
        prompt=full_prompt,
        options=self.options
    )
    return response['response']
```
* Each file turns it into one long text, labels (like FILE main.py:)

### Running the Application
```
def main():
    studio = VibeStudio()
    console.print("[bold cyan]VibeStudio is Online. Use natural language to code.[/bold cyan]")
    
    while True:
        task = input("\nWhat's the vibe? (or 'exit'): ")
        if task.lower() == 'exit': break
        console.print(Markdown(studio.process_vibe(task)))

if __name__ == "__main__":
    main()
```

````
Given a signed 32-bit integer x, return x with its digits reversed. If reversing x causes the value to go outside the signed 32-bit integer range [-231, 231 − 1], then return 0. Assume the environment does not allow you to store 64-bit integers (signed or unsigned).
````
```
Listing 4: Vibe coded output
def reverse_integer(x: int) -> int:
    """
    Reverses the digits of a 32-bit signed integer.
    If the reversed value exceeds the 32-bit signed integer range, returns 0.
    """
    # Define the limits for a 32-bit signed integer
    MIN_INT = -(2**31)
    MAX_INT = 2**31 - 1
    res = 0

    # Work with the absolute value to simplify modulo/division logic
    # and handle the sign at the end.
    num = abs(x)
    sign = -1 if x < 0 else 1

    while num > 0:
        # Extract the last digit
        digit = num % 10

        # Append it to our result
        res = (res * 10) + digit

        # Remove the last digit from the original number
        num //= 10

    # Apply the original sign
    res *= sign

    # Check if the result is within the valid 32-bit signed integer range
    if res < MIN_INT or res > MAX_INT:
        return 0

    return res

# Example usage:
# print(reverse_integer(123))  # Output: 321
# print(reverse_integer(-123)) # Output: -321
# print(reverse_integer(120))  # Output: 21
# print(reverse_integer(1534236465)) # Output: 0 (overflows)
```

* Gemma is also incredibly good at parsing audio text and pictures.
* Take this application further and do some DSL-specific stuff, like point it to a db and have it query the db via NL.
    * See it generate SQL commands underneath the hood. How about log analytics, or Splunk queries?
    * Or my favorite thing to dunk on, git.
```
Prompt
How do I revert the last two `git` commits that I have already pushed to develop?
```

* write a readme.md or add a .gitignore ``` Add a suitable `readme.md` to this project. ```
* ``` Suggest a suitable `.gitignore` for this project. ```
``` 
Prompt
Look at this layout bug image, check my `styles.css` file in the context window, and fix the alignment.
```

<img width="632" height="691" alt="image" src="https://github.com/user-attachments/assets/184a4293-7559-44ef-bea1-113c0ee95b5c" />

