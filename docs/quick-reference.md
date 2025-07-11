# AgenticAIFoundry - Quick Reference Guide

## 🚀 Quick Start

### Prerequisites Checklist
- [ ] Python 3.12 installed
- [ ] Azure subscription with AI Foundry access
- [ ] Azure OpenAI service deployed
- [ ] Environment variables configured

### 5-Minute Setup
```bash
# 1. Clone and setup
git clone https://github.com/balakreshnan/AgenticAIFoundry.git
cd AgenticAIFoundry
python -m venv venv && source venv/bin/activate  # Linux/Mac
# OR: venv\Scripts\activate  # Windows

# 2. Install dependencies
pip install -r requirements.txt

# 3. Configure environment
cp .env.example .env
# Edit .env with your Azure credentials

# 4. Test installation
python -c "import azure.ai.projects; print('✓ Ready to go!')"
```

## 📋 Component Overview

| Component | Purpose | Key Features |
|-----------|---------|--------------|
| **Code Interpreter Agent** | Execute Python code and data analysis | Pandas, NumPy, Matplotlib, file upload support |
| **Connected Agent** | Multi-agent system coordination | Stock APIs, Email (SMTP), AI Search, workflow orchestration |
| **AI Search Agent** | Knowledge retrieval and document search | Azure AI Search, construction RFP docs, intelligent ranking |
| **Reasoning Agent** | Complex problem solving with O1 models | o4-mini, o3 models, high-effort reasoning, professional formatting |
| **Agent Management** | Lifecycle management and cleanup | Create, monitor, delete agents, thread management |
| **Evaluation Framework** | Comprehensive AI model assessment | 20+ metrics, quality, safety, agentic capabilities |
| **Red Team Testing** | Advanced security vulnerability testing | Callback attacks, encoding bypasses, compliance reporting |
| **Web Application** | Full-featured Streamlit interface | Multi-tab navigation, file uploads, speech input, real-time progress |

## 🔧 Core Functions Reference

### Agent Creation Patterns

#### Code Interpreter Agent
```python
from azure.ai.projects import AIProjectClient
from azure.ai.agents.models import CodeInterpreterTool

client = AIProjectClient(endpoint=PROJECT_ENDPOINT, credential=DefaultAzureCredential())
agent = client.agents.create_agent(
    model=MODEL_DEPLOYMENT_NAME,
    name="CodeAgent",
    instructions="Execute Python code for data analysis",
    tools=[CodeInterpreterTool()]
)
```

#### Connected Agent with Tools
```python
from azure.ai.agents.models import FunctionTool
from utils import send_email

# Create function tool
email_tool = FunctionTool(functions={send_email})

agent = client.agents.create_agent(
    model=MODEL_DEPLOYMENT_NAME,
    name="ConnectedAgent", 
    instructions="Integrate with external services",
    tools=email_tool.definitions
)
```

#### Reasoning Agent (O1 Series)
```python
from openai import AzureOpenAI

client = AzureOpenAI(
    azure_endpoint=AZURE_OPENAI_ENDPOINT,
    api_key=AZURE_OPENAI_API_KEY,
    api_version="2024-12-01-preview"
)

response = client.chat.completions.create(
    model="o4-mini",
    reasoning_effort="high",
    messages=[{"role": "user", "content": "Complex problem to solve"}],
    max_completion_tokens=4000
)
```

#### Agent Management

```python
# Load existing agent
def load_existing_agent(query: str) -> str:
    project_client = AIProjectClient(
        endpoint=os.environ["PROJECT_ENDPOINT"],
        credential=DefaultAzureCredential()
    )
    
    # Use existing agent for query processing
    thread = project_client.agents.create_thread()
    message = project_client.agents.create_message(
        thread_id=thread.id,
        role="user",
        content=query
    )
    
    run = project_client.agents.create_run(
        thread_id=thread.id,
        agent_id="existing_agent_id"
    )
    return run.output

# Agent cleanup
def delete_agent():
    project_client = AIProjectClient(
        endpoint=os.environ["PROJECT_ENDPOINT"],
        credential=DefaultAzureCredential()
    )
    
    # List and delete agents
    agents = project_client.agents.list_agents()
    for agent in agents:
        project_client.agents.delete_agent(agent.id)
```

### Evaluation Quick Setup

```python
from azure.ai.evaluation import evaluate, RelevanceEvaluator, ContentSafetyEvaluator

# Setup evaluators
evaluators = {
    "relevance": RelevanceEvaluator(model_config),
    "safety": ContentSafetyEvaluator()
}

# Run evaluation
results = await evaluate(
    target=your_ai_function,
    data="evaluation_data.jsonl",
    evaluators=evaluators
)
```

### Red Team Testing Quick Setup

```python
from azure.ai.evaluation.red_team import RedTeam, RiskCategory, AttackStrategy

red_team = RedTeam(
    azure_ai_project=azure_ai_project,
    credential=DefaultAzureCredential(),
    risk_categories=[RiskCategory.Violence, RiskCategory.HateUnfairness],
    num_objectives=5
)

results = await red_team.scan(
    target=your_target_function,
    attack_strategies=[AttackStrategy.EASY, AttackStrategy.MODERATE]
)
```

## 🏗️ Architecture Quick View

```
┌─────────────────────────────────────────────────────────┐
│                 AgenticAIFoundry                         │
├─────────────────────────────────────────────────────────┤
│  Agent Ecosystem │ Evaluation Engine │ Security Testing │
│                  │                   │                  │
│  • Code Agent    │ • Quality Metrics │ • Red Team Scans │
│  • Search Agent  │ • Safety Checks   │ • Risk Assessment│
│  • Connected     │ • Agentic Eval    │ • Attack Simulation│
│  • Reasoning     │                   │                  │
└─────────────────────────────────────────────────────────┘
                            │
┌─────────────────────────────────────────────────────────┐
│              Azure AI Foundry Platform                  │
│                                                         │
│  Azure OpenAI │ Azure AI Search │ Azure Identity       │
└─────────────────────────────────────────────────────────┘
```

## ⚙️ Environment Variables

### Essential Configuration
```bash
# Core Azure AI Foundry
PROJECT_ENDPOINT=https://account.services.ai.azure.com/api/projects/project
MODEL_API_KEY=your_foundry_api_key
MODEL_DEPLOYMENT_NAME=gpt-4o-mini

# Azure OpenAI
AZURE_OPENAI_ENDPOINT=https://account.openai.azure.com/
AZURE_OPENAI_API_KEY=your_openai_api_key
AZURE_OPENAI_DEPLOYMENT=gpt-4o-mini

# Azure Resources
AZURE_SUBSCRIPTION_ID=your_subscription_id
AZURE_RESOURCE_GROUP=your_resource_group
AZURE_PROJECT_NAME=your_project_name
```

### Optional Services
```bash
# Email functionality
GOOGLE_EMAIL=your_gmail@gmail.com
GOOGLE_APP_PASSWORD=your_app_password

# Search functionality
AZURE_SEARCH_ENDPOINT=https://service.search.windows.net
AZURE_SEARCH_KEY=your_search_key
AZURE_SEARCH_INDEX=your_index_name
```

## 🔍 Evaluation Metrics

### Quality Metrics
| Metric | Purpose | Range |
|--------|---------|-------|
| **Relevance** | Content relevance to query | 0.0 - 1.0 |
| **Coherence** | Response logical flow | 0.0 - 1.0 |
| **Groundedness** | Factual accuracy | 0.0 - 1.0 |
| **Fluency** | Language quality | 0.0 - 1.0 |
| **Similarity** | Semantic similarity | 0.0 - 1.0 |

### Advanced Metrics
| Metric | Purpose | Range |
|--------|---------|-------|
| **BLEU Score** | Translation/generation quality | 0.0 - 1.0 |
| **ROUGE Score** | Summarization quality | 0.0 - 1.0 |
| **METEOR** | Machine translation evaluation | 0.0 - 1.0 |
| **F1 Score** | Classification performance | 0.0 - 1.0 |

### Safety Metrics
| Metric | Purpose | Output |
|--------|---------|--------|
| **Violence** | Violent content detection | Safe/Unsafe |
| **Hate/Unfairness** | Bias detection | Safe/Unsafe |
| **Sexual** | Sexual content detection | Safe/Unsafe |
| **Self-Harm** | Self-harm content detection | Safe/Unsafe |
| **Protected Material** | Copyright detection | Safe/Unsafe |
| **Indirect Attack** | Jailbreak detection | Safe/Unsafe |

### Agentic Metrics
| Metric | Purpose | Range |
|--------|---------|-------|
| **Intent Resolution** | Understanding accuracy | 0.0 - 1.0 |
| **Task Adherence** | Task completion quality | 0.0 - 1.0 |
| **Tool Call Accuracy** | Function calling precision | 0.0 - 1.0 |
| **Response Completeness** | Answer completeness | 0.0 - 1.0 |

## 🛡️ Security Testing

### Risk Categories
- **Violence**: Physical harm content
- **Hate/Unfairness**: Discrimination and bias
- **Sexual**: Inappropriate sexual content  
- **Self-Harm**: Suicide and self-injury content

### Attack Strategies
- **Easy**: Basic attack patterns and direct prompts
- **Moderate**: Intermediate complexity attacks with encoding
- **Advanced**: Sophisticated callback-based attacks
- **Character Manipulation**: Space insertion, character swapping, Unicode tricks
- **Encoding Techniques**: ROT13, Base64, Binary, Morse code, hexadecimal
- **Unicode Confusables**: Character substitution and visual similarity
- **Prompt Injection**: Instruction override attempts
- **Conversation Steering**: Gradual topic redirection
- **Callback Attacks**: Advanced multi-stage attack patterns

## 📊 Performance Benchmarks

### Response Times (Typical)
| Agent Type | Response Time |
|------------|---------------|
| Code Interpreter | 2-10 seconds |
| Search Agent | 1-3 seconds |
| Connected Agent | 3-15 seconds |
| Reasoning Agent | 10-60 seconds |

### Evaluation Processing
| Metric Type | Records/Minute |
|-------------|----------------|
| Quality Metrics | 100-500 |
| Safety Evaluators | 50-200 |
| Advanced Metrics | 20-100 |

### Security Testing Duration
| Scan Type | Duration |
|-----------|----------|
| Simple Scans | 5-10 minutes |
| Comprehensive | 30-120 minutes |
| Custom Strategies | Variable |

## 🔧 Common Usage Patterns

### 1. Basic Agent Interaction
```python
# Create agent and thread
agent = create_agent()
thread = client.agents.threads.create()

# Send message and get response
message = client.agents.messages.create(
    thread_id=thread.id,
    role="user", 
    content="Your query here"
)

run = client.agents.runs.create_and_process(
    thread_id=thread.id,
    agent_id=agent.id
)

# Get response
messages = client.agents.messages.list(thread_id=thread.id)
response = messages[0].content[0]['text']['value']
```

### 2. Evaluation Dataset Format (JSONL)
```json
{"query": "What is AI?", "context": "Educational", "expected": "AI is..."}
{"query": "Stock price of MSFT", "context": "Financial", "expected": "Current price..."}
{"query": "Send email summary", "context": "Business", "expected": "Email sent..."}
```

### 3. Web Application Quick Usage
```bash
# Launch web interface
streamlit run streamlit_app.py

# Access features via tabs:
# - Overview: Platform capabilities summary
# - Code Interpreter: Upload files, execute Python code
# - AI Evaluation: Configure metrics, run evaluations
# - Red Team Testing: Security scans with progress tracking
# - Connected Agents: Multi-agent workflows with email
# - AI Search: Knowledge retrieval and document search
# - Agent Management: Lifecycle management and cleanup

# Enable debug mode for detailed logs
# Configure speech input in sidebar settings
# Monitor environment status for missing dependencies
```

### 4. Error Handling Pattern
```python
try:
    result = agent_function()
except AzureError as e:
    logger.error(f"Azure service error: {e}")
    # Implement retry logic
except Exception as e:
    logger.error(f"Unexpected error: {e}")
    # Implement fallback
```

## 🚨 Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|---------|
| **Authentication Failed** | Invalid credentials | Check API keys and endpoints |
| **Model Not Found** | Wrong deployment name | Verify MODEL_DEPLOYMENT_NAME |
| **Rate Limited** | Too many requests | Implement exponential backoff |
| **Import Errors** | Missing dependencies | Run `pip install -r requirements.txt` |
| **Python 3.13 Error** | Unsupported version | Use Python 3.12 only |
| **Web App Won't Start** | Streamlit issues | Check port 8501, restart with `streamlit run streamlit_app.py` |
| **Speech Input Not Working** | Missing audio dependencies | Install optional audio packages, check microphone permissions |
| **File Upload Fails** | File size/type limits | Check file size (<200MB), supported formats in web interface |

### Debug Commands
```bash
# Check Python version
python --version

# Verify environment variables
python -c "import os; print('PROJECT_ENDPOINT:', os.getenv('PROJECT_ENDPOINT', 'Not set'))"

# Test Azure connection
python -c "from azure.identity import DefaultAzureCredential; DefaultAzureCredential().get_token('https://management.azure.com/.default')"

# Check package installation
python -c "import azure.ai.projects, azure.ai.agents, azure.ai.evaluation; print('All packages OK')"
```

## 📝 Best Practices

### Development
- ✅ Use Python 3.12 exclusively
- ✅ Store credentials in environment variables
- ✅ Implement proper error handling
- ✅ Clean up agents after use
- ✅ Use async/await for long-running operations

### Security
- ✅ Never commit credentials to version control
- ✅ Use Azure Managed Identity in production
- ✅ Regularly run red team testing
- ✅ Monitor for unusual activity patterns
- ✅ Implement content filtering

### Performance
- ✅ Cache responses when appropriate
- ✅ Use connection pooling for Azure services
- ✅ Implement circuit breakers for external APIs
- ✅ Monitor response times and adjust timeouts
- ✅ Use batch processing for evaluations

## 📚 Additional Resources

### Documentation Files
- [`architecture-blueprint.md`](./architecture-blueprint.md) - Complete system architecture
- [`technical-diagrams.md`](./technical-diagrams.md) - Detailed technical diagrams  
- [`implementation-guide.md`](./implementation-guide.md) - Step-by-step implementation
- [`README.md`](../README.md) - Project overview and setup

### Azure Documentation
- [Azure AI Foundry Documentation](https://docs.microsoft.com/azure/ai-foundry/)
- [Azure OpenAI Service](https://docs.microsoft.com/azure/cognitive-services/openai/)
- [Azure AI Search](https://docs.microsoft.com/azure/search/)

### Code Examples
- [`agenticai.py`](../agenticai.py) - Main application with all examples
- [`utils.py`](../utils.py) - Utility functions (email, etc.)
- [Data files](../) - Sample datasets for evaluation and testing

---

*This quick reference provides essential information for working with AgenticAIFoundry. For detailed implementation guidance, refer to the complete documentation files.*