# Prompt Engineering

## Acknowledgements

### Content contributors
- Michael Sherman
- Yuan Cao
- Erick Armbrust
- Anant Nawalgaria
- Antonio Gulli
- Simone Cammel

### Curators and Editors
- Antonio Gulli
- Anant Nawalgaria
- Grace Mollison

### Technical Writer
- Joey Haymaker

### Designer
- Michael Lanning

## Table of contents

1. [Introduction](#introduction)
2. [Prompt engineering](#prompt-engineering)
3. [LLM output configuration](#llm-output-configuration)
4. [Prompting techniques](#prompting-techniques)
5. [System, contextual and role prompting](#system-contextual-and-role-prompting)
6. [Step-back prompting](#step-back-prompting)
7. [Chain of Thought (CoT)](#chain-of-thought-cot)
8. [Self-consistency](#self-consistency)
9. [Tree of Thoughts (ToT)](#tree-of-thoughts-tot)
10. [ReAct (reason & act)](#react-reason--act)
11. [Automatic Prompt Engineering](#automatic-prompt-engineering)
12. [Code prompting](#code-prompting)
13. [What about multimodal prompting?](#what-about-multimodal-prompting)
14. [Best Practices](#best-practices)
15. [Summary](#summary)
16. [Endnotes](#endnotes)

## Introduction

When thinking about a large language model input and output, a text prompt (sometimes accompanied by other modalities such as image prompts) is the input the model uses to predict a specific output. You don't need to be a data scientist or a machine learning engineer – everyone can write a prompt. However, crafting the most effective prompt can be complicated. Many aspects of your prompt affect its efficacy: the model you use, the model's training data, the model configurations, your word-choice, style and tone, structure, and context all matter. Therefore, prompt engineering is an iterative process. Inadequate prompts can lead to ambiguous, inaccurate responses, and can hinder the model's ability to provide meaningful output.

## Prompt engineering

Remember how an LLM works; it's a prediction engine. The model takes sequential text as an input and then predicts what the following token should be, based on the data it was trained on. The LLM is operationalized to do this over and over again, adding the previously predicted token to the end of the sequential text for predicting the following token. The next token prediction is based on the relationship between what's in the previous tokens and what the LLM has seen during its training.

When you write a prompt, you are attempting to set up the LLM to predict the right sequence of tokens. Prompt engineering is the process of designing high-quality prompts that guide LLMs to produce accurate outputs. This process involves tinkering to find the best prompt, optimizing prompt length, and evaluating a prompt's writing style and structure in relation to the task. In the context of natural language processing and LLMs, a prompt is an input provided to the model to generate a response or prediction.

## LLM output configuration

### Output length

An important configuration setting is the number of tokens to generate in a response. Generating more tokens requires more computation from the LLM, leading to higher energy consumption, potentially slower response times, and higher costs.

Reducing the output length of the LLM doesn't cause the LLM to become more stylistically or textually succinct in the output it creates, it just causes the LLM to stop predicting more tokens once the limit is reached. If your needs require a short output length, you'll also possibly need to engineer your prompt to accommodate.

### Sampling controls

LLMs do not formally predict a single token. Rather, LLMs predict probabilities for what the next token could be, with each token in the LLM's vocabulary getting a probability. Those token probabilities are then sampled to determine what the next produced token will be.

Temperature, top-K, and top-P are the most common configuration settings that determine how predicted token probabilities are processed to choose a single output token.

#### Temperature

Temperature controls the degree of randomness in token selection. Lower temperatures are good for prompts that expect a more deterministic response, while higher temperatures can lead to more diverse or unexpected results. A temperature of 0 (greedy decoding) is deterministic: the highest probability token is always selected.

#### Top-K and top-P

Top-K and top-P (also known as nucleus sampling) are two sampling settings used in LLMs to restrict the predicted next token to come from tokens with the top predicted probabilities.

- **Top-K sampling** selects the top K most likely tokens from the model's predicted distribution. The higher top-K, the more creative and varied the model's output; the lower top-K, the more restrictive and factual the model's output.

- **Top-P sampling** selects the top tokens whose cumulative probability does not exceed a certain value (P). Values for P range from 0 (greedy decoding) to 1 (all tokens in the LLM's vocabulary).

### Putting it all together

Choosing between top-K, top-P, temperature, and the number of tokens to generate depends on the specific application and desired outcome. As a general starting point:

- For creative tasks: temperature of .9, top-P of .99, top-K of 40
- For factual tasks: temperature of .1, top-P of .9, top-K of 20
- For tasks with single correct answers: temperature of 0

## Prompting techniques

LLMs are tuned to follow instructions and are trained on large amounts of data so they can understand a prompt and generate an answer. But LLMs aren't perfect; the clearer your prompt text, the better it is for the LLM to predict the next likely text. Additionally, specific techniques that take advantage of how LLMs are trained and how LLMs work will help you get the relevant results from LLMs.

### General prompting / zero shot

A zero-shot prompt is the simplest type of prompt. It only provides a description of a task and some text for the LLM to get started with. This input could be anything: a question, a start of a story, or instructions. The name zero-shot stands for 'no examples'.

### One-shot & few-shot

When creating prompts for AI models, it is helpful to provide examples. These examples can help the model understand what you are asking for. Examples are especially useful when you want to steer the model to a certain output structure or pattern.

A one-shot prompt provides a single example, hence the name one-shot. The idea is the model has an example it can imitate to best complete the task.

A few-shot prompt provides multiple examples to the model. This approach shows the model a pattern that it needs to follow. The idea is similar to one-shot, but multiple examples of the desired pattern increases the chance the model follows the pattern.

The number of examples you need for few-shot prompting depends on a few factors, including:
- The complexity of the task
- The quality of the examples
- The capabilities of the generative AI model

As a general rule of thumb, you should use at least three to five examples for few-shot prompting. However, you may need to use more examples for more complex tasks, or you may need to use fewer due to the input length limitation of your model.

### System, contextual and role prompting

System, contextual and role prompting are all techniques used to guide how LLMs generate text, but they focus on different aspects:

- **System prompting** sets the overall context and purpose for the language model. It defines the 'big picture' of what the model should be doing, like translating a language, classifying a review etc.

- **Contextual prompting** provides specific details or background information relevant to the current conversation or task. It helps the model to understand the nuances of what's being asked and tailor the response accordingly.

- **Role prompting** assigns a specific character or identity for the language model to adopt. This helps the model generate responses that are consistent with the assigned role and its associated knowledge and behavior.

### Step-back prompting

Step-back prompting is a technique for improving the performance by prompting the LLM to first consider a general question related to the specific task at hand, and then feeding the answer to that general question into a subsequent prompt for the specific task. This 'step back' allows the LLM to activate relevant background knowledge and reasoning processes before attempting to solve the specific problem.

By considering the broader and underlying principles, LLMs can generate more accurate and insightful responses. Step-back prompting encourages LLMs to think critically and apply their knowledge in new and creative ways.

### Chain of Thought (CoT)

Chain of Thought (CoT) prompting is a technique for improving the reasoning capabilities of LLMs by generating intermediate reasoning steps. This helps the LLM generate more accurate answers. You can combine it with few-shot prompting to get better results on more complex tasks that require reasoning before responding as it's a challenge with a zero-shot chain of thought.

#### Advantages of CoT:
- Low-effort while being very effective
- Works well with off-the-shelf LLMs (no need to finetune)
- Provides interpretability through visible reasoning steps
- Improves robustness when moving between different LLM versions

#### Disadvantages:
- Includes chain of thought reasoning in response (more output tokens)
- Higher costs due to longer responses
- Takes more time to generate responses

### Self-consistency

While large language models have shown impressive success in various NLP tasks, their ability to reason is often seen as a limitation that cannot be overcome solely by increasing model size. Self-consistency combines sampling and majority voting to generate diverse reasoning paths and select the most consistent answer. It improves the accuracy and coherence of responses generated by LLMs.

The process follows these steps:
1. Generating diverse reasoning paths with high temperature settings
2. Extracting answers from each generated response
3. Choosing the most common answer

### Tree of Thoughts (ToT)

Tree of Thoughts (ToT) generalizes the concept of CoT prompting because it allows LLMs to explore multiple different reasoning paths simultaneously, rather than just following a single linear chain of thought. This approach makes ToT particularly well-suited for complex tasks that require exploration.

### ReAct (reason & act)

ReAct prompting is a paradigm for enabling LLMs to solve complex tasks using natural language reasoning combined with external tools (search, code interpreter etc.) allowing the LLM to perform certain actions, such as interacting with external APIs to retrieve information which is a first step towards agent modeling.

ReAct mimics how humans operate in the real world, as we reason verbally and can take actions to gain information. ReAct performs well against other prompt engineering approaches in a variety of domains.

### Automatic Prompt Engineering

Automatic Prompt Engineering (APE) is a method that not only alleviates the need for human input but also enhances the model's performance in various tasks. The process involves:

1. Using a model to generate prompt variants
2. Evaluating the candidates based on chosen metrics
3. Selecting or tweaking the highest-scoring candidates
4. Iterating to improve results

## Best Practices

### Provide examples
The most important best practice is to provide examples within a prompt. This acts as a powerful teaching tool, showcasing desired outputs and allowing the model to learn from them.

### Design with simplicity
Prompts should be concise, clear, and easy to understand. If it's confusing for you, it will likely be confusing for the model. Avoid complex language and unnecessary information.

### Be specific about the output
Be specific about the desired output format, style, and content. Providing clear instructions helps the model focus on what's relevant.

### Use Instructions over Constraints
Focus on positive instructions rather than constraints. Tell the model what to do instead of what not to do.

### Control the max token length
Set appropriate token limits or request specific output lengths in your prompt.

### Document prompt attempts
Keep detailed records of your prompts, their results, and iterations. This helps track what works and what doesn't.

## Summary

This whitepaper has discussed various prompting techniques and best practices for working with large language models. Key topics covered include:

- Different prompting techniques from zero-shot to advanced methods like Chain of Thought
- Configuration options like temperature and sampling controls
- Best practices for crafting effective prompts
- The importance of documentation and iteration in prompt engineering

The field of prompt engineering continues to evolve, and success often comes from careful experimentation and application of these principles.

## Endnotes

1. Google, 2023, Gemini by Google. Available at: https://gemini.google.com
2. Google, 2024, Gemini for Google Workspace Prompt Guide
3. Google Cloud, 2023, Introduction to Prompting
4. Google Cloud, 2023, Text Model Request Body: Top-P & top-K sampling methods
5. Wei, J., et al., 2023, Zero Shot - Fine Tuned language models are zero shot learners
[etc...]
