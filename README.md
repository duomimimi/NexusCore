# NexusCore User Guide

> Model-Agnostic Intelligence Routing Layer — Connect all AI models and build your own neural hub

---

## Core Concept

NexusCore is a **model-agnostic routing framework** whose core mission is to let you call, switch between, and manage any AI model (Claude, GPT, Gemini, local models) through a unified interface.

**Why does it matter?**

In real production environments, you often need to:
- Call multiple models simultaneously for different tasks
- Automatically switch to a backup model when one fails
- Dynamically select the most suitable model based on task type
- Unified management of API costs and usage

NexusCore solves this. It's not another model — it's an **intelligent routing layer** that upgrades your AI architecture from "locked to a single model" to "model-free switching."

---

## How to Use

### Step 1: Define Your Model Configuration

```python
from nexuscore import Router

# Define multiple model nodes
models = {
    "claude": {
        "provider": "anthropic",
        "model": "claude-3-5-sonnet-20241022",
        "api_key": "your-anthropic-key",  # Recommended: use environment variables
        "endpoint": "https://api.anthropic.com"
    },
    "gpt": {
        "provider": "openai",
        "model": "gpt-4o",
        "api_key": "your-openai-key",
        "endpoint": "https://api.openai.com/v1"
    },
    "local": {
        "provider": "ollama",
        "model": "llama3",
        "endpoint": "http://localhost:11434"
    }
}

router = Router(models)
```

### Step 2: Route by Task Type

```python
# Analysis task → delegate to Claude (best for understanding)
result = await router.route(
    task_type="analysis",
    prompt="Analyze the performance bottlenecks in this code",
    preferred_model="claude"
)

# Fast generation → delegate to GPT (fast and cost-effective)
result = await router.route(
    task_type="generation",
    prompt="Generate sample code for a REST API",
    preferred_model="gpt"
)

# Local/private task → delegate to local model
result = await router.route(
    task_type="internal",
    prompt="Process internal documents",
    preferred_model="local"
)
```

### Step 3: Automatic Failover

```python
# When Claude is unavailable, automatically switch to GPT
result = await router.route(
    task_type="analysis",
    prompt="Analyze this code",
    fallback_enabled=True,  # Enable automatic fallback
    fallback_order=["claude", "gpt", "local"]
)
```

---

## Code Example

```python
import asyncio
from nexuscore import Router, LoadBalancer

# Initialize routing
models = {
    "primary": {"provider": "anthropic", "model": "claude-3-5-sonnet-20241022"},
    "secondary": {"provider": "openai", "model": "gpt-4o"}
}

router = Router(models)
lb = LoadBalancer(router, strategy="latency")

async def process_request(prompt: str):
    # Automatically select the model with lowest latency
    model = lb.select()
    result = await router.route(prompt, model=model)
    return result

asyncio.run(process_request("Explain quantum entanglement"))
```

---

## Use Cases

### Case 1: Multi-Model Parallel Processing
When you need to process large volumes of user requests simultaneously, NexusCore can automatically distribute them across different model instances for load balancing. For example: processing 100 user text analysis requests at once — the system automatically distributes them across 3 available models.

### Case 2: Cost-Optimized Routing
You can set rules: "Simple Q&A goes to GPT, complex analysis goes to Claude." NexusCore automatically selects the most cost-effective model based on task complexity, potentially saving 30%-50% in annual API costs.

### Case 3: Seamless Model Switching
When a model provider experiences issues, NexusCore can switch to a backup model in milliseconds, completely imperceptibly to users. Critical for high-availability scenarios like finance and healthcare.

### Case 4: Hybrid Local + Cloud Architecture
Sensitive data goes to local models (Ollama/LM Studio), while routine tasks use cloud APIs. NexusCore makes hybrid architectures simple — no changes to business code required.

---

## Relationship with Other Modules

| Module | Relationship | Description |
|:----:|:----:|:-----|
| TruthMatrix | Downstream Validation | After NexusCore calls a model, output goes to TruthMatrix for hallucination detection |
| AgentHive | Foundation Layer | Multiple agents in AgentHive share the same NexusCore routing layer |
| QualityForge | Scoring Feedback | QualityForge determines which model to use next based on output quality |
| MirrorOS | Distributed Routing | Multiple MirrorOS instances can share the same NexusCore cluster |

**Architecture Position**: NexusCore is the foundational layer of the entire framework, responsible for model connection and scheduling. All other modules are built on top of it.

---

## Next Steps

- See the [TruthMatrix Guide](./truthmatrix-guide_en.md) — How to validate NexusCore output quality
- See the [AgentHive Guide](./agenthive-guide_en.md) — How to build multi-agent systems with NexusCore
- Get started: `pip install nexuscore`

---

*NexusCore — Upgrade your AI architecture from "locked to a model" to "model freedom"*
