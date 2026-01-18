# AgentFoundry

A powerful template for creating large-scale multi-agent systems with dynamic agent generation, hierarchical organization, and built-in cost optimization.

---

## Table of Contents

1. [What is AgentFoundry?](#what-is-agentfoundry)
2. [Core Concepts](#core-concepts)
3. [Installation](#installation)
4. [Quick Start](#quick-start)
5. [Detailed Usage Guide](#detailed-usage-guide)
6. [Building Your Own System](#building-your-own-system)
7. [API Reference](#api-reference)
8. [Cost Management](#cost-management)
9. [Architecture Deep Dive](#architecture-deep-dive)
10. [Best Practices](#best-practices)
11. [Troubleshooting](#troubleshooting)

---

## What is AgentFoundry?

AgentFoundry is a **template and framework** for building large-scale multi-agent AI systems. It solves the challenge of orchestrating hundreds or thousands of AI agents efficiently, each with unique personalities, skills, and roles.

### Why AgentFoundry?

Traditional multi-agent systems face several challenges at scale:

| Challenge | AgentFoundry Solution |
|-----------|----------------------|
| Memory exhaustion with many agents | **Lazy loading** — agents instantiated only when needed |
| Runaway API costs | **Budget controls** — automatic cost tracking and limits |
| Redundant API calls | **Response caching** — identical queries return cached results |
| Organizational chaos | **Hierarchical groups** — automatic categorization and leadership |
| Slow sequential execution | **Batch processing** — parallel execution with configurable batches |

### Use Cases

- **Simulated Organizations** — Model companies, governments, or communities with distinct roles
- **Parallel Analysis** — Get diverse perspectives on problems from specialized agents
- **Consensus Building** — Use voting and deliberation across agent groups
- **Research Simulations** — Study emergent behavior in large agent populations
- **Content Generation** — Generate varied content from agents with different personalities

---

## Core Concepts

### Agent Profiles

Every agent in AgentFoundry has a **profile** that defines its identity:

```python
@dataclass
class AgentProfile:
    name: str                           # Unique identifier
    role: AgentRole                     # Primary function (WORKER, MANAGER, etc.)
    category: AgentCategory             # Grouping (TECHNICAL, CREATIVE, etc.)
    specialization: List[str]           # Areas of expertise
    personality_traits: List[str]       # Behavioral characteristics
    skills: List[str]                   # Capabilities
    experience_level: str               # junior, senior, expert
    agent: Optional[Agent]              # The actual AI agent (lazy loaded)
    is_loaded: bool                     # Whether agent is instantiated
```

### Agent Roles

Roles define an agent's primary function and responsibilities:

| Role | Purpose | Typical Responsibilities |
|------|---------|-------------------------|
| `WORKER` | Task execution | Follow procedures, report progress, maintain quality |
| `MANAGER` | Team oversight | Set priorities, allocate resources, make decisions |
| `SPECIALIST` | Domain expertise | Solve complex problems, mentor others, provide insights |
| `COORDINATOR` | Cross-team facilitation | Manage dependencies, resolve conflicts, optimize workflows |
| `ANALYST` | Data interpretation | Extract insights, identify patterns, support decisions |
| `CREATOR` | Innovation | Generate ideas, design solutions, prototype concepts |
| `VALIDATOR` | Quality assurance | Review work, ensure compliance, provide feedback |
| `EXECUTOR` | Implementation | Execute plans precisely, adapt to changes, deliver results |

### Agent Categories

Categories organize agents into functional groups:

| Category | Domain | Example Agents |
|----------|--------|----------------|
| `TECHNICAL` | Engineering & Infrastructure | Developers, DevOps, Architects |
| `CREATIVE` | Design & Content | Designers, Writers, Artists |
| `ANALYTICAL` | Data & Research | Data Scientists, Researchers, Analysts |
| `OPERATIONAL` | Process & Workflow | Project Managers, Operations, QA |
| `STRATEGIC` | Planning & Leadership | Directors, Strategists, Executives |
| `SUPPORT` | Assistance & Coordination | Support Staff, Coordinators |

### Agent Groups

Agents are automatically organized into groups by category. Each group can have:

- A **leader** (first agent in the category)
- A **Board of Directors swarm** for group-level decisions
- Voting and consensus mechanisms

---

## Installation

### Requirements

- Python 3.9+
- OpenAI API key (or compatible LLM provider)

### Install Dependencies

```bash
pip install -r requirements.txt
```

Or install manually:

```bash
pip install swarms loguru pandas
```

### Environment Setup

Set your API key:

```bash
# Windows
set OPENAI_API_KEY=your-api-key-here

# Linux/Mac
export OPENAI_API_KEY=your-api-key-here
```

---

## Quick Start

### Minimal Example

```python
from AgentFoundry import MassAgentTemplate

# Create a system with 100 agents
template = MassAgentTemplate(agent_count=100)

# Run a task with 5 random agents
result = template.run_mass_task(
    "What is the most important skill for success?",
    agent_count=5,
)

# Print responses
for response in result['results']:
    print(response)
```

### Run the Demo

```bash
python AgentFoundry.py
```

This demonstrates:
- System initialization with 1000 agent profiles
- Category and role distribution
- Small task execution (5 agents)
- Large task execution (200 agents)
- Cost-limited execution
- Final statistics

---

## Detailed Usage Guide

### 1. Initializing the Template

```python
from AgentFoundry import MassAgentTemplate

template = MassAgentTemplate(
    # Data source (optional - uses synthetic data if not provided)
    data_source="agents.json",          # Path to JSON or CSV file
    
    # Scale
    agent_count=1000,                   # Number of agents to create
    
    # Organization
    enable_hierarchical_organization=True,  # Group agents by category
    enable_group_swarms=True,           # Create Board of Directors for groups
    
    # Cost optimization
    enable_lazy_loading=True,           # Only load agents when needed
    enable_caching=True,                # Cache responses
    batch_size=50,                      # Agents per batch
    budget_limit=100.0,                 # Maximum spend in dollars
    
    # Debugging
    verbose=True,                       # Enable detailed logging
)
```

### 2. Running Mass Tasks

#### Basic Mass Task

Run a task with a random selection of agents:

```python
result = template.run_mass_task(
    task="Analyze the impact of AI on healthcare",
    agent_count=20,  # Use 20 random agents
)

# Result structure
{
    "task": "Analyze the impact...",
    "agents_used": ["Agent_0001", "Agent_0042", ...],
    "results": ["Response 1", "Response 2", ...],
    "total_agents": 20,
    "cached": False,
    "cost_stats": {
        "total_tokens": 4500,
        "total_cost": 0.12,
        "requests_made": 20,
        "cache_hits": 0,
        "cache_hit_rate": 0.0,
        "budget_remaining": 99.88
    }
}
```

#### Cost-Optimized Mass Task

For large-scale operations with strict cost control:

```python
result = template.run_mass_task_optimized(
    task="Summarize key trends in renewable energy",
    agent_count=500,    # Target 500 agents
    max_cost=10.0,      # Stop if cost exceeds $10
)
```

This method:
- Uses smaller batches (25 agents) for finer cost control
- Stops execution when `max_cost` is reached
- Returns partial results if budget exceeded

### 3. Working with Groups

#### Get Group Information

```python
# List all groups
for group_name, group in template.groups.items():
    print(f"{group_name}: {group.total_agents} agents, leader: {group.leader}")

# Get a specific group
tech_group = template.get_group("Technical_Group")
print(f"Technical agents: {tech_group.agents}")
```

#### Run Group Tasks

Use the Board of Directors swarm for group-level decisions:

```python
# First, enable group swarms during initialization
template = MassAgentTemplate(
    agent_count=200,
    enable_group_swarms=True,
)

# Run a task with a group's Board of Directors
result = template.run_group_task(
    group_name="Analytical_Group",
    task="Evaluate the ROI of implementing a new CRM system",
)

# Result includes voting and consensus from the group's leadership
print(result['result'])
```

### 4. Querying Agents

#### By Category

```python
from AgentFoundry import AgentCategory

# Get all creative agents
creative_agents = template.get_agents_by_category(AgentCategory.CREATIVE)

# Get all technical agents
tech_agents = template.get_agents_by_category(AgentCategory.TECHNICAL)
```

#### By Role

```python
from AgentFoundry import AgentRole

# Get all managers
managers = template.get_agents_by_role(AgentRole.MANAGER)

# Get all specialists
specialists = template.get_agents_by_role(AgentRole.SPECIALIST)
```

#### Individual Agent

```python
# Get a specific agent profile
profile = template.get_agent("Alex_Developer_0042")

if profile:
    print(f"Name: {profile.name}")
    print(f"Role: {profile.role}")
    print(f"Skills: {profile.skills}")
    print(f"Loaded: {profile.is_loaded}")
```

### 5. Monitoring System Statistics

```python
stats = template.get_system_stats()

print("=== SYSTEM STATUS ===")
print(f"Total agents: {stats['total_agents']}")
print(f"Loaded agents: {stats['loaded_agents']}")
print(f"Groups: {stats['total_groups']}")

print("\n=== COST STATS ===")
print(f"Total cost: ${stats['cost_stats']['total_cost']:.2f}")
print(f"Budget remaining: ${stats['cost_stats']['budget_remaining']:.2f}")
print(f"Cache hit rate: {stats['cost_stats']['cache_hit_rate']:.1%}")

print("\n=== CATEGORY BREAKDOWN ===")
for category, count in stats['categories'].items():
    print(f"  {category}: {count}")

print("\n=== ROLE BREAKDOWN ===")
for role, count in stats['roles'].items():
    print(f"  {role}: {count}")
```

---

## Building Your Own System

AgentFoundry is designed as a **template** you can extend and customize. Here's how to build your own multi-agent system.

### Step 1: Define Your Agent Data

Create a JSON or CSV file with your agent definitions:

#### JSON Format

```json
[
  {
    "name": "SeniorArchitect_Sarah",
    "role": "specialist",
    "category": "technical",
    "specialization": ["System Design", "Cloud Architecture", "Microservices"],
    "personality_traits": ["methodical", "detail-oriented", "collaborative"],
    "skills": ["AWS", "Kubernetes", "Python", "System Design"],
    "experience_level": "expert"
  },
  {
    "name": "JuniorDev_Mike",
    "role": "worker",
    "category": "technical",
    "specialization": ["Frontend Development", "React"],
    "personality_traits": ["eager", "curious", "fast-learner"],
    "skills": ["JavaScript", "React", "CSS", "Git"],
    "experience_level": "junior"
  }
]
```

#### CSV Format

```csv
name,role,category,specialization,personality_traits,skills,experience_level
SeniorArchitect_Sarah,specialist,technical,"System Design;Cloud Architecture","methodical;detail-oriented","AWS;Kubernetes;Python",expert
JuniorDev_Mike,worker,technical,"Frontend Development;React","eager;curious","JavaScript;React;CSS",junior
```

### Step 2: Add Custom Roles

Extend the `AgentRole` enum to add domain-specific roles:

```python
from AgentFoundry import AgentRole, MassAgentTemplate

class CustomRole(str, Enum):
    """Extended roles for your domain."""
    
    # Inherit existing roles
    WORKER = "worker"
    MANAGER = "manager"
    SPECIALIST = "specialist"
    
    # Add custom roles
    RESEARCHER = "researcher"
    REVIEWER = "reviewer"
    MENTOR = "mentor"
    CLIENT_ADVOCATE = "client_advocate"
```

### Step 3: Add Custom Categories

```python
from AgentFoundry import AgentCategory

class CustomCategory(str, Enum):
    """Extended categories for your organization."""
    
    # Existing categories
    TECHNICAL = "technical"
    CREATIVE = "creative"
    
    # Custom categories
    SALES = "sales"
    LEGAL = "legal"
    HR = "human_resources"
    FINANCE = "finance"
    CUSTOMER_SUCCESS = "customer_success"
```

### Step 4: Customize System Prompts

Override the `_generate_agent_system_prompt` method to customize how agents behave:

```python
class CustomMassAgentTemplate(MassAgentTemplate):
    """Your customized multi-agent system."""
    
    def _generate_agent_system_prompt(self, profile: AgentProfile) -> str:
        """Generate custom system prompts for your domain."""
        
        base_prompt = super()._generate_agent_system_prompt(profile)
        
        # Add your custom instructions
        custom_additions = f"""
        
COMPANY CONTEXT:
You work at Acme Corp, a leading provider of enterprise software.
Our core values are: Innovation, Integrity, Customer Focus.

COMMUNICATION STYLE:
- Always be professional and solution-oriented
- Reference company policies when relevant
- Escalate compliance issues immediately

DOMAIN KNOWLEDGE:
- Our main product is AcmeCloud
- Key competitors: TechCorp, CloudMax
- Target market: Enterprise B2B
"""
        
        return base_prompt + custom_additions
```

### Step 5: Add Custom Task Processing

```python
class CustomMassAgentTemplate(MassAgentTemplate):
    """Your customized multi-agent system."""
    
    def run_specialized_task(
        self,
        task: str,
        category: AgentCategory,
        min_experience: str = "senior",
    ) -> Dict[str, Any]:
        """Run a task with agents filtered by category and experience."""
        
        # Get agents matching criteria
        category_agents = self.get_agents_by_category(category)
        
        qualified_agents = [
            name for name in category_agents
            if self.agents[name].experience_level in [min_experience, "expert"]
        ]
        
        if not qualified_agents:
            return {"error": "No qualified agents found"}
        
        # Load and run
        agents = self._load_agents_batch(qualified_agents[:10])
        results = run_agents_concurrently(agents, task)
        
        return {
            "task": task,
            "category": category.value,
            "agents_used": qualified_agents[:10],
            "results": results,
        }
```

### Step 6: Implement Custom Workflows

Create multi-stage workflows with different agent groups:

```python
class WorkflowMassAgentTemplate(MassAgentTemplate):
    """Multi-agent system with workflow support."""
    
    def run_review_workflow(self, content: str) -> Dict[str, Any]:
        """
        Three-stage review workflow:
        1. Creators generate ideas
        2. Analysts evaluate
        3. Validators approve
        """
        
        results = {}
        
        # Stage 1: Creation
        creators = self.get_agents_by_role(AgentRole.CREATOR)[:3]
        creator_agents = self._load_agents_batch(creators)
        results['creation'] = run_agents_concurrently(
            creator_agents,
            f"Generate creative ideas for: {content}"
        )
        
        # Stage 2: Analysis
        analysts = self.get_agents_by_role(AgentRole.ANALYST)[:3]
        analyst_agents = self._load_agents_batch(analysts)
        results['analysis'] = run_agents_concurrently(
            analyst_agents,
            f"Analyze these ideas and identify the strongest: {results['creation']}"
        )
        
        # Stage 3: Validation
        validators = self.get_agents_by_role(AgentRole.VALIDATOR)[:2]
        validator_agents = self._load_agents_batch(validators)
        results['validation'] = run_agents_concurrently(
            validator_agents,
            f"Validate and approve the best idea: {results['analysis']}"
        )
        
        return results
```

### Step 7: Build a Complete Custom System

Here's a full example of a customized system:

```python
"""
CustomAgentSystem.py - Your organization's multi-agent system
"""

from AgentFoundry import (
    MassAgentTemplate,
    AgentProfile,
    AgentRole,
    AgentCategory,
)
from typing import Dict, List, Any


class AcmeAgentSystem(MassAgentTemplate):
    """
    Acme Corp's customized multi-agent system for product development.
    """
    
    def __init__(self, **kwargs):
        # Set your defaults
        kwargs.setdefault('agent_count', 50)
        kwargs.setdefault('budget_limit', 25.0)
        kwargs.setdefault('batch_size', 10)
        
        super().__init__(**kwargs)
        
        # Add custom initialization
        self.project_context = {}
        self.decision_history = []
    
    def set_project_context(self, context: Dict[str, Any]):
        """Set context that all agents will have access to."""
        self.project_context = context
    
    def product_brainstorm(self, problem: str) -> Dict[str, Any]:
        """Generate product ideas from diverse perspectives."""
        
        task = f"""
        Project Context: {self.project_context}
        
        Problem to solve: {problem}
        
        Generate innovative product ideas that could solve this problem.
        Consider feasibility, market fit, and competitive advantage.
        """
        
        # Get creative and technical perspectives
        creative = self.get_agents_by_category(AgentCategory.CREATIVE)[:5]
        technical = self.get_agents_by_category(AgentCategory.TECHNICAL)[:5]
        
        all_agents = self._load_agents_batch(creative + technical)
        results = run_agents_concurrently(all_agents, task)
        
        return {
            "problem": problem,
            "ideas": results,
            "agents": creative + technical,
        }
    
    def technical_review(self, proposal: str) -> Dict[str, Any]:
        """Have technical experts review a proposal."""
        
        specialists = self.get_agents_by_role(AgentRole.SPECIALIST)
        tech_specialists = [
            s for s in specialists
            if self.agents[s].category == AgentCategory.TECHNICAL
        ][:5]
        
        task = f"""
        Review this technical proposal and provide:
        1. Feasibility assessment (1-10)
        2. Technical risks
        3. Resource requirements
        4. Recommended changes
        
        Proposal: {proposal}
        """
        
        agents = self._load_agents_batch(tech_specialists)
        results = run_agents_concurrently(agents, task)
        
        return {
            "proposal": proposal,
            "reviews": results,
            "reviewers": tech_specialists,
        }


# Usage
if __name__ == "__main__":
    system = AcmeAgentSystem(
        data_source="acme_team.json",
        verbose=True,
    )
    
    system.set_project_context({
        "company": "Acme Corp",
        "product": "AcmeCloud",
        "quarter": "Q1 2026",
        "budget": "$500K",
    })
    
    # Run brainstorm
    ideas = system.product_brainstorm(
        "Customers are churning due to slow onboarding"
    )
    
    print("Generated Ideas:")
    for idea in ideas['ideas']:
        print(f"- {idea[:200]}...")
```

---

## API Reference

### MassAgentTemplate

#### Constructor

```python
MassAgentTemplate(
    data_source: str = None,
    agent_count: int = 1000,
    enable_hierarchical_organization: bool = True,
    enable_group_swarms: bool = True,
    enable_lazy_loading: bool = True,
    enable_caching: bool = True,
    batch_size: int = 50,
    budget_limit: float = 100.0,
    verbose: bool = False,
)
```

#### Methods

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `run_mass_task` | `task: str, agent_count: int` | `Dict[str, Any]` | Run task with random agents |
| `run_mass_task_optimized` | `task: str, agent_count: int, max_cost: float` | `Dict[str, Any]` | Run with strict cost limit |
| `run_group_task` | `group_name: str, task: str` | `Dict[str, Any]` | Run with group's Board |
| `get_agent` | `agent_name: str` | `Optional[AgentProfile]` | Get agent by name |
| `get_group` | `group_name: str` | `Optional[AgentGroup]` | Get group by name |
| `get_agents_by_category` | `category: AgentCategory` | `List[str]` | Get agents in category |
| `get_agents_by_role` | `role: AgentRole` | `List[str]` | Get agents with role |
| `get_system_stats` | — | `Dict[str, Any]` | Get system statistics |

### Return Types

#### Mass Task Result

```python
{
    "task": str,              # The executed task
    "agents_used": List[str], # Names of agents used
    "results": List[str],     # Agent responses
    "total_agents": int,      # Number of agents that responded
    "cached": bool,           # Whether result was from cache
    "cost_stats": {
        "total_tokens": int,
        "total_cost": float,
        "requests_made": int,
        "cache_hits": int,
        "cache_hit_rate": float,
        "budget_remaining": float,
    }
}
```

#### System Stats

```python
{
    "total_agents": int,
    "total_groups": int,
    "loaded_agents": int,
    "categories": Dict[str, int],
    "roles": Dict[str, int],
    "experience_levels": Dict[str, int],
    "cost_stats": {...},
    "optimization": {
        "lazy_loading": bool,
        "caching": bool,
        "batch_size": int,
        "budget_limit": float,
    }
}
```

---

## Cost Management

### Understanding Costs

AgentFoundry uses OpenAI's GPT-4o-mini by default. Cost is calculated as:

```
Cost = (tokens_used / 1,000,000) × $0.15
```

### Cost Optimization Features

| Feature | How It Works | Savings |
|---------|--------------|---------|
| **Lazy Loading** | Agents only instantiated when used | Memory + time |
| **Response Caching** | Identical queries return cached results | 100% on duplicates |
| **Batch Processing** | Smaller batches allow budget checks | Prevents overruns |
| **Budget Limits** | Automatic stop when limit reached | Guaranteed cap |

### Setting Budgets

```python
# Global budget for the session
template = MassAgentTemplate(budget_limit=50.0)

# Per-task budget
result = template.run_mass_task_optimized(
    task="...",
    agent_count=100,
    max_cost=5.0,  # This task only
)
```

### Monitoring Costs

```python
# Check current costs
stats = template.get_system_stats()

print(f"Spent: ${stats['cost_stats']['total_cost']:.2f}")
print(f"Remaining: ${stats['cost_stats']['budget_remaining']:.2f}")
print(f"Cache savings: {stats['cost_stats']['cache_hit_rate']:.1%}")

# Check if within budget
if template.cost_tracker.check_budget():
    print("Within budget!")
else:
    print("Budget exceeded!")
```

---

## Architecture Deep Dive

```
┌─────────────────────────────────────────────────────────────┐
│                    MassAgentTemplate                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────────┐    ┌──────────────────┐              │
│  │   Agent Profiles │    │   Agent Groups   │              │
│  │                  │    │                  │              │
│  │  ┌────────────┐  │    │  Technical_Group │              │
│  │  │ AgentProfile│ │    │  ├── leader      │              │
│  │  │ - name      │ │    │  ├── agents[]    │              │
│  │  │ - role      │ │    │  └── BoardSwarm  │              │
│  │  │ - category  │ │    │                  │              │
│  │  │ - skills    │ │    │  Creative_Group  │              │
│  │  │ - agent ◄───┼─┼────┼── (lazy loaded)  │              │
│  │  │   (lazy)    │ │    │                  │              │
│  │  └────────────┘  │    └──────────────────┘              │
│  │                  │                                       │
│  │  (×1000 profiles)│                                       │
│  └──────────────────┘                                       │
│                                                             │
│  ┌──────────────────┐    ┌──────────────────┐              │
│  │   Cost Tracker   │    │  Response Cache  │              │
│  │                  │    │                  │              │
│  │  - total_tokens  │    │  cache_key →     │              │
│  │  - total_cost    │    │    response      │              │
│  │  - budget_limit  │    │                  │              │
│  │  - cache_hits    │    │  (MD5 hash of    │              │
│  └──────────────────┘    │   task+agents)   │              │
│                          └──────────────────┘              │
│                                                             │
└─────────────────────────────────────────────────────────────┘

Execution Flow:
                                                              
  run_mass_task()                                             
       │                                                      
       ▼                                                      
  ┌─────────────┐     ┌─────────────┐                        
  │Check Budget │────►│Check Cache  │                        
  └─────────────┘     └─────────────┘                        
       │                    │                                 
       │ within budget      │ cache miss                      
       ▼                    ▼                                 
  ┌─────────────┐     ┌─────────────┐                        
  │Select Agents│────►│Load Batch   │ (lazy loading)         
  └─────────────┘     └─────────────┘                        
                            │                                 
                            ▼                                 
                      ┌─────────────┐                        
                      │Run Parallel │                        
                      └─────────────┘                        
                            │                                 
                            ▼                                 
                      ┌─────────────┐                        
                      │Update Costs │                        
                      │Cache Result │                        
                      └─────────────┘                        
```

---

## Best Practices

### 1. Start Small, Scale Up

```python
# Development: small scale, verbose
template = MassAgentTemplate(
    agent_count=50,
    batch_size=5,
    budget_limit=5.0,
    verbose=True,
)

# Production: large scale, optimized
template = MassAgentTemplate(
    agent_count=1000,
    batch_size=50,
    budget_limit=100.0,
    verbose=False,
)
```

### 2. Use Caching for Repeated Queries

```python
# First call: hits API
result1 = template.run_mass_task("What is AI?", agent_count=10)

# Second call: returns cached (free!)
result2 = template.run_mass_task("What is AI?", agent_count=10)

print(result2['cached'])  # True
```

### 3. Filter Agents for Quality

```python
# Get only expert-level agents
experts = [
    name for name, profile in template.agents.items()
    if profile.experience_level == "expert"
]

# Use specific agents instead of random
agents = template._load_agents_batch(experts[:10])
```

### 4. Monitor Costs Continuously

```python
def run_with_monitoring(template, task, agent_count):
    """Run task with cost monitoring."""
    
    before = template.cost_tracker.total_cost_estimate
    result = template.run_mass_task(task, agent_count)
    after = template.cost_tracker.total_cost_estimate
    
    print(f"Task cost: ${after - before:.4f}")
    print(f"Total spent: ${after:.2f}")
    
    return result
```

### 5. Use Groups for Organized Work

```python
# Instead of random agents, use relevant groups
tech_result = template.run_group_task(
    "Technical_Group",
    "Review this architecture proposal",
)

creative_result = template.run_group_task(
    "Creative_Group",
    "Design the user experience",
)
```

---

## Troubleshooting

### "Budget exceeded" Error

**Cause**: Cost limit reached before task completion.

**Solutions**:
1. Increase `budget_limit` in constructor
2. Reduce `agent_count` in task
3. Use `run_mass_task_optimized` with higher `max_cost`

### Slow Performance

**Cause**: Too many agents being loaded or processed.

**Solutions**:
1. Enable lazy loading: `enable_lazy_loading=True`
2. Reduce batch size: `batch_size=25`
3. Use caching: `enable_caching=True`

### Memory Issues

**Cause**: Too many agents loaded simultaneously.

**Solutions**:
1. Enable lazy loading
2. Reduce `agent_count`
3. Process in smaller batches

### Empty Results

**Cause**: No agents matched criteria or budget exhausted.

**Solutions**:
1. Check `cost_stats` in result
2. Verify agents exist for the category/role
3. Check if `error` key exists in result

### API Rate Limits

**Cause**: Too many concurrent requests.

**Solutions**:
1. Reduce `batch_size`
2. Add delays between batches (extend `run_mass_task`)
3. Use a model with higher rate limits

---

## License

MIT

## Contributing

Contributions welcome! Please ensure code follows the project's style guide:

- Type annotations on all functions
- Docstrings with Args, Returns, and Raises sections
- Use loguru for logging
- Add tests for new features
