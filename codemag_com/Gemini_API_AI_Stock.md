# AI stock analyst
## Tech Stack
  * Gemini API - with API Key.
  * client-server application
  * SPA using JavaScript and HTML, Tailwind CSS
  * <img width="600" height="350" alt="image" src="https://github.com/user-attachments/assets/186a1745-bc2f-4be6-8ce5-88c01cf6dd5a" />


## Start Building
```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>AI Stock Analyst</title>
        <script src="https://cdn.tailwindcss.com"></script>
        <link href="https://fonts.googleapis.com/css2? family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
        <style>
        </style>
    </head>
    <body>
        <div class="container-card">
        </div>
        <script>
        </script>
    </body>
</html>
```
## UI
```
<div class="container-card">
    <h1>AI Stock Analyst</h1>
    <p>Analyze a stock's latest performance using real-time, verifiable data via the Gemini API.</p>

    <!-- Input and Action -->
    <div>
        <input type="text" id="tickerInput" placeholder="Enter stock ticker (e.g., GOOGL, MSFT)"  value="GOOGL">
        <button id="analyzeButton" onclick="analyzeStock()">Analyze Stock</button>
    </div>

    <!-- Loading Indicator -->
    <div id="loadingIndicator">
        <div>
            <svg ..>
                <circle ..></circle>
                <path ..></path>
            </svg>
            Analyzing...
            </div>
            </div>

    <!-- Results Area -->
    <div id="resultsArea">
        <div id="analysisOutput">
            <h3>Analysis</h3>
            <p id="responseText">Analysis will appear here after you  click "Analyze Stock".</p>
        </div>

        <div id="citationOutput">
            <h3>Source Citations</h3>
                <ul id="sourcesList">
                    <li id="initialSourceText">Sources will be listed here, providing links to verify the AI's claims.</li>
                </ul>
        </div>
    </div>
</div>
```
## Application Logic
```
const API_URL_BASE = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent';
const apiKey = "";
const apiUrl = `${API_URL_BASE}?key=${apiKey}`;
const maxRetries = 3;
const initialDelay = 1000;
 
async function analyzeStock() {
  // Get input value and UI elements
  const ticker = document
      .getElementById('tickerInput')
      .value
      .trim()
      .toUpperCase();
  
  // ... other element references ...
  if (!apiKey || apiKey === "YOUR_GEMINI_API_KEY_HERE") {
      // Handle API Key error
      return;
  }
}
// ... disable button, show loading spinner ...
```

## system prompt
```
“You are a world-class financial analyst. ... CRITICAL INSTRUCTION: You MUST use the numerical citation format [1], [2], [3], etc., IMMEDIATELY after every sentence that contains factual information taken from the search results to ensure verification. Every factual statement must be followed by a citation. Do not use any external knowledge.”
const systemPrompt = `..`;
```

## userQuery
```
“What is the latest news, recent performance, and key analyst sentiment for the stock ticker ${ticker}? Summarize this in one paragraph.”
const userQuery = `..`;
```
## define the payload. 
```
const payload = {
    contents: [{ parts: [{ text: userQuery }] }],
    // CRITICAL: Enable Google Search grounding
    tools: [{ google_search: {} }],
    systemInstruction: {parts: [{ text: systemPrompt }]}
};
```

## Making network calls with exponential backoff
```
let attempt = 0;

while (attempt < maxRetries) {
    try {
        const response = await fetch(apiUrl, fetchOptions);
        // ... Check response.ok and process results ...

        break; // Success! Exit the loop.
    } catch (error) {
        attempt++;
        // ... If max retries, fail.
        // Otherwise, calculate delay and wait ...
    }
}
const fetchOptions = {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload)
};
```
## Response - information within the groundingMetadata object, specifically in the groundingChunks array. 
* The first parameter is text, which is the generated text from the model, e.g., "The stock is up [1] by 5% [2].".
* The second parameter is sources, which is an array of source objects. Finally, it returns an HTML string with citations as links.
```
Listing 7: Extracting grounding metadata
// In the analyzeStock function, after fetching the result:
const candidate = result?.candidates?.[0];
let sources = [];
const groundingMetadata = candidate.groundingMetadata;
if (groundingMetadata && groundingMetadata.groundingChunks) {
    sources = groundingMetadata.groundingChunks.map(attribution => ({
        uri: attribution.web?.uri,
        title: attribution.web?.title,
    }))
    // Filter out invalid sources
    .filter(source => source.uri && source.title);
}

function formatGroundedText(text, sources) {
    // Regex to find citation markers like [1], [2], etc.
    const citationRegex = /\[(\d+)\]/g;

    return text.replace(citationRegex, (match, index) => {
        const sourceIndex = parseInt(index) - 1;
        const source = sources[sourceIndex];

        if (source && source.uri) {
            // Create an inline citation link
            return `<a href="${source.uri}" 
                target="_blank" 
                title="${source.title || 'Source'}" 
                class="citation inline-block ml-1"><sup>[${index}]</sup></a>`;
        }

        // Return original text if source is invalid
        return match;
    });
}
```
	
## displays the sources
```
Listing 9: Rendering the final output
// In the analyzeStock function, after formatGroundedText:
if (sources.length > 0) {
    sourcesList.innerHTML = '';
    sources.forEach((source, index) => {
        // code to create <li> with [Index + 1]
        // and the clickable URL ...
    });
}
```

* Complete Code
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>AI Stock Analyst</title>
	<style>
		:root {
			--bg-1: #0b1424;
			--bg-2: #1a2d4b;
			--panel: #0e1d32;
			--panel-border: #24456f;
			--accent: #4fd1c5;
			--accent-2: #7dd3fc;
			--text: #e7f0ff;
			--muted: #9db1d1;
			--danger: #ff8888;
			--shadow: 0 14px 35px rgba(2, 10, 25, 0.45);
		}

		* {
			box-sizing: border-box;
		}

		body {
			margin: 0;
			min-height: 100vh;
			font-family: "Trebuchet MS", "Segoe UI", sans-serif;
			color: var(--text);
			background:
				radial-gradient(circle at 8% 12%, rgba(125, 211, 252, 0.14), transparent 32%),
				radial-gradient(circle at 92% 88%, rgba(79, 209, 197, 0.16), transparent 35%),
				linear-gradient(150deg, var(--bg-1), var(--bg-2));
			display: grid;
			place-items: center;
			padding: 20px;
		}

		.card {
			width: min(980px, 100%);
			background: linear-gradient(165deg, rgba(14, 29, 50, 0.95), rgba(12, 26, 45, 0.9));
			border: 1px solid var(--panel-border);
			border-radius: 18px;
			box-shadow: var(--shadow);
			overflow: hidden;
			animation: fadeInUp 500ms ease-out;
		}

		.header {
			padding: 20px 22px 10px;
			border-bottom: 1px solid rgba(125, 211, 252, 0.22);
		}

		.title {
			margin: 0;
			font-size: clamp(1.4rem, 2vw, 2rem);
			letter-spacing: 0.02em;
		}

		.subtitle {
			margin: 8px 0 0;
			color: var(--muted);
			line-height: 1.5;
			font-size: 0.97rem;
		}

		.controls {
			padding: 18px 22px;
			display: grid;
			grid-template-columns: 1fr auto;
			gap: 12px;
		}

		input[type="text"] {
			width: 100%;
			border: 1px solid #2f5f90;
			background: #0a1830;
			color: var(--text);
			border-radius: 10px;
			padding: 12px 14px;
			font-size: 0.98rem;
			transition: border-color 180ms ease, box-shadow 180ms ease;
		}

		input[type="text"]:focus {
			outline: none;
			border-color: var(--accent-2);
			box-shadow: 0 0 0 3px rgba(125, 211, 252, 0.25);
		}

		button {
			border: 0;
			border-radius: 10px;
			padding: 0 18px;
			background: linear-gradient(120deg, #14b8a6, #22d3ee);
			color: #062232;
			font-weight: 700;
			cursor: pointer;
			transition: transform 120ms ease, filter 180ms ease;
		}

		button:hover {
			filter: brightness(1.07);
		}

		button:active {
			transform: translateY(1px);
		}

		button:disabled {
			opacity: 0.6;
			cursor: not-allowed;
		}

		.status {
			display: none;
			align-items: center;
			gap: 10px;
			padding: 0 22px 16px;
			color: var(--muted);
			font-size: 0.94rem;
		}

		.status.visible {
			display: flex;
		}

		.spinner {
			width: 16px;
			height: 16px;
			border: 2px solid rgba(125, 211, 252, 0.25);
			border-top-color: var(--accent-2);
			border-radius: 50%;
			animation: spin 800ms linear infinite;
		}

		.grid {
			padding: 0 22px 22px;
			display: grid;
			grid-template-columns: 1.3fr 1fr;
			gap: 14px;
		}

		.panel {
			background: rgba(6, 19, 36, 0.72);
			border: 1px solid rgba(68, 113, 158, 0.45);
			border-radius: 12px;
			padding: 14px;
			min-height: 200px;
		}

		h2 {
			margin: 0 0 10px;
			font-size: 1rem;
			color: var(--accent-2);
			letter-spacing: 0.01em;
		}

		.response {
			margin: 0;
			line-height: 1.6;
			color: #eef6ff;
			word-wrap: break-word;
		}

		.response .citation {
			color: var(--accent);
			text-decoration: none;
			font-weight: 700;
		}

		.response .citation:hover {
			text-decoration: underline;
		}

		.sources {
			margin: 0;
			padding-left: 18px;
			color: var(--muted);
			line-height: 1.5;
		}

		.sources li {
			margin-bottom: 8px;
		}

		.sources a {
			color: #9de7ff;
			text-decoration: none;
		}

		.sources a:hover {
			text-decoration: underline;
		}

		.error {
			color: var(--danger);
		}

		@keyframes spin {
			to {
				transform: rotate(360deg);
			}
		}

		@keyframes fadeInUp {
			from {
				opacity: 0;
				transform: translateY(10px);
			}
			to {
				opacity: 1;
				transform: translateY(0);
			}
		}

		@media (max-width: 860px) {
			.controls {
				grid-template-columns: 1fr;
			}

			button {
				height: 44px;
			}

			.grid {
				grid-template-columns: 1fr;
			}
		}
	</style>
</head>
<body>
	<main class="card" aria-live="polite">
		<section class="header">
			<h1 class="title">AI Stock Analyst</h1>
			<p class="subtitle">Enter a ticker symbol to fetch recent market context, news pulse, and analyst sentiment with grounded citations.</p>
		</section>

		<section class="controls">
			<input type="text" id="tickerInput" placeholder="Ticker (example: MSFT, NVDA, GOOGL)" value="GOOGL" maxlength="10" autocomplete="off">
			<button id="analyzeButton" type="button">Analyze Stock</button>
		</section>

		<div id="loadingIndicator" class="status" role="status" aria-label="Loading">
			<span class="spinner" aria-hidden="true"></span>
			<span>Analyzing with live web grounding...</span>
		</div>

		<section class="grid">
			<article class="panel">
				<h2>Analysis</h2>
				<p id="responseText" class="response">Analysis appears here after you click Analyze Stock.</p>
			</article>

			<article class="panel">
				<h2>Source Citations</h2>
				<ul id="sourcesList" class="sources">
					<li>Sources will appear here for verification.</li>
				</ul>
			</article>
		</section>
	</main>

	<script>
		const API_URL_BASE = "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent";
		const API_KEY = "sdfsdfdsfdfsdfsd";
		const MAX_RETRIES = 3;
		const INITIAL_DELAY_MS = 1000;

		const systemPrompt = "You are a world-class financial analyst. Use only grounded web search evidence and cite factual claims with numeric markers like [1], [2], [3] directly after each factual sentence.";

		const tickerInput = document.getElementById("tickerInput");
		const analyzeButton = document.getElementById("analyzeButton");
		const loadingIndicator = document.getElementById("loadingIndicator");
		const responseText = document.getElementById("responseText");
		const sourcesList = document.getElementById("sourcesList");

		analyzeButton.addEventListener("click", analyzeStock);
		tickerInput.addEventListener("keydown", (event) => {
			if (event.key === "Enter") {
				analyzeStock();
			}
		});

		function sleep(ms) {
			return new Promise((resolve) => setTimeout(resolve, ms));
		}

		function escapeHtml(value) {
			return String(value)
				.replace(/&/g, "&amp;")
				.replace(/</g, "&lt;")
				.replace(/>/g, "&gt;")
				.replace(/\"/g, "&quot;")
				.replace(/'/g, "&#039;");
		}

		function normalizeTicker(raw) {
			return raw.trim().toUpperCase().replace(/\s+/g, "");
		}

		function isValidTicker(ticker) {
			return /^[A-Z0-9.-]{1,10}$/.test(ticker);
		}

		function renderError(message) {
			responseText.classList.add("error");
			responseText.textContent = message;
			sourcesList.innerHTML = "<li>None</li>";
		}

		function setLoading(isLoading) {
			analyzeButton.disabled = isLoading;
			tickerInput.disabled = isLoading;
			loadingIndicator.classList.toggle("visible", isLoading);
			if (isLoading) {
				responseText.classList.remove("error");
				responseText.textContent = "Working on your request...";
				sourcesList.innerHTML = "<li>Collecting sources...</li>";
			}
		}

		function extractSources(result) {
			const chunks = result?.candidates?.[0]?.groundingMetadata?.groundingChunks || [];
			const seen = new Set();
			const sources = [];

			for (const item of chunks) {
				const uri = item?.web?.uri;
				const title = item?.web?.title || "Untitled source";

				if (!uri || seen.has(uri)) {
					continue;
				}

				seen.add(uri);
				sources.push({ uri, title });
			}

			return sources;
		}

		function renderSources(sources) {
			sourcesList.innerHTML = "";

			if (!sources.length) {
				const li = document.createElement("li");
				li.textContent = "No grounding sources returned by the model.";
				sourcesList.appendChild(li);
				return;
			}

			sources.forEach((source, index) => {
				const li = document.createElement("li");
				const link = document.createElement("a");
				link.href = source.uri;
				link.target = "_blank";
				link.rel = "noopener noreferrer";
				link.textContent = `[${index + 1}] ${source.title}`;
				li.appendChild(link);
				sourcesList.appendChild(li);
			});
		}

		function formatGroundedText(text, sources) {
			const escaped = escapeHtml(text).replace(/\n/g, "<br>");
			return escaped.replace(/\[(\d+)\]/g, (match, indexText) => {
				const index = Number(indexText);
				const source = sources[index - 1];

				if (!source || !source.uri) {
					return match;
				}

				const safeTitle = escapeHtml(source.title || "Source");
				const safeHref = escapeHtml(source.uri);
				return `<a class="citation" href="${safeHref}" target="_blank" rel="noopener noreferrer" title="${safeTitle}"><sup>[${index}]</sup></a>`;
			});
		}

		async function requestWithRetry(apiUrl, payload) {
			const options = {
				method: "POST",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify(payload)
			};

			for (let attempt = 0; attempt < MAX_RETRIES; attempt += 1) {
				try {
					const response = await fetch(apiUrl, options);

					if (!response.ok) {
						const body = await response.json().catch(() => ({}));
						const message = body?.error?.message || `Request failed (${response.status})`;
						const retryable = response.status === 429 || response.status >= 500;

						if (!retryable || attempt === MAX_RETRIES - 1) {
							throw new Error(message);
						}
					} else {
						return await response.json();
					}
				} catch (error) {
					if (attempt === MAX_RETRIES - 1) {
						throw error;
					}
				}

				const delay = INITIAL_DELAY_MS * (2 ** attempt);
				await sleep(delay);
			}

			throw new Error("Unexpected retry failure.");
		}

		async function analyzeStock() {
			const ticker = normalizeTicker(tickerInput.value);

			if (!isValidTicker(ticker)) {
				renderError("Enter a valid ticker symbol (letters, numbers, dot, or dash). Example: MSFT, BRK.B, RACE.");
				return;
			}

			if (!API_KEY) {
				renderError("Missing API key. Set API_KEY in this file before running analysis.");
				return;
			}

			const apiUrl = `${API_URL_BASE}?key=${encodeURIComponent(API_KEY)}`;
			const userQuery = `What is the latest news, recent price performance, and key analyst sentiment for ticker ${ticker}? Provide one concise paragraph and cite all factual claims.`;

			const payload = {
				contents: [{ parts: [{ text: userQuery }] }],
				tools: [{ google_search: {} }],
				systemInstruction: { parts: [{ text: systemPrompt }] }
			};

			setLoading(true);

			try {
				const result = await requestWithRetry(apiUrl, payload);
				const candidate = result?.candidates?.[0];
				const rawText = candidate?.content?.parts?.map((part) => part.text || "").join("\n").trim() || "No analysis text returned.";
				const sources = extractSources(result);

				responseText.classList.remove("error");
				responseText.innerHTML = formatGroundedText(rawText, sources);
				renderSources(sources);
			} catch (error) {
				const message = error instanceof Error ? error.message : String(error);
				renderError(`Analysis failed: ${message}`);
			} finally {
				setLoading(false);
			}
		}
	</script>
</body>
</html>
```
