# Tutorium_online_marketing_2
Praxisnahe Anwendung eines Agenten, sodass Studierende einen besseres Verständnis für die Wichtigkeit und den Nutzen des Agenten im Bereich Online Marketing haben
Case Study: Automated Research Agent for Market Intelligence
This repository contains a lecture-ready implementation of a Sequential Multi-Agent System. Using the Google AI SDK and Gemini, we simulate a professional research workflow where specialized agents collaborate to generate a market intelligence report.

#1. Project Overview
The goal of this case study is to automate the process of gathering, analyzing, and summarizing information on emerging technologies (e.g., "Green Hydrogen in the EU").

The Sequential Workflow:

The Scout (Search Agent): Uses search tools to identify high-quality sources and URLs.

The Analyst (Research Agent): Processes the raw text, filters for key facts, and checks for inconsistencies.

The Editor (Writer Agent): Synthesizes the findings into a structured Markdown report.

2. Technical Architecture
The implementation leverages Function Calling and Structured Outputs. The state is passed sequentially through the chain:

State 
i+1
​	
 =Agent 
i
​	
 (State 
i
​	
 ,Tools)
Key Components:

Model: gemini-1.5-flash (for speed) or gemini-1.5-pro (for complex reasoning).

Tools: Custom Python functions for web search and URL content extraction.

Prompting: Role-based system instructions to enforce professional personas.

3. Code Example (Notebook Snippet)
Below is a simplified version of how the agents are orchestrated in the provided Jupyter Notebooks.

Python
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
4. Lecture Content & Exercises
File	Description	Learning Objective
01_environment_setup.ipynb	Installation & API Keys	Learn to manage .env and Google Cloud projects.
02_tool_definition.ipynb	Building Search Tools	Understand Function Calling and API integration.
03_sequential_workflow.ipynb	The Full Pipeline	Implement state management between agents.
04_evaluation.ipynb	Quality Control	Learn to identify and mitigate "Hallucinations".
Exercises for Students:

Prompt Tuning: Modify the Analyst Agent to focus specifically on financial risks.

Error Handling: Implement a check that forces the agent to retry if no URLs are found.

Format Challenge: Change the Writer Agent output to a slide-deck format (JSON).

5. Setup Instructions
Clone the Repo:

Bash
git clone https://github.com/your-username/automated-research-agent.git
Install Dependencies:

Bash
pip install -r requirements.txt
Configure API Key: Create a .env file and add: GOOGLE_API_KEY=your_key_here
