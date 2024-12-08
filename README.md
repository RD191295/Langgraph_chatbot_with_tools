:robot: LangGraph Chatbot with Tools

This repository demonstrates how to build a powerful chatbot using the LangGraph framework, integrating external tools like Arxiv and Wikipedia for enhanced information retrieval. The chatbot can answer user queries by pulling real-time data from Arxiv research papers and Wikipedia articles, and it is designed to perform specific tasks such as code generation.
____________________________________________________________________________________________________________________________________________________________________________

:sparkles: Features

LangGraph Framework: Build and manage state-based workflows for chatbots.
Arxiv Integration: Retrieve academic papers and articles from Arxiv based on user queries.
Wikipedia Integration: Fetch relevant, real-time information from Wikipedia.
Groq LLM Integration: Utilizes the powerful Gemma2-9b-It language model from Groq.
Tool-based Interaction: Access external APIs like Arxiv and Wikipedia for more accurate and context-aware responses.
Customizable: Easily add or modify tools to fit your use case.
_____________________________________________________________________________________________________________________________________________________________________________

:floppy_disk: Installation
To set up and run the LangGraph Chatbot with tools, follow the steps below:

1. Clone the repository
```git
git clone https://github.com/yourusername/langgraph-chatbot.git
cd langgraph-chatbot
```

2. Install the required dependencies
Make sure you have Python 3.8+ installed. Then, install the dependencies:
```python
pip install langgraph langsmith langchain langchain_groq langchain_community
pip install arxiv wikipedia
```

3. Set up Groq API Key
To use the Groq language model, you’ll need an API key. Add your API key as an environment variable or use userdata in Jupyter environments.
_____________________________________________________________________________________________________________________________________________________________________________

:gear: Usage
1. Define the Tools
The chatbot integrates with Arxiv and Wikipedia tools. Here's how to set them up:
```python
from langchain_community.utilities import ArxivAPIWrapper, WikipediaAPIWrapper
from langchain_community.tools import ArxivQueryRun, WikipediaQueryRun

# Arxiv Tool
arxiv_wrapper = ArxivAPIWrapper(top_k_results=1, doc_content_chars_max=300)
arxiv_tool = ArxivQueryRun(api_wrapper=arxiv_wrapper)

# Wikipedia Tool
wikipedia_wrapper = WikipediaAPIWrapper(top_k_results=1, doc_content_chars_max=300)
wikipedia_tool = WikipediaQueryRun(api_wrapper=wikipedia_wrapper)
```

2. Set up the State
The chatbot maintains its state in a dictionary that tracks the conversation:
```python
from typing import Annotated
from typing_extensions import TypedDict
from langgraph.graph.message import add_messages

class State(TypedDict):
    messages: Annotated[list, add_messages]
```

3. Build the State Graph
In LangGraph, a StateGraph defines the flow of the chatbot, handling tools, and actions. Here’s how to set up the graph:
```python
from langgraph.graph import StateGraph, START, END
from langgraph.prebuilt import ToolNode, tools_condition

# Initialize graph builder
graph_builder = StateGraph(State)

# Define chatbot function
def chatbot(state: State):
    state['messages'].append(llm_with_tools.invoke(state['messages']))
    return state  # Return the updated state

graph_builder.add_node("chatbot", chatbot)
graph_builder.add_edge(START, "chatbot")

# Adding tool node for tools like Wikipedia and Arxiv
tool_node = ToolNode(tools=tools)
graph_builder.add_node("tools", tool_node)

# Define conditional edges based on tool usage
graph_builder.add_conditional_edges("chatbot", tools_condition)
graph_builder.add_edge("tools", "chatbot")
graph_builder.add_edge("chatbot", END)

# Compile the graph
graph = graph_builder.compile()
```

4. Visualize Graph
You can Visualize Graph
```python
# display graph builded earlier
from IPython.display import Image,display

try:
  display(Image(graph.get_graph().draw_mermaid_png()))
except:
  pass
```

🎨Visualizing the Workflow
Here’s a graphical representation of the LangGraph workflow:

![Chatbot Graph](https://github.com/RD191295/Langgraph_chatbot_with_tools/raw/main/Images/chatbot-graph.png)

_____________________________________________________________________________________________________________________________________________________________________________

📊 Example Interaction

Let’s walk through an example of interacting with the chatbot. Here’s how it works when a user inputs a query:

User Input: "Write a Python code for Armstrong number"
Flow:
The chatbot will first process the input using the Groq language model.
If necessary, it will query Wikipedia for related information.
The Arxiv tool could also be used to retrieve any relevant research papers or resources related to Armstrong numbers or algorithms.
Finally, the chatbot will respond with a Python code example.
Example Code:

```python
user_input = "write a python code for armstrong number"
events = graph.stream(
    {"messages": [("user", user_input)]}, stream_mode="values"
)

for event in events:
    event["messages"][-1].pretty_print()
```
This flow shows how the chatbot is capable of generating a solution for a specific coding problem while also pulling relevant data from external sources.

🤝 Contributing
We welcome contributions! To contribute:

Fork the repository.
Create a new branch.
Make your changes and add tests.
Submit a pull request.
Please ensure your code follows the existing style and includes appropriate tests.

_____________________________________________________________________________________________________________________________________________________________________________

📜 License
This project is licensed under the MIT License - see the LICENSE file for details.

_____________________________________________________________________________________________________________________________________________________________________________

🛠️ Dependencies
LangGraph: Framework for managing state-based workflows.
LangChain: A framework to build applications powered by large language models (LLMs).
Langchain_Groq: Integrates Groq's powerful LLM models.
Arxiv: Interface for fetching academic papers from Arxiv.
Wikipedia: Interface for fetching articles from Wikipedia.
Groq: Use Groq’s advanced LLM model (Gemma2-9b-It).

This is a complete walkthrough of setting up and running the LangGraph Chatbot with integrated tools like Arxiv and Wikipedia. With these integrations, the chatbot can handle dynamic queries and provide real-time information, making it a highly adaptable conversational agent.

Happy coding! 🎉
