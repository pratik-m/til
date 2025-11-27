# TIL: DSPy - Programming Language Models

## What is DSPy?

DSPy (Declarative Self-improving Python) is a framework that lets you **program language models** instead of prompting them. Rather than crafting and tweaking brittle prompts, you write compositional Python code that DSPy automatically compiles into optimized prompts.

## Core Concepts

**Signatures**: Define the input/output behavior for each AI component
```python
class GenerateResume(dspy.Signature):
    """Generate resume based on work experience and job description"""
  
    work_experience = dspy.InputField(desc="Candidate's skills & experience")
    job_description = dspy.InputField(desc="Role requirements & qualifications")
    resume = dspy.OutputField(desc="Tailored one-page resume")
```

**Modules**: Choose a strategy for invoking your LM (Predict, ChainOfThought, ReAct, etc.)
```python
resume_builder = ChainOfThought(GenerateResume)
pred = resume_builder(work_experience=exp, job_description=jd)
```

## What I Found Interesting

DSPy automatically generates structured system prompts from your signatures. From my simple class definition above, it created:

```
Your input fields are:
1. `work_experience` (str): Candidate's skills & experience
2. `job_description` (str): Role requirements & qualifications

Your output fields are:
3. `reasoning` (str)
4. `resume` (str): Tailored one-page resume

[structured format for I/O]

Your objective is: Generate resume based on work experience and job description
```

**No explicit prompt engineering needed** - just Python docstrings and field descriptions. DSPy handles the rest, including parsing structured outputs.

## Performance Comparison

Testing resume generation (Prompt engineering vs DSPy):

| Method | Input Tokens | Output Tokens | Latency (s) | Quality |
|--------|--------------|---------------|-------------|---------|
| Manual Prompting | 5,052 | 1,988 | 32.4 | Good |
| DSPy (ChainOfThought) | 4,385 | 2,310 | 57.5 | Better* |

*5/5 LLM judges preferred DSPy output (more comprehensive reasoning, better alignment)

**Trade-off**: DSPy is slower but produces more detailed reasoning and higher quality output. The extra output tokens come from structured CoT reasoning.

## The Real Power: Optimization

What I haven't explored yet (but is DSPy's killer feature): **automatic prompt optimization**. DSPy includes optimizers like `BootstrapFewShot` and `MIPROv2` that learn from your training data to improve prompts automatically.

```python
# Example (not tested yet)
optimizer = BootstrapFewShot(metric=your_metric)
optimized_module = optimizer.compile(resume_builder, trainset=examples)
```

This means you can iterate on your AI system by improving data/modules rather than manually tweaking prompts.

## When to Use DSPy

**Good fit for:**
- Complex, multi-step AI pipelines
- Systems that need optimization over time
- Production applications requiring maintainability
- RAG pipelines, agents, or chained reasoning

**Overkill for:**
- Simple one-off prompts
- Rapid prototyping where manual prompts suffice
- Very latency-sensitive applications (DSPy adds overhead, extra tokens)

## Setup

```python
import dspy

lm = dspy.LM("anthropic/claude-sonnet-4-5-20250929", api_key=api_key)
dspy.configure(lm=lm)

# Enable logging to see what's happening
dspy.enable_logging()
```


## Key Takeaway

DSPy shifts AI development from **prompt engineering** (string manipulation) to **module engineering** (composable Python code). The abstraction makes AI systems more maintainable, testable, and optimizable - especially valuable as applications grow in complexity.

