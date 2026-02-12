# MSP Meeting Intelligence Workflow (n8n)

This n8n workflow automates the analysis of client meeting transcripts for Managed Service Providers (MSPs) - and able to be adapted for other professional service organizations. It leverages LLMs ( I used Ollama/Gemma3:12 while building it) to extract actionable insights, operational risks, business opportunities, and sentiment analysis, ultimately generating an executive-ready report in HTML and PDF formats.

Best of all, everything used can be self hosted - keeping your data in your hands.

![n8n screenshot](./readme-assets/screenshot-n8n.png)

## Features

- **Transcript Processing:** Accepts uploaded meeting transcripts in Markdown or plain text.
- **Segment Parsing:** Splits transcripts by timestamp and groups them into configurable time windows.
- **Action Item Extraction:** Identifies operational tasks, responsible parties, due dates, and priorities.
- **Risk Analysis:** Detects security gaps, process weaknesses, and operational vulnerabilities.
- **Opportunity Identification:** Extracts potential business value, service expansion, and efficiency improvements.
- **Sentiment Analysis:** Provides overall engagement, emotional tone, risk signals, and positive signals.
- **Executive Summary Generation:** Produces a structured summary suitable for senior leadership and board review.
- **Document Output:** Generates HTML and PDF reports with all extracted insights.

## Workflow Overview

The workflow consists of the following main stages:

1. **Chat Trigger**
   - Node: `When chat message received`
   - Starts the workflow when a transcript is uploaded.

2. **File Extraction**
   - Node: `Extract from File`
   - Converts uploaded Markdown or text files into plain text.

3. **Transcript Segmentation**
   - Nodes:
     - `Split by timestamp` – splits the transcript at each `[HH:MM:SS]`.
     - `Add Timestamps to Array Items` – calculates total seconds for each segment.
     - `Chunk by X minutes` – groups segments into time windows (default 5 minutes).

4. **LLM Processing**
   - **Action Items**
     - Node: `Action Item and Task Extraction`
     - Extracts operational tasks and assignees.
   - **Risk Extraction**
     - Node: `Risk Extraction`
     - Identifies security and operational risks.
   - **Opportunity Extraction**
     - Node: `Opportunity Extraction`
     - Finds business value and growth opportunities.
   - **Sentiment Analysis**
     - Node: `Sentiment Analysis`
     - Analyzes overall sentiment, engagement, and emotional tone.

5. **Data Parsing & Aggregation**
   - Multiple `Parse JSON, Build New Object` nodes consolidate LLM outputs.
   - `Aggregate` nodes combine all tasks, risks, opportunities, and sentiment data.

6. **Executive Summary**
   - Node: `Executive Summary`
   - Synthesizes extracted data into a concise summary for senior leadership.

7. **Document Generation**
   - Node: `DocumentGenerator`
   - Creates HTML reports with all insights.
   - `Base64 Encode HTML` and `Base64 -> File` prepare the file for output.
   - `HTML -> PDF (Gotenberg)` converts HTML to a PDF report.

8. **Internal Knowledge Base Article (Optional)**
   - Node: `Ollama Model4`
   - Produces an internal guidance article summarizing operational takeaways, lessons learned, and training recommendations.

## Supported File Types

- `text/markdown` (.md)
- `text/plain` (.txt)

## Output

The workflow produces:

1. **Executive Summary Report** (HTML and PDF) including:
   - Risk level
   - Meeting overview
   - Key decisions
   - Identified risks
   - Identified opportunities
   - Financial implications
   - Operational concerns
   - Recommended next steps
   - Escalations required

2. **Detailed Extracted Data**
   - Tasks, risks, and opportunities per transcript segment
   - Sentiment analysis with emotional tones, risk signals, positive signals, and notable quotes

3. **Internal Knowledge Base Article**
   - Operational guidance and lessons learned
   - Recommended training or communication steps

## Configuration

- **Chunking Window:** Adjust the `windowSize` variable in the `Chunk by X minutes` node to change segment grouping.
- **LLM Model:** Ollama’s `gemma3:12b` is used by default, but other models can be configured in the LLM nodes.
- **PDF Generation:** Requires a Gotenberg instance for HTML-to-PDF conversion. Update the URL in `HTML -> PDF (Gotenberg)` as needed.

## Usage

1. Upload a transcript file via the chat trigger.
2. The workflow automatically:
   - Splits and chunks the transcript
   - Extracts tasks, risks, opportunities
   - Analyzes sentiment
   - Aggregates and generates executive-ready output
3. Download the generated HTML or PDF report from the workflow output.

## Notes

- Only explicit information from the transcript is extracted; the workflow does **not** infer tasks or risks.
- Sentiment analysis is designed for MSP client interactions and focuses on engagement, emotional tone, and risk indicators.
- Ensure that LLM credentials and Gotenberg endpoints are correctly configured for the workflow to function.

## References

- [n8n Documentation](https://docs.n8n.io)
- [Ollama API](https://ollama.com)
- [Gotenberg HTML to PDF](https://thecodingmachine.github.io/gotenberg/)
