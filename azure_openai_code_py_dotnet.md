```
from openai import OpenAI

'''
# working 1
endpoint = "https://p-t-e.openai.azure.com/openai/v1"
deployment_name = "gpt-5"
api_key = "123"

# working 2
endpoint = "https://eus2.openai.azure.com/openai/v1"
deployment_name = "EGPT-4.1"
api_key = "123"

# working 3
endpoint = "https://dv2.openai.azure.com/openai/v1"
deployment_name = "gpt-5-mini"
api_key = "123"
'''

client = OpenAI(
    base_url=endpoint,
    api_key=api_key
)

completion = client.chat.completions.create(
    model=deployment_name,
    messages=[
        {
            "role": "user",
            "content": "What is the capital of France?",
        }
    ],
)

print(completion.choices[0].message)
```
```
--------------------
.net code

var endpoint = "https://p-t-e.openai.azure.com/openai/v1";
var deployment_name = "gpt-5";
var api_key = "123";

HttpClient _httpClient = new HttpClient()
{
    BaseAddress = new Uri(endpoint.TrimEnd('/') + "/"),
};

_httpClient.DefaultRequestHeaders.Add("api-key", api_key);

var requestBody = new Dictionary<string, object>
{
  //["max_completion_tokens"] = 3000,
    ["model"] = deployment_name,
    ["messages"] = new[]
    {
        new { role = "user", content = "What is the capital of France?" }
    }
};

// Reasoning models (gpt-5*) spend this budget on hidden reasoning tokens, so an
// unset value lets the service apply its own default instead of truncating the answer.
if (int.TryParse(_configuration["AzureOpenAI:MaxCompletionTokens"], out var maxCompletionTokens))
{
    requestBody["max_completion_tokens"] = maxCompletionTokens;
}

var payload = JsonSerializer.Serialize(requestBody);
using var content = new StringContent(payload, Encoding.UTF8, "application/json");

try
{
    var response = await _httpClient.PostAsync("chat/completions", content);
    var json = await response.Content.ReadAsStringAsync();
    if (!response.IsSuccessStatusCode)
    {
        console.writeline($"error:azure-openai-{(int)response.StatusCode}:{json}");
    }

    using var document = JsonDocument.Parse(json);
    var choice = document.RootElement.GetProperty("choices")[0];
    var message = choice.GetProperty("message").GetProperty("content").GetString();
    if (string.IsNullOrEmpty(message))
    {
        var finishReason = choice.TryGetProperty("finish_reason", out var reason) ? reason.GetString() : "unknown";
        console.writeline($"error:azure-openai-empty-content:finish_reason={finishReason}");
    }

    console.writeline(message);
}
catch (Exception ex)
{
    console.writeline($"error:azure-openai-unavailable:{ex.Message}");
}
```

* gpt-5 is a reasoning model. It generates hidden reasoning tokens before the visible answer, and those tokens count against max_completion_tokens. Your .NET code hard-coded: ``` max_completion_tokens = 3000, ```

* With a long prompt, the model burns the entire 3000-token budget on reasoning, hits the cap before emitting any visible text, and returns finish_reason: "length" with content: "".

* Your Python code never sets that parameter, so the service applies its own (much larger) default — which is why all three configs work there.

* Why only config 1 failed:

| Config	| Model	| Reasoning model?	| 3000-token cap
| - | - | - | - | 
| 1	| gpt-5	| Yes	| Consumed by reasoning → empty
| 2	| EGPT-4.1	| No	| Fine
| 3	| gpt-5-mini	| Yes	| Cheaper reasoning, fit under the cap
