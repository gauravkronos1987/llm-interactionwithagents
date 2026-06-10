# LLM Agent Interaction with RAG

An intelligent course teaching assistant that uses LLM agents with tool calling and Retrieval-Augmented Generation (RAG) to answer student questions based on course FAQ databases.

## Overview

This project demonstrates how to build an AI agent that can:
- **Search** course FAQ databases intelligently
- **Reason** about the best search queries to find relevant information
- **Iterate** by making multiple searches to gather comprehensive answers
- **Respond** with accurate, context-grounded answers

The agent uses OpenAI's function calling (tool use) capability to decide when and how to search the FAQ database, making it a true agentic system rather than a simple question-answering pipeline.

## Features

- 🤖 **Agentic Loop**: Multi-step reasoning where the LLM decides when to search and what to search for
- 📚 **RAG Pattern**: Retrieves facts from FAQ databases and grounds responses in real data
- 🔍 **Semantic Search**: Uses minsearch with weighted fields for smart FAQ matching
- 🛠️ **Tool Calling**: Demonstrates OpenAI's function calling API for agent tool use
- 📊 **Multi-source FAQ**: Loads and indexes FAQ data from remote course repositories
- ✅ **Off-topic Detection**: Identifies and handles off-topic questions appropriately

## Architecture

### Components

1. **Ingest Module** (`ingest.py`)
   - Fetches FAQ data from remote source (datatalks.club)
   - Builds searchable index using minsearch
   - Supports multiple courses

2. **RAG Helper** (`rag_helper.py`)
   - `RAGBase` class: Core RAG implementation
   - Manages search with field boosting and course filtering
   - Builds context from search results
   - Generates LLM prompts

3. **Agent Notebook** (`agents.ipynb`)
   - Demonstrates the agentic loop
   - Shows how to set up OpenAI tool calling
   - Examples of multi-turn agent interactions

### How It Works

```
User Question
    ↓
Agent Loop (agentic reasoning)
    ↓
LLM decides: "Should I search?"
    ↓ (if yes)
Call search tool with keywords
    ↓
Retrieve FAQ results
    ↓
LLM analyzes results
    ↓
LLM decides: "Do I need more searches?"
    ↓ (loop back if yes, or exit if no)
Generate final answer
```

## Requirements

- Python >= 3.12
- OpenAI API key
- Internet connection (to fetch FAQ data)

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd llm-interactionwithagents
```

2. Create a virtual environment:
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -e .
```

4. Set up your OpenAI API key:
```bash
cp .env.example .env  # If provided
# Edit .env and add your OpenAI API key
echo "OPENAI_API_KEY=your-api-key-here" >> .env
```

## Usage

### Interactive Notebook

The primary way to use this project is through the Jupyter notebook:

```bash
jupyter notebook agents.ipynb
```

The notebook includes:
- Loading and indexing FAQ data
- Setting up the RAG assistant
- Creating search tools for agent use
- Running the agentic loop with multi-turn searches

### Example Query

```python
from ingest import load_faq_data, build_index
from rag_helper import RAGBase
from openai import OpenAI

# Setup
documents = load_faq_data()
index = build_index(documents)
client = OpenAI()

instructions = """
You're a course teaching assistant.
Answer questions about the course using the FAQ database.
Make multiple searches if needed.
Only use facts from the FAQ - don't make up information.
"""

assistant = RAGBase(index=index, llm_client=client, instructions=instructions)

# Query
answer = assistant.rag('How do I run Ollama locally?')
print(answer)
```

### Agent Loop Example

See `agents.ipynb` for examples of:
- Setting up function calling with OpenAI
- Implementing an agentic loop that iterates until it has enough information
- Handling multiple search queries in a single conversation

## Project Structure

```
.
├── agents.ipynb           # Main interactive notebook with examples
├── ingest.py              # FAQ data loading and indexing
├── rag_helper.py          # RAG implementation
├── main.py                # CLI entry point (placeholder)
├── pyproject.toml         # Project configuration
├── README.md              # This file
└── .env                   # Environment variables (create this)
```

## Configuration

### Environment Variables

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your-openai-api-key
```

### Customization

You can customize the RAG behavior in `rag_helper.py`:

- `boost_dict`: Weight different FAQ fields (e.g., prioritize 'question' over 'answer')
- `filter_dict`: Filter by course or other metadata
- `model`: Change the OpenAI model being used
- `instructions`: Modify the system prompt for the assistant

## Dependencies

- `openai>=2.41.0` - OpenAI API client
- `minsearch>=0.1.0` - Lightweight semantic search
- `python-dotenv>=1.2.2` - Environment variable management
- `requests>=2.34.2` - HTTP requests
- `jupyter>=1.1.1` - Jupyter notebook support

## Use Cases

- 📖 Course FAQs and Q&A automation
- 🎓 Intelligent tutoring systems
- 💼 Customer support with knowledge base
- 📚 Documentation search and retrieval
- 🤖 Building agentic AI systems with tool use

## Key Concepts Demonstrated

1. **Function Calling**: How LLMs can decide when to use tools
2. **Agentic Loop**: Multi-turn agent reasoning
3. **RAG Pattern**: Grounding LLM responses in retrieved facts
4. **Semantic Search**: Finding relevant documents with boosted fields
5. **Context Management**: Building prompts with retrieved context

## Troubleshooting

- **Import errors**: Make sure to install the package with `pip install -e .`
- **OpenAI API errors**: Verify your API key is set correctly in `.env`
- **FAQ data not loading**: Check your internet connection; the script fetches from datatalks.club
- **Tool not called**: Ensure you're using a model that supports function calling (gpt-4, gpt-4-turbo, gpt-5.4-mini, etc.)

## Future Improvements

- [ ] Add persistent conversation history
- [ ] Support for custom FAQ sources
- [ ] Caching of FAQ data locally
- [ ] Rate limiting for API calls
- [ ] More sophisticated prompt engineering
- [ ] Support for multiple languages

## License

MIT

## References

- [OpenAI Function Calling](https://platform.openai.com/docs/guides/function-calling)
- [RAG Pattern Overview](https://en.wikipedia.org/wiki/Retrieval-augmented_generation)
- [minsearch Documentation](https://github.com/alexeygrigorev/minsearch)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.