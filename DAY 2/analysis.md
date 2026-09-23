# Day 2 – Reasoning and Acting: Direct Prompting, Chain-of-Thought, and ReAct

## 1. Scenario

For this task, I chose a **college course fee assistant** scenario.

The available courses and fees are:

- CS101 – Rs. 12,000
- AI202 – Rs. 18,000
- DS303 – Rs. 15,000

The assistant needs to answer questions about course fees, calculate scholarship amounts, compare course combinations, and solve reasoning-based questions.

This scenario is useful because some questions can be answered using the model's existing knowledge, some require multi-step reasoning, and some require external information from tools.

---

# 2. Explanation of the Three Approaches

## 2.1 Direct Prompting

Direct prompting sends the user's question directly to the language model and asks it to provide an answer.

For example:

> What is the fee for AI202?

The model receives the question and immediately generates an answer.

Direct prompting does not use any external tool and does not show the model's internal reasoning process. It depends on the information already available to the model.

### What it can answer

Direct prompting can answer general questions and simple reasoning questions when the required information is already known by the model.

For example, it can perform a simple calculation such as:

> What is 12000 + 18000?

### What it cannot answer reliably

If the question requires information that is not available in the model's knowledge, direct prompting cannot independently retrieve that information.

For example, if the current course fee is stored in an external database, direct prompting cannot access that database without a tool.

### Limitation in this scenario

For the college fee scenario, direct prompting may provide an answer for a known calculation, but it cannot guarantee that a course fee is correct if the fee must be retrieved from an external source.

---

## 2.2 Chain-of-Thought Prompting

Chain-of-Thought prompting asks the model to reason through a problem step by step before producing the final answer.

For example:

> The total fees are Rs. 45,000. A 15% scholarship is given. What is the final amount?

The model needs to perform multiple reasoning steps:

1. Calculate 15% of Rs. 45,000.
2. Subtract the scholarship from the total.
3. Give the final payable amount.

The important point is that Chain-of-Thought improves the handling of multi-step reasoning, but it does not automatically give the model access to external information.

### What it can answer

It is useful for problems involving:

- Multiple calculations
- Logical relationships
- Multi-step reasoning
- Comparing different possibilities

### What it cannot answer reliably

If a required fact is missing from the model's knowledge, Chain-of-Thought cannot obtain that fact by itself.

For example, if the model does not know the current fee of a course stored in an external database, reasoning alone cannot retrieve it.

### Limitation in this scenario

Chain-of-Thought can correctly reason about scholarship calculations after the fees are known. However, it cannot independently call the course-fee tool to retrieve missing course fees.

---

## 2.3 ReAct Agent

ReAct stands for **Reasoning and Acting**.

A ReAct agent combines reasoning with actions. Instead of only generating an answer, the agent can decide when it needs a tool, call that tool, observe the result, and continue reasoning.

The basic cycle is:

**Thought → Action → Observation → Thought → Action → Observation → Final Answer**

For this project, the available tools include:

- `get_course_fee`
- `calculator`

For example, the question used in the ReAct experiment was:

> Which is cheaper: CS101 and AI202 with a 10% scholarship, or all three courses with a 25% scholarship? By how much?

The agent first obtains the course fees using the course-fee tool.

It then uses the calculator to calculate:

```text
(12000 + 18000) × 0.90 = 27000