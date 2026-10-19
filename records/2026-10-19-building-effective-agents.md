# Original submitted reading notes

Submitted by Dineth on 2026-10-19. Original wording retained below; whitespace and list escaping normalized for Markdown.

Source: https://www.anthropic.com/engineering/building-effective-agents?utm_source=chatgpt.com

**Definitions and Setup:**

Large Language Model (LLM): A mathematical model trained to recognize patterns in language and use those patterns to generate text. More concretely, a large collection of adjustable numbers - parameters - arranged in a computational structure that transforms an input into an output - a neural network.

Tool: A function that an AI system can request to perform a specific action beyond generating text. Eg: Web search tool -> Find information online, File reader tool -> Read a document's contents, etc.

Code Path: A particular sequence of instructions followed when a program runs, its path possibly depending on inputs or conditions.

Eg: A program contains a rule: If the receipt exceeds $100, send it for review. Otherwise, approve it automatically. For a $150 receipt, the code path is Read Receipt -> Check amount -> Send for review. For a $50 receipt, the code path is Read Receipt -> Check Amount -> Approve automatically.

Workflows: "Systems where LLMs and tools are orchestrated through predefined code paths"

Agents: " Systems where LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks."

The most fundamental building block of agentic systems is the augmented LLM - an LLM enhanced with augmentations such as retrievals, tools, and memory. When implementing these augmentations, two aspects are of special importance - tailoring them to your specific use case; ensuring they provide an easy, well-documented interface for your LLM.

One implementation mechanism is the Model Context Protocol (MCP), a standard way for AI applications to connect to tools and sources of information.

As LLMs mature in key capabilities, agents will become more and more prevalent. Agents begin their work with either a command from or interactive discussion with a human user. Once the task is clear, agents plan and operate independently, returning to the human if needed for further information or judgement.

During execution, it is critical that agents regularly check their outputs and assumptions against external evidence to assess its progress. At checkpoints or when encountering blockers, agents can also pause for human feedback. Agent tasks typically terminate upon completion, though it is also common to include stopping conditions for a task (eg: maximum number of iterations) to maintain control.

While agents can handle sophisticated tasks, their implemention is often straightforward: they are typically just LLMs using tools based on environmental feedback in a loop. Therefore, it is crucial to design toolsets and their documentations clearly.

Agents can be used for open-ended problems where it's difficult or impossible to predict the required number of steps, and where you cannot hardcode a fixed path.

Agents' autonomy makes them ideal for scaling tasks in a trusted environment. However, this autonomy also means higher costs and the potential for compounding errors. As such, extensive testing in sandbox environments with the appropriate guardrails is strongly recommended.

Example of useful agent use include a coding agent or a computer use agent.

One key guiding principle - defer to simplicity when possible. Consider adding complexity only when it demonstrably improves outcomes.

When implementing agents, follow three core principles:
1) Maintain simplicity in agent design
2) Prioritize transparency by explicitly showing the agent's planning steps
3) Carefully craft your agent-computer interface (ACI) through thorough tool documentation and testing.

Anthropic's work has revealed two promising applications for AI agents. Both applications satisfy the criteria for agent use: they illustrate how agents add the most value for their task that requires both conversation and action, have clear success criteria, enable feedback loops, and integrate meaningful human oversight.

A. Customer support
B. Coding agents

When prompt-engineering tools for your agent, several principles are good to follow.

1) Give the models enough tokens to "think" before it commits to an output that is later revealed to have been poorly reasoned
2) Keep the format closest to what the model has naturally seen occurring in text on the internet
3) Make sure there's no formatting "overhead" - extra work required to express the output beyond figuring out the output to an action itself. Keep tool inputs easy to express.

As much as possible, put yourself in the model's shoes - invest effort into creating good ACIs.
