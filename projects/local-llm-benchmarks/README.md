# Local LLM Experiments: Speed and Numeric Fidelity

**Abhinay Vallamkonda | CPU inference, structured data and backend integration**

I explored local language models for converting structured KPI JSON into concise summaries, focusing on response time, completeness and preservation of numeric facts. Related development included a FastAPI service and DuckDB integration.

## Evidence and scope

These are historical experiment notes transcribed from terminal/API results shared in August 2026 conversations. Original benchmark scripts and raw log files are not bundled here, and these tests have not been rerun for this publication. Timing labels are preserved rather than treating different measurements as equivalent.

This page publishes model measurements and general observations. Business records, financial amounts, customer/channel names, credentials and internal implementation source are excluded.

## Test environment

| Component | Recorded environment |
| --- | --- |
| Processor | AMD Ryzen 5 7520U, 4 cores / 8 threads |
| Memory | 16 GB RAM |
| OS | Windows 11 with WSL2 / Ubuntu |
| Compute | CPU inference; CUDA unavailable in the recorded Python session |
| Runtimes explored | Ollama; Python with Hugging Face Transformers / PEFT |
| Phi run setting | Context size 2048 reported for Phi-4-mini; not established for every other run |

Exact runtime versions, quantization, prompts and sampling settings were not consistently retained across all runs. This is not a controlled model leaderboard.

## Recorded response times — 19 August 2026

| Model | Run 1 (s) | Run 2 (s) | Run 3 (s) | Input tokens | Output tokens per run | Outcome noted |
| --- | ---: | ---: | ---: | ---: | ---: | --- |
| Phi-4-mini | 137.165 | 25.811 | 27.307 | 1,249 | 120 | Output truncated before covering all requested metrics |
| Qwen2.5 1.5B | 58.266 | 10.718 | 10.614 | 1,396 | 120 | Incorrect highest-revenue category and incomplete output |

The logs called these measurements **response time**. The retained excerpts do not establish whether that field included every loading/network overhead, so it is not relabelled as generation-only time. Runs are numbered in recorded order; cold/warm state is not verified.

Reported generation rates, a separate metric:

| Model | Run 1 (tokens/s) | Run 2 (tokens/s) | Run 3 (tokens/s) |
| --- | ---: | ---: | ---: |
| Phi-4-mini | 4.52 | 4.90 | 4.58 |
| Qwen2.5 1.5B | 11.57 | 12.10 | 12.14 |

Qwen's recorded response times were lower in these experiments, but prompt lengths differed and the output contained errors. These runs do not establish general superiority or production suitability.

## Structured-output experiments — 19 August 2026

| Configuration | Timing field | Recorded seconds | Output observation |
| --- | --- | ---: | --- |
| SmolLM2-1.7B-Instruct + SmolStruct-1.7B adapter | Loading | 36.846 | See generation result below |
| Same configuration | Generation | 259.858 | 1,207 input tokens, 2 output tokens; visible output was only `}` |
| Osmosis-Structure-0.6B, Q4_K_M via Ollama | Prompt evaluation | 11.701953 | Empty response; reported eval_count = 1 |
| Same Osmosis request | Total duration | 12.431873 | No usable summary |

Model identifiers recorded:
- `HuggingFaceTB/SmolLM2-1.7B-Instruct`
- `tugrulkaya/smolstruct-1.7b`
- `hf.co/osmosis-ai/Osmosis-Structure-0.6B:Q4_K_M`

Loading, prompt evaluation, generation and total duration are different measurements. Do not compare these rows as if they were the same benchmark.

## Separate API smoke test — 24 August 2026

A short Qwen2.5 1.5B request returned a greeting/readiness message. It was **not a successful KPI-summary benchmark**.

| API field | Raw nanoseconds | Seconds |
| --- | ---: | ---: |
| total_duration | 5,519,915,260 | 5.519915260 |
| load_duration | 783,348,580 | 0.783348580 |
| prompt_eval_duration | 106,539,000 | 0.106539000 |
| eval_duration | 4,609,751,000 | 4.609751000 |

The request used 42 prompt tokens and 30 generated tokens. Total duration may include overhead beyond the listed components.

## Other models explored

Qwen2.5 3B, Qwen3 0.6B, Llama3.2 1B/3B and Gemma3 4B were discussed, installed or used during the broader project. Comparable verified timings were not recovered for this page, so no timings are assigned to them.

The related Qwen3 0.6B service work included FastAPI and DuckDB. A working API response does not by itself demonstrate numeric fidelity or a latency target.

## Numeric fidelity: what matters

The observed problems included an incorrect category ranking, missing metrics and unusable output. These are distinct from simply checking whether a number appears somewhere in a sentence.

A reproducible evaluation should check:
- Exact values and decimal precision against source fields.
- Whether each value is attached to the correct metric.
- Rankings against deterministic calculations.
- Coverage of requested metrics.
- Unsupported claims and incomplete output.

No aggregate numeric-accuracy percentage is published: the full scoring method and raw evaluation set are not available here.

### Synthetic example for future tests

This example was created for public illustration. It is not the original benchmark input and no measured timing above belongs to it.

```json
{
  "bills": 12,
  "guests": 24,
  "gross_amount": "15000.50",
  "discount": "500.25",
  "net_amount": "14500.25"
}
```

Expected facts: 12 bills, 24 guests, gross amount 15000.50, discount 500.25 and net amount 14500.25. Checks should reject altered values, swapped labels or omitted requested metrics. Use decimal arithmetic for monetary checks.

## Target versus measured result

The project aimed for **2–5 second** responses on a CPU. This was a target, not a demonstrated result across the tested models. None of the recorded summary response times above establishes that target with a complete, correct summary.

## Next reproducible benchmark

Preserve exact model tags, quantization, runtime versions, prompts and generation settings. Separate cold-start and repeated warm runs. Save source data, raw responses, timing fields, token counts and field-level validation results. Publish the fixtures only with synthetic or explicitly shareable data.

This case study documents experimentation and failure analysis, not a production deployment or a guaranteed model performance claim.
