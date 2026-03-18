# Fixing Security Issues in Seconds, Not Sprints, Powered by GenAI

* Developers typically discover vulnerabilities during PR reviews or using tools like CodeQL or SonarQube to flag them later—at a point when fixing them takes more time and costs more.
* What if developers could catch those issues instantly, right in their local environment, before even submitting a PR?
* SecureCodeAgent, a GenAI-powered solution that shifts security left in the software development lifecycle.
* SecureCodeAgent uses this service as its GenAI engine.
  
* how SecureCodeAgent works, quick summary of secure code agent.
* <img width="200" height="225" alt="image" src="https://github.com/user-attachments/assets/4b630ae6-bd15-44cd-b684-83baad9e66d9" />

## Why Catching Issues Early Matters
* Fixing a vulnerability in production can cost 10–30 times more than fixing it during development.
* An SQL injection caught after deployment might mean weeks of remediation, patches, and potential downtime.
* The same issue flagged in a developer's editor or local environment could be resolved in minutes.
* Current tools like CodeQL and SonarQube detect vulnerabilities, but only after developers submit pull requests.
* When security issues are flagged, the development cycle stalls, PRs fail, developer's context-switch to fix code they wrote days earlier, and the process repeats until all gates pass.
* GenAI agents solve this by moving security analysis into the coding process itself, providing real-time feedback that transforms security from a downstream gate into an upstream enabler of faster, safer development.

## GenAI vs. Traditional Static Analysis: Cost and Capability Comparison
* CodeQL and SonarQube dominate the static analysis landscape, GenAI-powered solutions like SecureCodeAgent offer compelling advantages in both cost and capability.
* Traditional tools can cost - $19–$99 per user/month for CodeQL through GitHub Advanced Security and SonarQube $150 for developer and €30 monthly for SonarQube Cloud's Team plan (100k lines of code).
* Azure OpenAI API typically charges $5 per million input tokens and $15 per million output tokens, translating to roughly $0.02–$0.05 per code analysis for average functions—potentially 10–50 times cheaper for small to medium teams.

* GenAI excels in areas where rule-based tools struggle:
    * It explains why code is vulnerable rather than just flagging it,
    * applies security knowledge across languages,
    * adapts to new vulnerability patterns without manual rule updates,
    * and provides natural language explanations that improve developer education.
* CodeQL and SonarQube provide comprehensive rule coverage and enterprise-grade reporting, GenAI tools enhance the developer experience through conversational feedback, instant explanations, and the ability to understand business context, making security more accessible and educational rather than merely punitive.

* By moving security checks closer to where developers write code, they can:
    * Reduce delays caused by back-and-forth in PR reviews.
    * Avoid introducing vulnerabilities into shared branches.
    * Improve their own awareness of secure coding practices.

## How SecureCodeAgent Works
* <img width="700" height="400" alt="image" src="https://github.com/user-attachments/assets/14218887-06cb-46bf-befd-3293c5b3b34e" />

* Each finding is presented in a structured, developer-friendly table, highlighting:
    * The specific insecure or inefficient line of code
    * A secure replacement suggestion
    * The rationale behind the recommendation
    * The severity level (high, medium, or low)

## Step-by-Step Demonstration
* Python code defines a simple password storage function that uses MD5 hashing, a known security weakness.
* SecureCodeAgent detects the issue and suggests a fix.
```
import os
from openai import AzureOpenAI

# Initialize Azure OpenAI client
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),
    api_version="2024-05-01-preview",
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT")
)

# Example: Developer code snippet to be validated
sample_code = """
import hashlib

def store_password(password):
    # Weak hashing algorithm
    return hashlib.md5(password.encode()).hexdigest()
"""

# Prompt instructs AI to return structured feedback
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {
            "role": "system",
            "content": (
                "Analyze the given code and provide results in a table "
                "with columns: 1) Detected Issue, 2) Suggested Secure "
                "Code, 3) Reason for Change, 4) Criticality."
            )
        },
        {
            "role": "user",
            "content": sample_code
        }
    ],
    temperature=0,
    top_p=1
)

# Extract response content
response_content = (
    response.choices[0].message.content
    if response.choices else "No response generated."
)

# Print security analysis
print("=== SecureCodeAgent Analysis ===")
print(response_content)
```
* <img width="750" height="75" alt="image" src="https://github.com/user-attachments/assets/c87ac970-f260-4f43-a1b9-d614ac4f4415" />

## Automating the Workflow
* Running the script manually is useful, but the real power comes when SecureCodeAgent is integrated into Git workflows.
    * Pre-commit hook: Run scans before a commit is made.
    * Pre-push hook: Block insecure code from being pushed upstream.
    * CI/CD pipeline: Add SecureCodeAgent as a job in GitHub Actions or Jenkins for automated enforcement.
* For example, a simple pre-push Git hook can automatically run the script and prevent insecure code from reaching the remote repository.
* Git Pre-Push Hook with SecureCodeAgent
    * Save this as .git/hooks/pre-push in your repo and make it executable (chmod +x .git/hooks/pre-push). The following code depicts a bash script for invoking SecureCodeAgent.
```
#!/bin/bash
# Pre-push Git hook to run SecureCodeAgent
# before pushing code

echo "🔍 Running SecureCodeAgent security scan.."

# Path to your Python SecureCodeAgent script
SECURECODE_AGENT_SCRIPT="./securecodeagent_scan.py"

# Run the scan
python3 "$SECURECODE_AGENT_SCRIPT"

# Capture the exit status
STATUS=$?
if [ $STATUS -ne 0 ]; then
    echo "❌ SecureCodeAgent found issues. Push aborted."
    exit 1
else
    echo "✅ No critical security issues found."
    echo "Proceeding with push..."
fi
```

## Error Handling and Robustness
* Common issues include rate limits, timeouts, or malformed responses. By adding retry logic with exponential backoff, SecureCodeAgent can handle these gracefully.
```
def analyze_with_retry(sample_code, max_retries=3):
    for attempt in range(max_retries):
        try:
            resp = client.chat.completions.create(
                model="gpt-4o-mini",
                messages=[
                    {
                        "role": "system",
                        "content": "Analyze code for security Issues."
                    },
                    {
                        "role": "user",
                        "content": sample_code
                    }
                ],
                temperature=0
            )
            return resp.choices[0].message.content
        except RateLimitError:
            time.sleep(2 ** attempt)
        except Exception as e:
            logging.error(e)
```
* Multi-Language Support
* Performance Considerations - Running AI scans on every commit could feel heavy if not optimized. A practical approach, checks only files modified/commit or PR. Developers can also only block pushes.

## Complete  Code
```
import os, json
from pathlib import Path
from openai import AzureOpenAI

azure_endpoint = "https://eus2.openai.azure.com"
azure_api_key = "sdfsdffsdf"
azure_chat_deployment = "EGPT-4.1"
azure_chat_api_version= "2024-12-01-preview"

# Initialize Azure OpenAI client
client = AzureOpenAI(
    api_key=azure_api_key,
    api_version=azure_chat_api_version,
    azure_endpoint=azure_endpoint
)

def run_securecodeagent_txt(code_snippet):
    # Prompt instructs AI to return structured feedback
    response = client.chat.completions.create(
        model=azure_chat_deployment,
        messages=[
            {
                "role": "system",
                "content": (
                    "Analyze the given code and provide results in a table "
                    "with columns: 1) Detected Issue, 2) Suggested Secure "
                    "Code, 3) Reason for Change, 4) Criticality."
                )
            },
            {
                "role": "user",
                "content": code_snippet
            }
        ],
        temperature=0,
        top_p=1
    )

    # Extract response content
    response_content = (
        response.choices[0].message.content
        if response.choices else "No response generated."
    )
    return response_content

def run_securecodeagent_json(code_snippet):
    # Prompt instructs AI to return structured feedback
    response = client.chat.completions.create(
        model=azure_chat_deployment,
        messages=[
            {
                "role": "system",
                "content": (
                    "Analyze the given code and provide results in a JSON format "
                    "with keys: issues, suggestion, reason, criticality."
                )
            },
            {
                "role": "user",
                "content": code_snippet
            }
        ],
        temperature=0,
    )
    return json.loads(response.choices[0].message.content)


def analyze_with_retry(sample_code, max_retries=3):
    for attempt in range(max_retries):
        try:
            resp = client.chat.completions.create(
                model="gpt-4o-mini",
                messages=[
                    {
                        "role": "system",
                        "content": "Analyze code for security Issues."
                    },
                    {
                        "role": "user",
                        "content": sample_code
                    }
                ],
                temperature=0
            )
            return resp.choices[0].message.content
        except RateLimitError:
            time.sleep(2 ** attempt)
        except Exception as e:
            logging.error(e)

# Print security analysis
    
#file_name = "password_store_sample.py"
file_name = "fetch_data.py"
sample_code_path = Path(__file__).with_name(file_name)
sample_code = sample_code_path.read_text(encoding="utf-8")

'''
print("=== SecureCodeAgent Analysis ===")
response_content = run_securecodeagent_json(sample_code)
print(response_content)
'''

print("=== Structured Findings ===")
results = run_securecodeagent_json(sample_code)
print(json.dumps(results, indent=2))
```

Conclusion
