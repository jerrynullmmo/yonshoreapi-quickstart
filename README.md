# YonshoreAPI Quickstart

YonshoreAPI exposes an OpenAI-compatible API for developers in supported regions.

## Before you start

1. Confirm that both YonshoreAPI and the relevant upstream provider support your location and use case.
2. Create an account at <https://api.yonshore.com/register>.
3. Create an API key in the dashboard.
4. Select a current model from <https://api.yonshore.com/api/pricing>.
5. Confirm that the account has usable balance. Recharge in the dashboard only if needed; model calls are paid usage.

YonshoreAPI is currently unavailable in mainland China. Do not use a proxy, VPN, shared account, forged header, or remote-control service to bypass regional restrictions.

## curl

Requirement: a shell with `curl` installed. macOS and most Linux distributions include it.

```bash
export OPENAI_BASE_URL="https://api.yonshore.com/v1"
export OPENAI_API_KEY="your_key_here"
export MODEL="replace-with-a-current-public-model"

curl "$OPENAI_BASE_URL/chat/completions" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "'"$MODEL"'",
    "messages": [{"role": "user", "content": "Reply with OK"}],
    "max_tokens": 16
  }'
```

A successful response is JSON containing a non-empty `choices[0].message.content`, a returned `model`, and usage fields. An HTTP 200 response containing an `error` object is not a successful model call.

## Python

Install Python 3.9+ and the official OpenAI SDK in a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install --upgrade openai
```

```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["OPENAI_API_KEY"],
    base_url="https://api.yonshore.com/v1",
)

response = client.chat.completions.create(
    model=os.environ["MODEL"],
    messages=[{"role": "user", "content": "Reply with OK"}],
    max_tokens=16,
)
print(response.choices[0].message.content)
```

## Node.js

Install Node.js 18+ and the SDK in an existing project:

```bash
npm install openai
```

Save the following as an `.mjs` file, or use a project with `"type": "module"`.

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
  baseURL: "https://api.yonshore.com/v1",
});

const response = await client.chat.completions.create({
  model: process.env.MODEL,
  messages: [{ role: "user", content: "Reply with OK" }],
  max_tokens: 16,
});

console.log(response.choices[0].message.content);
```

## Common failures

- `401` or `invalid_api_key`: confirm the Key is active and sent in the Bearer header; never paste it into an issue or screenshot.
- `400` or model-not-found: copy the current model name from the pricing endpoint instead of an old article.
- Insufficient balance or quota: check usable balance and the account ledger; recharge only through the dashboard if you choose to continue.
- `429`: reduce request rate and check the current account or model limits.
- `5xx`, gateway error, or timeout: do not blindly retry a non-idempotent workflow. Record the timestamp and request identifier, remove secrets, and check status before retrying.

When finished, remove secrets from the shell session:

```bash
unset OPENAI_API_KEY OPENAI_BASE_URL MODEL
```

## Live references

- Status: <https://api.yonshore.com/api/status>
- Pricing and model list: <https://api.yonshore.com/api/pricing>
- Documentation: <https://api.yonshore.com/about>

The public status and pricing endpoints show product configuration, not a guarantee that every request will succeed. Run a small call with your intended model before migrating production traffic.
