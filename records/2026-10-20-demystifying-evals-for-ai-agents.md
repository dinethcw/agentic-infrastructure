# Original submitted reading notes — 20 October 2026

Source: https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents?utm_source=chatgpt.com

Record note: Submitted wording is preserved below; chat HTML spacing entities and redundant blank lines have been normalized to Markdown. This is the latest, expanded submission, not a separate record of the earlier partial paste.

---

Good evaluations are important. Without them, agents can easily get stuck in reactive loops where issues only get caught in production and fixing one failure can lead to another. Evaluations make problems and behavioral changes visible before they affect users , and their value compounds over the lifecycle of an agent.

Absent evaluations, debugging is reactive - wait for complaints, reproduce them manually, fix the bug, and hope nothing else has regressed. Teams can't distinguish real regressions from noise, automatically test changes against hundreds of scenarios before shipping, or measure improvements.

Writing evals is useful at any stage of an AI agents' production.

Evaluation (Eval): "A test for an AI system. Give an AI an input, then apply grading logic to its output to measure success." Automated evals can be run during development without real users.

Single-turn evaluations are straightforward, consisting of a prompt, a response, and a grading logic. As AI capabilities have advanced, multi-turn evaluations have become increasingly common.

Agent evaluations are even more complex. Since they use tools across many turns and modify their environment state, their mistakes can propagate and compound.

When building agent evaluations, we rely on the following definitions:

1. Task (or problem or test case): A single task with defined inputs and success criteria.
2. Trial: An attempt at a task
3. Grader: Logic that scores some aspect of an agent's performance. A single task may have multiple graders, each with multiple assertions or checks.
4. Transcript (or trace or trajectory): The complete record of a trial, including outputs, tool calls, reasoning, intermediate results, and any other interactions.
5. Outcome: The final state of the environment at the end of the trial.
6. Evaluation harness: "Infrastructure that runs evals end-to-end. It provides instructions and tools, runs tasks concurrently, record all the steps, grades outputs, and aggregates results.
7. Agent harness (or scaffold): The system that enables a model to act as an agent. It processes inputs, orchestrates tool calls, and return results
8. Evaluation suite: Collection of tasks designed to measure specific capabilities or behaviors. Tasks in a suite typically share a broad goal - for instance, a customer support eval suite might test refunds, cancellations, and escalations.

When we evaluate an AI agent, we're evaluating the harness and the model working together.

There are three types of graders for agents, and agent evaluations typically combine all three: code-based, model-based, and human. Each grader evaluates some portion of either the transcript or the outcome. *An essential component of effective evaluation design is to choose the right graders for the job*.

***Code Based Graders*** - String match checks, binary tests, static analysis, outcome verification, etc

Pros: Fast, Cheap, Objective, Reproducible, Easily debuggable, Verify specific conditions

Cons: Brittle to valid variations that don't match expected patterns exactly, lack nuance, limited in evaluating subjective tasks

*Model Based Graders* - Rubric based scoring, natural-language assertion, pairwise comparison, reference-based comparison, multi-judge consensus

Pros: Flexible, Scalable, Captures nuance, Handles open-ended tasks, Handles free-form output

Cons: Non-deterministic,  more expensive than code, requires calibration from human graders for output.

*Human graders* - SME Review, Crowdsourced judgement, Spot-checking sampling, A/B testing, Inter-annotator agreement

Pros: Gold standard quality, matches expert user judgement, used to calibrate model-based graders

Cons: Expensive, slow, often requires access to human experts at scale.

There are two types of evals: capability and regression.

Capability or "quality" evals ask "What can this agent do well?" They should start at a low pass rate, target tasks the agend struggles with, and give it a hill to climb on.

Regression evals ask "Does the agent still handle all the tasks it used to?" and should have a near 100% pass rate. A decline in score signals something is broken and needs to be improved. As teams hill-climb on capability evals, it's important to ensure regression evals are maintained.

After an agent is launched, capability evals can eventually graduate to becoming regression evals.

There are already several proven evaluation techniques for several types of agents. These methods can be used as a foundation and then extended to your domain as needed. Reference them at the site.

But what about the challenge of non-determinism in agent evals?

Since agent behavior varies between runs, we often want to measure the proportion of trials an agent succeeded at a task. There are two metrics to capture this approach:

1. pass @ k  - The probability an agent gets a correct solution in k attempts. Useful for tools where success matter.
2. pass^k - The probability that all k trials succeed. Especially important for tools where consistency is essential eg: customer facing agents.

The Holy Grail Road Map for eval-driven agent development: everything from defining success early, measuring it clearly, and iterating continuously.

\*Collect tasks for the initial eval dataset\*

Step 0. Aim for 20-50 simple tasks drawn from real failures as a good start. Mature agents may need larger and more difficult eval, but it's best to take the 80/20 approach at the beginning.

Step 1. Start with the manual checks you actually test during development to make sure test case suite reflects actual usage. This prioritizes impact where it counts.

Step 2. Write unambiguous tasks with reference solutions.

A good task is one where two domain experts would independently reach the same pass/fail verdict, and which obviously they could pass themselves. Ambiguity in task specifications becomes noise in metrics. Each task should be passable by an agent that follows the instructions correctly. Everything the grader checks should be clear from the task description: agetns should not get failed due to ambiguous specs. A 0% pass rate is often a sign of a broken task. For each task, it is useful to make a reference solution i.e a known working output that passes all graders.

Step 3. Test both cases where a behavior should occur and when it shouldn't - one-sided evals create one-sided optimization.

\*Design the eval harness and graders\*

Step 4. Build a robust eval harness with a stable environment. It is essential that the agent in the eval functions roughly the same as in production - the enviornment should not introduce any noise. Each trial should be "isolated" by starting in a clean box. Make sure trials are independent.

Step 5. Design graders thoughtfully. It's often better to grade the agent's output rather than the path it took. For tasks with multiple components, build in partial credit. A support agent that identifies a problem but fails to solve it is better than one that fails outright, and this continuum of success in results is important. Model grading often takes careful iteration to validate accuracy. LLM graders should be closely calibrated with human experts to gain confidence that there is little divergence between human and model grading. To avoid hallucinations, give the model a way out like providing an instrction to return "unknown". It can help to create clear, structured rubrics to grade each dimension of a task, and then grade each dimension with an LLM judge rather than using one grade across all dimensions. Once the system is robust, it is sufficient to use human-review only occassionally.

Make your graders resistant to bypasses, hacks, or loopholes. The loopholes shouldnt be able to cheat the evals, and should only pass when genuinely solving the required problem.

\*Maintain and use the long-term eval\*

Step 6. Check the transcript. You cannot know if your graders are working well unless you read the trasncripts and grades from many trials. Failures should seem fair. Reading traces is a critical skill for agent development.

Step 7. Monitor for capability eval saturation. An eval at 100% tracks regression but provides no signal for improvement. Eval saturation occurs when an agent passes all of the solvable tasks, leaving no room for improvement.

"As a rule, we do not take eval scores at face value until someone digs into the details of the eval and reads some transcripts. If grading is unfair, tasks are ambiguous, valid solutions are penalized, or the harness constrains the model, the eval should be revised. " IMPORTANT.

Step 8. Keep evaluation suites healthy long-term through open contribution and maintenance. An eval suite is a living artifcat that needs ongoing maintenance andf clear ownership to remain useful.

I have a feeling the 8 step thing will be very important to reference as I go through this what I predict will be a very painful and long and arduous process.

Step 6.
