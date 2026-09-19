# Awesome Jev use cases

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A list of things built with Jev, TypeSafe's model for typed decisions, plus the patterns and limits that showed up while building them. This list is unofficial and is not affiliated with TypeSafe.

Every entry links to proof from the builder. Ideas that nobody has shipped are in their own section and marked as ideas.

## Contents

- [What Jev is](#what-jev-is)
- [Built with Jev](#built-with-jev)
- [Cookbooks from TypeSafe](#cookbooks-from-typesafe)
- [Patterns](#patterns)
- [Limits of Jev 1.13](#limits-of-jev-113)
- [Reported cost and latency](#reported-cost-and-latency)
- [Ideas nobody has shipped yet](#ideas-nobody-has-shipped-yet)
- [Tools](#tools)
- [Contributing](#contributing)

## What Jev is

Jev answers typed questions about a piece of context. You send one state (a string, an object, or an array) and a map of named questions to a single endpoint. There are three question types.

- Choice picks one option from a set you define and returns the probability of every option.
- Score rates the state on an ordered rubric and can land between two levels.
- Noul answers yes or no as a probability.

Jev does not generate text. Output tokens are free, and questions asked over the same state run in parallel. For anything that needs prose, pair it with a normal LLM.

The smallest request:

```bash
curl https://api.typesafe.ai/v1/systemone \
  -H "Authorization: Bearer $TYPESAFE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "state": "Help! My payouts have been failing for 3 days.",
    "model": "jev-latest",
    "questions": {
      "is_urgent": {"type": "noul", "instructions": "Does this convey urgency?"}
    }
  }'
```

The [API reference](https://docs.typesafe.ai/api) shows this response for that request:

```json
{
  "model": "jev-latest",
  "answers": { "is_urgent": { "type": "noul", "noul": 0.92 } },
  "usage": { "input_tokens": 312, "output_tokens": 48 }
}
```

The [models page](https://docs.typesafe.ai/models) lists Jev 1.13 at $42 per billion input tokens, a limit of 250,000 tokens per second and 1,200 requests per minute, and a cap of 32k tokens on the state plus the longest question. The `model` value `jev-latest` currently points to `jev-1.13.0`.

## Built with Jev

- [Realtime adblocker extension](https://x.com/iam_zachi/status/2100529273186472318) by @iam_zachi. A browser extension that checks every DOM element on a page, classifies it as ad or not ad, and removes the ones that match. Built on TypeSafe.
- [Live viral post analyzer](https://x.com/rileybrown/status/2100425868053008758) by @rileybrown. Analyzes a draft tweet half a second after you stop typing and categorizes the tweet live. The author calls it an experiment and says he may add similar tweets for inspiration.
- [1kpapers](https://x.com/nutlope/status/2100426999546184123) by @nutlope. Classifies 1,018 AI research papers into 24 topics and shows them at [1kpapers.com](https://1kpapers.com). DeepSeek V4 Flash writes the summaries and Jev does the classification. The author reports 8 cents for the classification and a 256 ms median end-to-end latency per paper, and says he is still running evals before replacing the current labels.
- will-it-hit by @walidboulanouar. A live LinkedIn draft scorer. One call asks eight Score questions (hook, specificity, emotion, clarity, repostability, authority, algorithm fit, expected engagement) and one Choice question for post type. The rubrics carry real engagement numbers from LinkedIn posts. A separate LLM writes rewrites, and Jev scores each rewrite again so you see both numbers. The source is in a private repository for now.
- linkedin-slop-blocker by @walidboulanouar. A browser extension that scans the LinkedIn feed and removes posts that read like AI slop or spam. A free pattern scan runs first, then one Jev call per scroll returns a spam probability and a 0 to 100 quality score for each post. It keeps the user's own API key in the browser and learns from posts the user hides or restores. An earlier version removed 34 posts in one scroll session. The source is in a private repository for now.

## Cookbooks from TypeSafe

TypeSafe's own worked examples. Numbers below are the ones TypeSafe reports in each cookbook.

- [Parallel questions](https://docs.typesafe.ai/cookbooks/parallel_questions). Runs a 13-question regulatory briefing over one document. Batching every question into one call is reported as 12.2x cheaper and 10.0x faster with no change in answers.
- [Re-ranking](https://docs.typesafe.ai/cookbooks/rerank_typesafe). Asks one question per query and candidate pair on 30-passage shortlists for 40 legal queries. Top-1 accuracy goes from 5% to 18% and top-10 from 38% to 62%.
- [Line-by-line search](https://docs.typesafe.ai/cookbooks/semantic_find). Semantic search over GitHub's Terms of Service. One request scores 218 line ids against a plain-language query.
- [Structure recovery](https://docs.typesafe.ai/cookbooks/autoformat). Rebuilds Markdown from plain text that lost its formatting, in two requests.
- [Function calling](https://docs.typesafe.ai/cookbooks/function_calling). Turns natural-language trading requests into calls to ordinary typed functions.
- [Skill suggestion](https://docs.typesafe.ai/cookbooks/skill_suggestion). Picks at most one skill for an agent turn out of the 182 in Nous Research's Hermes catalog, and can reject every candidate.
- [Knowledge graph entity alignment](https://docs.typesafe.ai/cookbooks/entity_alignment). Decides which of 450 candidate pairs from two beer catalogues are the same product. One Score question carries the decision, and its levels are merge, leave unlinked, and hand to a curator.
- [Classifying RAG passages](https://docs.typesafe.ai/cookbooks/classifying_rag_passages). Scores each retrieved passage in one request, then code decides which ones reach the answering model. A passage carrying a hidden instruction can be dropped.
- [Double-checking citations](https://docs.typesafe.ai/cookbooks/citation_check). One Choice question decides whether the quote's context supports the claim, and low confidence flags the citation for review.
- [Guardrails for LLMs](https://docs.typesafe.ai/cookbooks/llm_guardrails). Screens every message going into and out of an LLM app with one request that scores hazards such as jailbreak attempts. Your code applies the thresholds.
- [SDE cascade](https://docs.typesafe.ai/cookbooks/sde_cascade). A two-stage structured-data-extraction cascade that gets most of a large reasoning model's quality at a fraction of the cost.
- [Date extraction](https://docs.typesafe.ai/cookbooks/date_extraction_cookbook). Asks Jev for the parts of a date named in a document, then resolves and validates them in code.
- [Pre-parsed value extraction](https://docs.typesafe.ai/cookbooks/pre_parsed_value_extraction_cookbook). Regexes find candidate emails, phone numbers, and amounts, then Jev selects the requested span so code can normalize it.
- [Hierarchical classification](https://docs.typesafe.ai/cookbooks/hierarchical_classification). Classifies documents through deep patent, retail product, biomedical, and source-code hierarchies with parallel beam search over Choice probabilities.
- [Autoresearch feature discovery](https://docs.typesafe.ai/cookbooks/autoresearch_feature_discovery). A loop that proposes questions, turns free text into numeric features, and uses model errors to improve a supervised CatBoost regressor.
- [Classification using confidence](https://docs.typesafe.ai/cookbooks/classification_using_confidence). Classifies SEC annual reports into 75 industry groups with one Choice each, then reads the answer's confidence to decide whether to report the group or the broader division above it.
- [Self-consistency with nouls](https://docs.typesafe.ai/cookbooks/consistency_noul_cookbook) and [with choices](https://docs.typesafe.ai/cookbooks/consistency_choice_cookbook). Route uncertain probabilities to human review, and add an uncertain outcome to moderation decisions.

## Patterns

From TypeSafe:

- [Speculative fan-out](https://docs.typesafe.ai/patterns/fan-out). Send many questions in one call. Speculative ones are fine, and your code decides what is relevant.
- [Confidence-gated routing](https://docs.typesafe.ai/patterns/confidence-routing). The answer tells you what, and confidence tells you whether to act.
- [Composite scoring](https://docs.typesafe.ai/patterns/composite-scoring). Break a complex judgment into atomic scores and combine them with weights you control in code.
- [Intent routing](https://docs.typesafe.ai/patterns/intent-routing). Classify incoming requests and send each to a deterministic handler, a specialist LLM, or a human.

Found while building the projects above:

- Generate with an LLM and judge with Jev. In 1kpapers the summaries cost $3.99 and the classification cost $0.08, so judging 1,018 papers cost about 50 times less than summarizing them. In will-it-hit, a separate LLM writes the rewrite because Jev cannot write text, and Jev then scores the result.
- Put many items in one state with one question per item. Send an array as the state, key the questions like `post_0` and `post_1`, and refer to items by path such as `posts[0]` in the instructions. One request covered a full feed scroll. The cost is accuracy, because TypeSafe's docs warn that unrelated material in the state lowers it. Keep batches small and check results by hand.
- Write rubrics from real outcomes. Score levels should describe concrete situations, and the instructions can carry observed numbers, such as how many reactions a flop and an outlier received, so scores do not drift upward.
- Do the arithmetic in code. will-it-hit combines its eight Score answers with fixed weights in code and uses thresholds for the verdict labels, which matches the advice on TypeSafe's limits page.
- Send requests from the extension's service worker. On linkedin.com a fetch from the page context to localhost failed in testing. A background service worker is not bound by the page's Content Security Policy.
- Select by test ids on LinkedIn, not class names. The feed uses hashed class names that change on every deploy. In September 2026 these worked: `div[role="listitem"][componentkey^="update-card-focus"]` for a post card, `[data-testid="expandable-text-box"]` for the body text, and the `aria-label` of `button[aria-label^="Open control menu for post by "]` for the author name. Expect them to break when LinkedIn changes its markup.

## Limits of Jev 1.13

From TypeSafe's [jaggedness page](https://docs.typesafe.ai/model-jaggedness/jev-1.13), last reviewed 2026-09-17, and the [models page](https://docs.typesafe.ai/models). Read the source pages before you build.

- It reads instructions literally. Write the exact condition and put boundary cases in the criteria.
- It is not a calculator. Keep math in code, which includes counting and date comparison, and use Jev to extract the parts.
- Use Score outputs for thresholds and ranking. Do not interpolate an exact number between two levels.
- Accuracy falls when the state carries content unrelated to the question. Filter first.
- Text in the state can steer the answer. An injected instruction or a misleading framing can move it, so test edge cases before deploying to many users.
- Separate questions are not guaranteed to agree. A Noul and a yes or no Choice about the same thing can return different numbers, so do not carry a threshold from one type to the other.
- It does not generate text. Use a generative model for that.
- English works best. Test other languages on your own content.
- The state plus the longest question is capped at 32k tokens and a whole request at 64k. Input is text only.

## Reported cost and latency

Except where a row says TypeSafe, these are numbers from the builders themselves and have not been independently verified.

| Source | Report |
| --- | --- |
| TypeSafe models page | $42 per billion input tokens ($0.042 per million). Output tokens are free. |
| TypeSafe parallel-questions cookbook | 12.2x cheaper and 10.0x faster than one call per question, with the same answers, on one document. |
| @nutlope | 8 cents to classify 1,018 papers, 256 ms median end-to-end per paper. The summaries from another model cost $3.99. |
| @cjzafir | $3.40 spent over 24 hours of testing. States that responses take roughly 70 to 500 ms and are 193.6x faster and 444.6x cheaper than frontier LLM workflows. Read the [full thread](https://x.com/cjzafir/status/2100991512020725788) for his other points. |
| @walidboulanouar | About $0.001 spent across roughly 100k tokens in one day of building. |

## Ideas nobody has shipped yet

These are hypotheses from a brainstorm on 2026-09-19, not products. Where a documented limit applies, the note says so.

Consumer and content:

- A tone meter for any text box. Score clarity and tone as you type in Gmail, Slack, or X. The hard part is attaching to other sites' inputs without breaking them.
- Live chat moderation. Classify each message in a fast Twitch or Discord chat with Noul questions for toxicity and spam. Messages written to fool the classifier are the main risk.
- A feed reranker that asks only when unsure. Score every item in an infinite feed for interest fit and ask the user a question only when confidence is low.

Developer tools:

- A model router. Score prompt complexity and choose a model tier before the call, so the router costs less than the call it routes. Watch for flapping between tiers on near-identical prompts.
- CI test selection. Choose which test tier to run from a commit diff. Diffs are large, so filter to changed paths and hunks before sending them.
- Abuse scoring at an API gateway. Ask a Noul question about request text. Rates and timing must be computed in code. Request text is controlled by the attacker, so this is the riskiest idea here.

Browser extensions:

- A job board scam filter. Score each listing card as real, ghost, or scam. Selectors differ by site and change often.
- A marketplace price-trap detector. Flag likely bait listings, and compare prices in code instead of asking the model.
- A fake review flagger. Score each review as the list loads. Text alone is a weak signal for reviews written to look organic.

Operations:

- Support ticket triage. A Choice for department, a Score for urgency, and a Noul for refund requests. Calibrate the criteria on the client's own ticket history.
- Inbound lead scoring on form submit. Needs closed-won and closed-lost examples to calibrate the rubric.
- An outbound message compliance check. One Noul per policy rule in one request before send. Tune the confidence thresholds so false positives do not push people to turn it off.
- A CRM hygiene sweep. Score record quality and possible duplicates. A duplicate check needs both records in the state.

## Tools

- [TypeSafe skill for Claude Code](https://github.com/typesafe-ai/skills). Install with `claude plugin marketplace add typesafe-ai/skills`, then `claude plugin install typesafe@typesafe-ai`. It points the agent at TypeSafe's live docs.
- [Docs index for agents](https://docs.typesafe.ai/llms.txt). Every docs page with a one-line description.
- [Python SDK](https://docs.typesafe.ai/sdk/python) and [JavaScript SDK](https://docs.typesafe.ai/sdk/javascript).
- [Use case map](https://docs.typesafe.ai/concepts/use-case-map). TypeSafe's own list of use cases by industry.

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md), then open a pull request.

## License

[CC0 1.0](LICENSE)
