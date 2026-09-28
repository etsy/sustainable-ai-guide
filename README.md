# Sustainable AI Guidance

This publication is intended to provide guidance about leveraging AI efficiently and strategically to **minimize the cost and environmental impact** associated with its use. These objectives are not always aligned, but they generally trend together, so optimizing for one will likely optimize the other. This guidance aims to strike a balance between being **specific enough to be useful and generic enough to be broadly applicable**. It is organized around two audiences: individuals using [AI in workplace and coding tools](#ai-in-workplace-tools) and Engineering + Product teams shipping [AI-powered features](#ai-in-production). 

**We welcome contributions!** Create an issue, submit a pull request (PR), or email us at sustainability@etsy.com.

### Table of Contents

- [AI in Workplace Tools](#ai-in-workplace-tools)
  - [Optimize for environmental impact and transparency](#optimize-for-environmental-impact-and-transparency)
  - [Use AI where it is most impactful](#use-ai-where-it-is-most-impactful)
  - [Minimize token usage](#minimize-token-usage)
  - [Right-size models](#right-size-models)
- [AI in Production](#ai-in-production)
  - [Optimize for environmental impact and transparency (continued)](#optimize-for-environmental-impact-and-transparency-continued)
  - [Use AI where it is most impactful (continued)](#use-ai-where-it-is-most-impactful-continued)
  - [Minimize token usage (continued)](#minimize-token-usage-continued)
  - [Right-size models (continued)](#right-size-models-continued)


# AI in Workplace Tools

This section is relevant to anyone using any workplace or coding AI tools.

## Optimize for environmental impact and transparency

### Consider standardized impact measurement approaches

AI consumes energy and other resources, yet most users lack the data to understand or reduce its impact. Most AI labs do not publish model-level energy or emissions data and while some hyperscalers provide customers with cloud emissions data, it's generally limited to customer-managed cloud workloads rather than enterprise AI applications, especially those using closed models.

To address this gap, on September 29, 2026, Sustainable AI Group launched an [open-source methodology](https://sustainableaigroup.com/CLEERlaunch) to estimate LLM energy use and emissions based on token usage. This kind of methodology can support improved GHG accounting and disclosure. When integrated into internal telemetry, it can show estimated impact alongside cost in the tools engineers already use—helping inform decisions before usage occurs. Etsy helped develop and test this methodology.

### Monitor your token usage

For tools that offer it, periodically check your token usage and experiment with implementing techniques below to reduce it. When token use is unavailable, cost is the next best proxy. To see your usage (details vary by platform):

- **Claude:**
  - **Claude desktop app or web UI**: click your name → Settings → Usage. Shows dollars spent in the current month.
  - **Claude Code CLI**: run `/status` then tab to Usage and Stats. Shows detailed statistics including token usage by model.
- **Cursor:** Go to your [Cursor dashboard](https://cursor.com/dashboard/usage) to see detailed usage statistics.
- **Codex / ChatGPT / OpenAI:**
  - **Codex CLI**: Use the `/status` command while a task is running to see your exact active token count. For lifetime and daily stats, use `/usage`.
  - **OpenAI Platform UI**: go to the [Usage tab](https://platform.openai.com/usage) to see detailed usage statistics.
- Use [ccusage](https://github.com/ccusage/ccusage) to see detailed statistics about Claude, Codex, and Gemini CLI usage. Run `npx ccusage@latest daily` (requires [node.js](https://nodejs.org)).

## Use AI where it is most impactful

Use AI for the greatest impact, i.e. for writing code or technical documents instead of quick Slack messages. In general, AI is most impactful where the effort of a task is high, but the creativity is low. **Highly creative tasks are often best performed by humans.**

### High-ROI examples

- Turning messy inputs into structured outputs. Ex: meeting transcript → recap, action items, decisions.
- Synthesizing information across sources. Ex: strategy docs + goals → one-page summary.
- Drafting complex, multi-section docs. Ex: first draft of a planning doc that you'll heavily edit.
- Learning unfamiliar tools in low-risk environments where you can safely experiment and iterate. Ex: first pass at SQL that you'll then get reviewed.
- Managing repetitive tasks. Ex. build a tool once (like a complex spreadsheet or workflow) that you can keep using repeatedly.

### Low-ROI examples (little or no gain over the work yourself)

- Asking an LLM to repeatedly iterate on content it has generated. **If you know how you want the content modified, make the edits yourself.**
- Using GenAI to draft materials where tone and nuance matter more than speed.
- Asking an LLM to find documentation that you already know how to find through a less intensive tool. 

### Be aware of AI-by-default

- **Turn off auto-enabled AI features** like meeting recording and summarization or Slack channel summarization and only use them when you will actually consume their output.
- **Explicitly search without AI when all you need is search.** Ex: adding "-ai" to a Google search will return pure search results without the AI summary.
- **Re-evaluate scheduled content.** If you are not reading it or using it frequently, unschedule it.

### Use media generation mindfully

Image and video generation are generally much more energy-intensive, often by orders-of-magnitude (though numbers vary widely by model and over time).

- **Default to text + simple diagrams when possible.** For internal docs, ASCII diagrams or basic charts are often clearer than AI-generated images.
- **Use AI images where they clearly add value.** Ex: design concepts, marketing explorations, early stakeholder-facing visuals.
- **Keep generations to a minimum** Generating many similar images with only slight variation is resource intensive and often for very low ROI.

## Minimize token usage

### Prompt intentionally

Prompting (what you send to an LLM) is what determines the input tokens you use, and it influences the quantity of output tokens returned and the quality of the results.

- **Be succinct and direct.** Use enough detail to limit repeated iteration, but keep in mind that excess words are wasted tokens and can contribute to confusion and hallucination. The volume of output tokens tends to drive the bulk of energy use (output tokens are generally [significantly more energy-intensive](https://hotcarbon.org/assets/2026/paper-17.pdf) than input tokens), so using more input tokens can be worthwhile to the extent that it results in fewer output tokens.
- **Ask for shorter, more specific output.** LLMs are verbose, and the volume of output tokens is typically a major driver of energy use; directing them to return less output is often more resource-efficient and can result in more effective written material.
  - "Give me 3 bullet points under 100 words" will help ensure that you don't end up with a wasteful, flowery novel.
  - "What is the process for submitting an expense report?" instead of "Tell me about our travel policies."
- **Use one prompt per objective.** Asking an LLM to do too many things in a single prompt makes it more prone to forgetting instructions, mixing up roles, hallucinating, and delivering shallow or messy results.
- **Use a few good examples.** If you are looking for specific output, it's generally most effective to provide a few simple examples instead of a long narrative explaining what you're looking for.
- **Understand effective prompt keywords:** [Research shows](https://arxiv.org/pdf/2503.10666) that using particular high-specificity action-oriented keyword prompts can result in meaningful energy savings though effects are task-specific.
  - **Efficient words:** "summarize", "measure", "generate", "translate", "classify"
  - **Inefficient words:** "analyze", "write", "explain", "recommend", "create"

### Manage context

As you interact with an AI agent, it saves the conversation history (both your messages and its output), any documents or images you've uploaded, any resources from the internet that you've directed it to, and some internal data about its work. All of this content is called "context", and it grows as a conversation unfolds. A lot of this context is included with each message you send, so as the "context window" grows, so does the amount of input tokens for each message. Agent applications perform limited context management behind the scenes, but intentional context management is the biggest lever a user has for controlling token usage, cost, energy, and model performance. Techniques for optimizing context include:

- **Prompt intentionally** (see above) – short, specific, focused prompts prevent context bloat.
- **Start fresh conversations.** New chats reset the context window. This not only reduces waste, it improves the quality of the output. LLMs are known to suffer from "context rot" when the context window is too full, resulting in attention dilution, which can lead to hallucinations, contradictory responses, ignored constraints, and increases in redundant work.
- **Only add what you need.** Instead of pointing an agent to a whole directory or even a whole document, only upload the specific document or portion of text that is relevant to what you are doing. Ex: if you are debugging, paste in only a few relevant log lines, not the whole log file.
- **Reuse context where possible.**
  - **Optimize for token caching.** For similar, repetitive tasks, use a consistent prompt, and put the repeated portion at the start of the prompt. This allows the LLM to cache the initial set of tokens.
  - **Leverage custom agents where possible.** Many providers offer a form of saved custom agents that users can create by uploading content and adding instructions, meant for reuse.
  - **Create summaries.** Ask an agent to concisely summarize what you've worked on together so it can be picked up again easily in the future without having to re-do work.
- **Use tools' context inspection features.** For example, in Claude Code, get in the habit of using `/context` to see which files or prompts are dominating the token window, then prune or down-scope those.
- **Use agent files and Skills strategically.**
  - **Keep CLAUDE.md / AGENTS.md files short** (<200 lines): they are loaded into EVERY prompt as context, making them token-expensive. Be sure they only contain information that is applicable to all prompts in the directory.
  - **Use Skills for efficient, reusable context loading.** Skills are efficient because only their names and descriptions get loaded into the agent initially. Their full contents are only read by the agent once activated by keywords in your prompt, or after they're explicitly referenced, making them token-efficient.
- **Use RAG-style retrieval where possible.** Retrieval-augmented generation (RAG) is a technique that enables large language models (LLMs) to retrieve and incorporate new information from external data sources (as opposed to only relying on memorized training data). When tools support it, point them at a corpus and let them retrieve small chunks per request instead of uploading entire PDFs or folders into a single prompt.

## Right-size models

### Choose the smallest effective model and reasoning effort

Energy, latency, and cost all roughly scale with model size × reasoning effort × tokens used. Model size determines the AI's overall capabilities; reasoning effort determines how long it works through a request before answering. Model size impacts the amount of computing resources required for both training and inference and the amount of tokens returned (larger models often return more text). Reasoning effort impacts the amount of server time used in processing the task and higher reasoning effort generates more internal "thinking" tokens.

In general, prefer smaller, task-appropriate models until you have evidence that you need something larger.

- Explore coding tools' **model auto-selection** features. This can help optimize for the task you're working on without significant research or thought on your end. If you use this, it's a good idea to gut-check its recommendations frequently.
  - [LLMRouter](https://github.com/ulab-uiuc/LLMRouter) automatically selects models based on each request.
  - In Claude Code CLI run `/model opusplan` when beginning a significant task. It will use Opus for planning then switch to Sonnet for implementation.
  - **Cursor & Codex have "Plan mode"**, designed to work with larger models for planning. You have to remember to change your model to a smaller model once your plan is ready for implementation.
- **Consider starting with a larger model.** Arguably, on a moderately complex task, if a smaller model will require more calls, iterations, and more of your time to get the task right, starting with a large model could be more cost/energy/carbon/time efficient overall. There are no hard and fast rules for this – it's worth experimenting.

Model and effort selection recommendations distilled into a matrix with examples:

| Effort → / Model tier ↓ | **Low:** Prioritize speed | **Standard:** Balanced default | **High:** Prioritize depth |
| --- | --- | --- | --- |
| **Fast & cheap** | **Use where applicable:** Quick questions, short summaries, formatting | Extraction, classification, straightforward rewrites | **Usually inefficient:** Use a workhorse model if high reasoning is required |
| **Workhorse** | Quick first drafts, explain familiar material | **Default for most work:** Drafting, analysis, routine coding | Complex analysis, difficult debugging, multi-step planning |
| **Frontier** | Strong quick answer when advanced knowledge or capability matters | Consequential synthesis, architecture design, nuanced review | **Use with intention:** Ambiguous work, deep research, complex decisions and planning |

---

# AI in Production

This section is most relevant to teams working on LLM-powered features (search, recommendations, Q&A, agents, moderation, etc.) or internal tooling powered by AI. It builds off of the prior section.

## Optimize for environmental impact and transparency (continued)

### Make "sustainable" observable

Track efficiency metrics relevant to your product and use that data to prioritize optimization work and identify waste.

- **Consider tracking:**
  - LLM calls per user action
  - Traffic by model
  - Approximate tokens per request
  - Skill usage
- Enforce per-feature "token error budgets" – if the value exceeds the limit, a design improvement or model downgrade is warranted
- Monitor for unintended usage patterns – ex: excessive retries or site scrapers

## Use AI where it is most impactful (continued)

### Does the problem require gen AI?

- **What type of problem is it?**
  - Is it a fundamentally probabilistic or generative problem (e.g., summarizing messy text, generating human-like language) → good fit for gen AI.
  - Or is it a structured logic or recall problem? → search index or rules engine might be better. For certain things we can leverage AI to write structured logic then use only the logic.
- **Can we encode the behavior in conventional ML** (classification, ranking, regression) with a lower-footprint model? Could we do so to reduce the size of data before leveraging an LLM?
- **Could a simpler, task-specific model be used?**

## Minimize token usage (continued)

### Leverage models strategically

- **Prefer RAG over full text.** Use models mainly as reasoners over retrieved snippets, not for reading full product descriptions on every request.
- **Leverage prefix caching where possible.** If there are elements of a prompt that stay constant, begin the prompt with them. LLMs can cache prefixes, saving cost, time, and energy.
- **Batch or precompute where latency and UX allow.** Pre-generate embeddings, classifications, or summaries offline once daily in a batch context and serve them from storage, instead of regenerating them per page view.
- **Use AI only upon request.** In engineering contexts, depending on the desired UX or feature, consider calling an LLM at request time – only when needed instead of pre-emptively for all data.
- **Use multi-stage pipelines:**
  - Stage 1: Cheap filter narrows work (keyword / heuristics / fast model).
  - Stage 2: Heavier model only for what truly needs deep reasoning.
- **Cache expensive steps.** Ex: If your flow is retrieve → summarize → classify → respond, consider:
  - Persisting summaries in a feature store or table.
  - Reusing classification results across multiple downstream surfaces.
- **Collaborate.** Be aware of what other teams are building and proactively seek to share or partner on what they're using LLMs for. Batching across jobs with similar inputs can drastically reduce resource usage because models will cache by default.

## Right-size models (continued)

### Choose the smallest effective model (continued)

The "Right-size models" section above has guidance on model selection, but additionally:

- **Bake model choice into config, not code.** Use config flags to swap models per environment or cohort so you can:
  - Easily experiment with smaller models.
  - Fall back to smaller models when quotas are constrained.
- **Quantify marginal benefit of "bigger".** When you propose upgrading, quantify "X% lift in metric Y for Z× more tokens / cost."
- **Consider on-device models for lighter tasks.** Local models ([iOS](https://developer.apple.com/documentation/FoundationModels), [Android](https://developer.android.com/newsletter/android-dev/2023/12-ai-update)) can be a faster, cheaper alternative to calling out to server-side models.

---

*This article reflects general engineering guidance we're sharing for informational purposes - not a specific commitment, target, or guarantee about Etsy's environmental impact or roadmap, and not advice for your own organization's compliance needs. We may update this over time.*
