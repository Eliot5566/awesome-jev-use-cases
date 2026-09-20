# Keyword research for Jev content

Snapshot 2026-09-20. Volumes are monthly US Google searches from a keyword-data provider called through treg (about $0.001 per call). Live results pages were read through treg's Google SERP endpoint. All 112 keywords found are in [data/keywords.csv](../data/keywords.csv).

## How this was done

1. Started from 20 seed phrases that describe jobs Jev does: routing, classification, judging, guardrails, moderation, spam and triage, plus the company and model names.
2. Asked treg for related keyword ideas for each seed, which returns volume, competition and cost per click.
3. Read the top 10 Google results for five keywords to check what the searcher wants, before deciding what kind of page to write.

## Keywords worth targeting

| Keyword | Monthly searches | Competition | CPC (USD) |
| --- | --- | --- | --- |
| openrouter ai | 12,100 | MEDIUM | $4.21 |
| small language model | 2,900 | LOW | $1.64 |
| claude code router | 2,900 | LOW | $8.53 |
| openrouter free models | 2,400 | LOW | $5.99 |
| llm as a judge | 2,400 | LOW | $12.57 |
| llm gateway | 1,600 | MEDIUM | $15.39 |
| ai guardrails | 1,000 | MEDIUM | $24.64 |
| llm router | 720 | MEDIUM | $7.09 |
| openrouter alternative | 720 | MEDIUM | $11.20 |
| typesafe ai | 320 | LOW | - |
| semantic router | 210 | LOW | $5.34 |
| prompt injection ai | 140 | MEDIUM | $13.48 |
| ai slop detector | 260 | LOW | $3.17 |
| content moderation api | 50 | MEDIUM | $19.36 |
| ai spam filter | 40 | MEDIUM | $22.96 |
| llm classifier | 40 | LOW | - |

## What the search results say

| Keyword | Who ranks now | What it means for a page |
| --- | --- | --- |
| claude code router | A GitHub repo (musistudio/claude-code-router) holds positions 1 and 2, then a Hacker News thread, a vendor guide, a Reddit post | Searchers want a tool to install. A "how to route Claude Code with Jev" tutorial fits. Jev-based routers already exist in this list, such as gargpratyush/jev-router. |
| llm as a judge | An arXiv survey, Langfuse docs, Wikipedia, and guides from Confident AI, Evidently and MLflow | Searchers want an explainer. Jev is a cheap, fast judge that returns a probability, so a comparison "LLM-as-a-judge vs a typed decision model" has a clear angle. |
| ai guardrails | IBM, the Guardrails AI repo, F5, an Australian government page, GeeksforGeeks | Mostly enterprise explainers. The highest cost per click in the set ($24.64) means advertisers pay well. The pi-warden project in this list is a working example. |
| llm router | An academic library (ulab-uiuc/LLMRouter), Braintrust's "best routers in 2026", TrueFoundry, Reddit r/LocalLLaMA, NVIDIA | A roundup page. Jev routers belong in "best LLM routers" lists. |
| typesafe ai | The company's own pages fill positions 1 to 6 | Nothing to win here. Demand is about 320 a month and rising fast, but the company owns the page. |

## Read these numbers with care

- "ai detector" shows about 5,000,000 searches a month because the seed "ai slop detector" surfaced it. That is people checking student essays, not LinkedIn slop. It is not demand for a slop detector. "ai slop detector" itself is about 260 a month.
- "jev" alone is about 4,400 a month, but the word has other meanings, so it does not measure demand for the model.
- Terms with no volume, such as "jev model", "jev api" and "system one model", are too new for the provider. Blank means unknown, not zero.
- Volume is Google US only. It does not include X, Reddit, GitHub or Hacker News, where most Jev discussion happens.

## Where people actually talk about Jev

Search volume undercounts this model because it launched days ago and the conversation is on X and GitHub. Two other signals in this repo:

- GitHub: 150 or more repositories mention Jev or TypeSafe, and 111 of them appeared in a single search sweep. See [data/more-repos.csv](../data/more-repos.csv).
- X: 74 demo posts with video, with the top one at 10,435 likes. See [data/demos.csv](../data/demos.csv).

## Suggested content, in order

1. A tutorial for "claude code router", built on a working Jev router from this list.
2. A comparison for "llm as a judge": cost, speed and calibration against a general LLM, using the numbers in the cost table and the limits section.
3. A page for "llm router" that ranks the Jev routers by stars.
4. A guardrails walkthrough that uses pi-warden as the example.

Each page needs a real test run before publishing. This research says what people search for. It does not say Jev beats the alternatives, and the [limits](../README.md#limits-of-jev-113) still apply.
