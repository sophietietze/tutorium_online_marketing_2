# Case Study: Automated Research Agent for Market Intelligence
This repository contains a lecture-ready implementation of a Sequential Multi-Agent System. Using the Google AI SDK and Gemini, we simulate a professional research workflow where specialized agents collaborate to generate a market intelligence report.

# Project Overview
The goal of this case study is to automate the process of gathering, analyzing, and summarizing information on emerging technologies (e.g., "Green Hydrogen in the EU").

The Sequential Workflow:

The Scout (Search Agent): Uses search tools to identify high-quality sources and URLs.

The Analyst (Research Agent): Processes the raw text, filters for key facts, and checks for inconsistencies.

The Editor (Writer Agent): Synthesizes the findings into a structured Markdown report.

# Technical Architecture
The implementation leverages Function Calling and Structured Outputs. The state is passed sequentially through the chain:

Key Components:

Model: gemini-1.5-flash (for speed) or gemini-1.5-pro (for complex reasoning).

Tools: Custom Python functions for web search and URL content extraction.

Prompting: Role-based system instructions to enforce professional personas.

> [!IMPORTANT]
> You need to have uv installed on your machine (go to [this repo](https://github.com/kirenz/uv-setup) for installation instructions).
> You also need a Google AI API Key.

Step-by-step instructions
Clone the repository

```bash
git clone https://github.com/kirenz/google-adk-first-agent.git
cd automated-research-example
Sync the Python environment
```

```bash
uv sync
```
This installs all required packages (Google AI SDK, dotenv, etc.) in an isolated environment managed by uv.

Prepare your environment variables

```bash
cp .env.example .env
```
Configure your API Key Open the new .env file in VS Code and replace the placeholder with your Google AI Studio API Key: GOOGLE_API_KEY=your_actual_key_here

Run the Research Agent

```bash
uv run python research_agent.py
```
The script will initialize the Scout Agent, perform a search, hand over the data to the Analyst Agent, and save a research_report.md to your folder.

Explore the Notebooks Open the notebooks/ folder to walk through the step-by-step logic of how agents communicate and how "Function Calling" works in detail.

Files
```bash
research_agent.py
```
– The main script that orchestrates the handoff between the Scout and the Analyst.

```bash
.env.example
```
– Template for your Google API credentials.

```bash
pyproject.toml
```
– Dependency definition (includes google-generativeai).

```bash
notebooks/03_sequential_workflow.ipynb
```
– Interactive version of the agent logic for classroom use.

Python packages used
```bash
google-generativeai
```
 – The official SDK to interact with Gemini 1.5 Pro and Flash models.

```bash
python-dotenv
```
– Loads your API keys securely from the .env file.

```bash
rich – (Optional)
```
Used for beautiful terminal output during the agent's "thinking" process.


3. Code Example (Notebook Snippet)
Below is a simplified version of how the agents are orchestrated in the provided Jupyter Notebooks.

Python
```bash
import google.generativeai as genai
import os

# 1. Setup Tools
def web_search(query: str):
    """Simulates a search tool. In production, use Google Custom Search API."""
    return f"Search Results for {query}: [Link 1: Info on H2], [Link 2: Policy Paper]"

# 2. Define the Agent Roles
scout_instruction = "You are a Research Scout. Find the best 3 sources for the given topic."
analyst_instruction = "You are a Market Analyst. Extract facts and figures from the provided sources."

# 3. Execution (Sequential Chain)
model = genai.GenerativeModel('gemini-1.5-flash')

# Step 1: Search
search_response = model.generate_content(f"{scout_instruction} Topic: Green Hydrogen")

# Step 2: Analysis (Context handoff)
final_report = model.generate_content(f"{analyst_instruction} Based on this data: {search_response.text}")

print(final_report.text)
```
1. Setup & Authentication
Zuerst installieren wir die notwendigen Bibliotheken und laden den API-Key.

Python
# Installation (falls nötig)
# !pip install -q -U google-generativeai python-dotenv

import google.generativeai as genai
import os
from dotenv import load_dotenv

# API Key laden
load_dotenv()
genai.configure(api_key=os.getenv("GOOGLE_API_KEY"))
2. Tool Definition (Die Fähigkeiten)
Agenten benötigen Werkzeuge, um mit der Außenwelt zu interagieren. Hier definieren wir ein Mock-Tool für die Suche.

Python
def web_search_tool(query: str):
    """
    Sucht nach aktuellen Marktinformationen. 
    In einer Produktionsumgebung würde hier die Google Search API aufgerufen werden.
    """
    # Mock-Daten für die Vorlesung
    results = {
        "Green Hydrogen": "EU investiert 430 Mrd. Euro bis 2030. Hauptakteure: Thyssenkrupp, ITM Power.",
        "Solar Trends": "Perowskit-Solarzellen erreichen neuen Effizienzrekord von 25%."
    }
    return results.get(query, "Keine spezifischen Daten gefunden, aber Trends deuten auf Wachstum hin.")

# Wir verpacken die Tools für Gemini
tools = [web_search_tool]
3. Agent 1: Der "Scout" (Recherche)
Der erste Agent im sequentiellen Ablauf. Er ist darauf programmiert, Informationen zu sammeln.

Python
# Konfiguration des ersten Agenten
scout_model = genai.GenerativeModel(
    model_name='gemini-1.5-flash',
    tools=tools,
    system_instruction="""Du bist ein Market Research Scout. 
    Deine Aufgabe ist es, präzise Fakten zu einem Thema zu finden. 
    Nutze das 'web_search_tool', um Daten zu sammeln. 
    Gib eine Liste von Rohfakten zurück."""
)

# Chat starten
chat_scout = scout_model.start_chat(enable_automatic_function_calling=True)
response_scout = chat_scout.send_message("Wie sieht der Markt für Grünen Wasserstoff in der EU aus?")

print("--- SCOUT ERGEBNIS ---")
print(response_scout.text)
4. Agent 2: Der "Analyst" (Synthese)
Dieser Agent erhält die Rohdaten des Scouts und erstellt daraus einen strukturierten Bericht. Dies ist der sequentielle Schritt.

Python
analyst_model = genai.GenerativeModel(
    model_name='gemini-1.5-flash',
    system_instruction="""Du bist ein Senior Business Analyst. 
    Du erhältst Rohdaten von einem Scout. Deine Aufgabe ist es, diese in einen 
    professionellen Management-Bericht zu verwandeln. 
    Nutze Markdown-Header, Bullet Points und eine SWOT-Analyse."""
)

# Handoff: Die Antwort von Agent 1 wird der Input für Agent 2
prompt_analyst = f"Analysiere die folgenden Rohdaten und erstelle einen Bericht: {response_scout.text}"
response_analyst = analyst_model.generate_content(prompt_analyst)
