# Kestra AI Copilot & Agents — Quiz Answers & Explanations

LLM Zoomcamp bonus module: AI Copilot, RAG, and AI Agents in Kestra

---

## 1. Why does AI Copilot generate better Kestra flows than ChatGPT on the same prompt?

**Answer: AI Copilot has access to current Kestra plugin documentation**

A general-purpose model like ChatGPT only knows what was in its training data as of its cutoff, so it produces flow YAML from memory — often with outdated property names, deprecated plugin types, or invented fields that were never real. Kestra's AI Copilot is grounded (RAG-style) in the live plugin schema and documentation at generation time, so it writes syntactically valid YAML using the properties that actually exist in the version you're running. It isn't that Copilot runs a bigger or more powerful model, burns more tokens, or has open internet access — it's specifically retrieval against current, authoritative Kestra documentation that closes the gap between "plausible-looking YAML" and "YAML that actually runs."

## 2. How should the non-RAG response about Kestra 1.1 features be characterized?

**Answer: Vague, generic, or fabricated — the model guesses from training data**

Without retrieval, a model asked about a specific, relatively recent release has nothing concrete to draw on beyond whatever fragments happened to be in its pretraining data (which may predate the release entirely). Instead of refusing or leaving the answer empty, LLMs tend to produce fluent, confident-sounding text that fills the gap with generic orchestration-platform boilerplate or outright invented feature names — the classic hallucination failure mode. This is the direct illustration of why retrieval grounding matters: the ungrounded answer isn't identical to the RAG version and isn't a refusal, it's confidently wrong.

## 3. Approximate output token count for `multilingual_agent` at `summary_length = short`

**Answer: 60-100 tokens**

The system message caps "short" at 1-2 sentences. A tight factual sentence or two, tokenized, lands consistently in the roughly 60-100 token range — enough for real content and sentence structure, but far short of the 200+ tokens a "medium" or "long" summary would need. 5-15 tokens would be too small even for a single compressed sentence.

## 4. How many more output tokens does `long` use compared to `short`?

**Answer: 2-5x more**

"Long" is capped at 1-3 paragraphs versus "short"'s 1-2 sentences, so it's naturally longer — but not unboundedly so. The source text being summarized is itself only a few short paragraphs, which puts a ceiling on how much genuinely new content the model can generate before it starts padding or repeating itself. In practice this keeps the "long" output in the 2-5x range relative to "short," not the 10-20x or 50x you might assume from "sentences vs. paragraphs" alone.

## 5. `english_brevity`: 3 sentences vs. the original 1-sentence version

**Answer: 2-4x more**

Asking for 3 sentences instead of 1 doesn't produce a clean 3x scaling, because a single sentence already carries fixed overhead (subject, verb, framing) that doesn't repeat proportionally as more sentences are added. The observed increase lands in the 2-4x range — a real, measurable jump from the instruction change, but not the 5-10x or 10x+ blow-up that a naive "3 times the sentences = 3 times the tokens" assumption would predict.

## 6. Best approach for production workflows needing deterministic, auditable results

**Answer: Use traditional task-based workflows for predictability and auditability**

AI Agents are valuable precisely because they can adapt and make dynamic decisions at runtime — but that same property means the same input can produce a different sequence of steps or tool calls on different runs. That's the opposite of what strict compliance and audit requirements need. A traditional, explicit task-based flow (a fixed DAG) gives up some flexibility in exchange for reproducibility: the same input reliably produces the same execution path, every time, which is what "predictable and auditable" actually requires. Agents still have a place in production — but as a bounded step wrapped in guardrails (retries, timeouts, human approval), not as the default for the entire workflow.
