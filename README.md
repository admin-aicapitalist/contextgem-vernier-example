# ContextGem + Vernier: check an agent's payment against the invoice

An agent that pays invoices has to read the invoice right and pay what it read.
[ContextGem](https://github.com/shcherbak-ai/contextgem) extracts the fields, each with the
sentence it came from. [Vernier](https://www.aicapitalist.net/vernier) checks the payment against
that extraction before it runs.

```
invoice.txt --ContextGem--> fields + sources --agent--> pay_invoice(...) --Vernier--> ALLOW / BLOCK / ESCALATE
```

## Run it

```sh
export VERNIER_ADMIN_KEY=...      # get one at https://www.aicapitalist.net/vernier
uv run demo.py --offline          # reads extraction.json: nothing to install, no LLM key
```

To extract the invoice live with ContextGem first:

```sh
export CONTEXTGEM_MODEL=openai/gpt-4o-mini   # any LiteLLM model
export CONTEXTGEM_API_KEY=...
uv run --extra extract demo.py
```

## What it shows

The agent tries three payments and asks Vernier before each one:

| Payment | Verdict | Why |
| --- | --- | --- |
| The IBAN and total from the invoice | ALLOW | everything matches the extraction, and the user said yes |
| An IBAN from a "we changed banks" email | BLOCK | that IBAN appears nowhere in the extraction |
| Ten times the total | ESCALATE | the amount appears nowhere, so a person decides |

The checks are written out in `demo.py` (`VERIFIER`) and set on the agent with one call. A payment
goes through only when every check passes. Every verdict comes with a signed receipt.

The invoice, the companies and the IBANs are synthetic.
