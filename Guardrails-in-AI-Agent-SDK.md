
[Guardrails](./images/AI-Agent-SDK-Guardarails.png)

**Think your Agent is safe without guardrails?**

That’s like leaving your front door wide open.

7 types of guardrails for your Agent 👇

1/ Relevance classifier:

Ensures agent responses stay within the intended scope by flagging off-topic queries.


2/ Safety classifier:

Detects unsafe inputs (jailbreaks or prompt injections)   that attempt to exploit system vulnerabilities.

3/ PII filter:

Prevents unnecessary exposure of personally identifiable information (PII) by vetting model output for any potential PII.

4/ Moderation:

Flags harmful or inappropriate inputs (hate speech, harassment, violence) to maintain safe, respectful interactions.

5/ Tool safeguards:

Assess the risk of each tool available to your agent. Use these risk ratings to trigger automated actions, such as pausing for guardrail checks before executing high-risk functions or escalating to a human if needed.


6/ Rules-based protections:

Simple deterministic measures (blocklists, input length limits, regex filters) to prevent known threats like prohibited terms or SQL injections.

7/ Output validation:

Ensures responses align with brand values via prompt engineering and content checks, preventing outputs that could harm your brand’s integrity.

Bonus tip: Optimise for both security and user experience, tweaking your guardrails as your agent evolves.