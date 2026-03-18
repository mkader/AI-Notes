# Fixing Security Issues in Seconds, Not Sprints, Powered by GenAI

* Developers typically discover vulnerabilities during pull request reviews, or using tools like CodeQL or SonarQube to flag them later—at a point when fixing them takes more time and costs more.
* What if developers could catch those issues instantly, right in their local environment, before even submitting a PR?
* That's the idea behind SecureCodeAgent, a GenAI-powered solution that shifts security left in the software development lifecycle.

* Generative AI (GenAI) is the broad technology category.
* OpenAI builds some of the leading GenAI models, and Microsoft's Azure OpenAI API provides enterprise access to those models.
* SecureCodeAgent uses this service as its GenAI engine.
  
* how SecureCodeAgent works, quick summary of secure code agent.
* <img width="400" height="450" alt="image" src="https://github.com/user-attachments/assets/4b630ae6-bd15-44cd-b684-83baad9e66d9" />

Why Catching Issues Early Matters
Fixing a vulnerability in production can cost 10–30 times more than fixing it during development. An SQL injection caught after deployment might mean weeks of remediation, patches, and potential downtime. The same issue flagged in a developer's editor or local environment could be resolved in minutes. Current tools like CodeQL and SonarQube detect vulnerabilities, but only after developers submit pull requests. When security issues are flagged, the development cycle stalls, PRs fail, developer's context-switch to fix code they wrote days earlier, and the process repeats until all gates pass.

This reactive approach creates friction. After the fact, developers learn about security issues where delivery timelines are extended due to security bottlenecks, and teams miss opportunities to build secure coding habits during active development.

GenAI agents solve this by moving security analysis into the coding process itself, providing real-time feedback that transforms security from a downstream gate into an upstream enabler of faster, safer development.

GenAI vs. Traditional Static Analysis: Cost and Capability Comparison
Although established tools like CodeQL and SonarQube dominate the static analysis landscape, GenAI-powered solutions like SecureCodeAgent offer compelling advantages in both cost and capability. Traditional tools can cost a lot: $19–$99 per user per month for CodeQL through GitHub Advanced Security (https://www.byteplus.com/en/topic/516886), and SonarQube pricing starts at $150 for the developer edition and €30 monthly for SonarQube Cloud's Team plan (100k lines of code) (https://revpilots.com/pricing/sonarqube-pricing). In contrast, Azure OpenAI API typically charges $5 per million input tokens and $15 per million output tokens (https://learn.microsoft.com/en-us/answers/questions/2107857/cost-rates-and-historical-cost-of-azure-openai-mod), translating to roughly $0.02–$0.05 per code analysis for average functions—potentially 10–50 times cheaper for small to medium teams.

Beyond cost, GenAI excels in areas where rule-based tools struggle: It explains why code is vulnerable rather than just flagging it, applies security knowledge across languages, adapts to new vulnerability patterns without manual rule updates, and provides natural language explanations that improve developer education. Although CodeQL and SonarQube provide comprehensive rule coverage and enterprise-grade reporting, GenAI tools enhance the developer experience through conversational feedback, instant explanations, and the ability to understand business context, making security more accessible and educational rather than merely punitive.

By moving security checks closer to where developers write code, they can:

Reduce delays caused by back-and-forth in PR reviews.
Avoid introducing vulnerabilities into shared branches.
Improve their own awareness of secure coding practices.
This approach forms the foundation of shift-left security—addressing problems as early as possible.

How SecureCodeAgent Works
SecureCodeAgent integrates seamlessly into a developer's existing workflow, as illustrated in Figure 2. Traditionally, security scans are performed after a Pull Request (PR) is raised using tools such as SonarQube or CodeQL. This approach delays feedback and often surfaces vulnerabilities late in the development cycle.

Figure 2: Flow diagram with existing Secure Code Tools vs. SecureCodeAgent
Figure 2: Flow diagram with existing Secure Code Tools vs. SecureCodeAgent
With SecureCodeAgent, this process shifts earlier—security checks are performed as soon as the code is ready for a PR. The agent leverages the Azure OpenAI API to conduct an AI-driven scan that intelligently identifies potential security and performance issues within the source code.

Each finding is presented in a structured, developer-friendly table, highlighting:

The specific insecure or inefficient line of code
A secure replacement suggestion
The rationale behind the recommendation
The severity level (high, medium, or low)
By providing instant insights before code review, SecureCodeAgent empowers developers to resolve issues proactively, reducing dependency on licensed tools like SonarQube and CodeQL. This not only saves time and cost but also ensures that code reaching the PR stage is already security-vetted, accelerating the overall deployment pipeline.

In essence, the proposed flow introduces GenAI (powered by SecureCodeAgent) as an early-stage safeguard—giving developers immediate visibility into vulnerabilities and promoting a more secure, efficient, and automated development lifecycle.

Step-by-Step Demonstration
Listing 1 is an example using Python. The code defines a simple password storage function that uses MD5 hashing, a known security weakness. SecureCodeAgent detects the issue and suggests a fix. SecureCodeAgent is demonstrated here as a Python script using the Azure OpenAI API. You can copy this code into your own projects and customize it to their workflow.

Listing 1: Invoking SecureCodeScan on Sample Code

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
Sample Output (AI-generated)
Figure 3 depicts the output generated running Listing 1 code.

Figure 3: Output generated by running SecureCodeScan on sample code
Figure 3: Output generated by running SecureCodeScan on sample code
This feedback gives developers instant visibility into security flaws before pushing their code.

Automating the Workflow
Running the script manually is useful, but the real power comes when SecureCodeAgent is integrated into Git workflows.

Pre-commit hook: Run scans before a commit is made.
Pre-push hook: Block insecure code from being pushed upstream.
CI/CD pipeline: Add SecureCodeAgent as a job in GitHub Actions or Jenkins for automated enforcement.
For example, a simple pre-push Git hook can automatically run the script and prevent insecure code from reaching the remote repository.

Git Pre-Push Hook with SecureCodeAgent
Save this as .git/hooks/pre-push in your repo and make it executable (chmod +x .git/hooks/pre-push). The following code depicts a bash script for invoking SecureCodeAgent.

 
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

Technical Deep Dive
Before jumping into the implementation, it helps to understand how SecureCodeAgent communicates with the model. The agent relies on structured JSON messages sent to the API, where each message defines the role (system, user) and content (instructions or code). The model then responds in JSON as well, which can be easily parsed back into Python objects.

This approach makes the workflow transparent:

Python code builds the request (JSON payload with model, messages, and parameters).
The API analyzes the snippet and returns structured JSON feedback.
Python extracts the response content from the JSON object.
This design shows how lightweight the integration can be—no external frameworks or SDKs are required beyond the API client itself.

With that context in mind, let's look at a pure Python example that demonstrates how JSON-based requests and responses power SecureCodeAgent in practice.

Advertisement

Pure Python Example
SecureCodeAgent can also return structured JSON output. For a code example, refer to Figure 4. This code makes it easy to integrate into existing testing frameworks or dashboards.

Figure 4: Secure Code Agent response in JSON format
Figure 4: Secure Code Agent response in JSON format
Error Handling and Robustness
When integrating with Azure OpenAI APIs, robustness is essential. Developers don't want their workflow to break if an API call fails. Common issues include rate limits, timeouts, or malformed responses. By adding retry logic with exponential backoff, SecureCodeAgent can handle these gracefully.

The following code is an example of retry logic for SecureCodeAgent:

 
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

This ensures that SecureCodeAgent remains non-disruptive: Even if the API is unavailable, developers see a clear message instead of a crash.

Multi-Language Support
Although the example shows Python, the approach works equally for Java, JavaScript, and other languages. Developers can pass any code snippet to the API, and SecureCodeAgent provides context-aware recommendations. A future enhancement could involve automatically detecting the language and applying tailored checks per ecosystem.

Performance Considerations
Running AI scans on every commit could feel heavy if not optimized. A practical approach is to limit checks to files modified in the current commit or PR. Developers can also configure SecureCodeAgent to only block pushes for high severity issues, and logging medium/low ones as warnings. This keeps security proactive but non-intrusive.

Managing False Positives
No static or AI-powered tool is perfect. SecureCodeAgent should allow developers to mark findings as acknowledged or ignored for cases where the code is acceptable. Storing these decisions in a configuration file (e.g., .securecodeignore) ensures that teams don't waste time triaging the same false positives repeatedly.

Benefits and Lessons Learned
By shifting security left, SecureCodeAgent helps:

Developers: Catch issues early without leaving their editor
AppSec teams: Focus on higher-level security rather than routine issues
Organizations: Reduce costly post-deployment fixes
Industry evidence demonstrates the value of this approach: Shifting security from the Build stage to the earlier Local Development and Code Commit stages improves security, while saving time, increasing productivity, and enhancing developer experience.

Conclusion
