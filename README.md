# LOY'S PISOWIFI Messenger AI (Bisaya)

A starter project for an AI customer-service bot that replies in Bisaya/Cebuano to incoming messages sent to your Facebook **Page** using Meta's official Messenger Platform.

## Important limitations

- This connects to a Facebook Page inbox. It does **not** add a bot to ordinary Messenger group chats or automate a personal Facebook account.
- Meta permissions, webhook subscriptions, app mode/review, and messaging policies apply. The recipient normally must have messaged the Page recently (standard 24-hour messaging window), unless a permitted exception applies.
- This project does not include your tokens. You must configure them privately in your hosting provider.
- Review replies before launch; AI can make mistakes. Add your real service list, approved prices, coverage, and escalation details before relying on it.

## Features

- Meta webhook verification endpoint: `/webhook`
- HMAC SHA-256 webhook signature verification
- Sends replies through Meta's Send API
- Bisaya-first customer-service system prompt
- Safe fallback when AI key is absent
- `/health` endpoint for hosting health checks
- Environment variables for secrets (no secrets in source code)

## Deploy on Render

1. Create a GitHub repository and upload these files. Do **not** upload `.env` or real credentials.
2. In Render, create a **New Web Service** from that repository. Render can also use `render.yaml`.
3. Add the environment variables listed in `.env.example` in Render's Environment settings.
4. Deploy. Copy the public HTTPS service URL, e.g. `https://your-service.onrender.com`.
5. Check `https://your-service.onrender.com/health`; it should return JSON with `"ok": true`.

## Connect Meta Messenger

1. Go to [Meta for Developers](https://developers.facebook.com/) and create/configure an app with the Messenger Platform use case.
2. Connect the Facebook Page for **LOY'S PISOWIFI Computer Digital Services Bohol**.
3. Generate a Page access token and store it as `FACEBOOK_PAGE_ACCESS_TOKEN` in Render.
4. Copy the app secret to `FACEBOOK_APP_SECRET`.
5. Set `FACEBOOK_VERIFY_TOKEN` to a long random string you choose; use the same value in Meta's webhook configuration.
6. In Messenger/Webhooks settings, set callback URL to `https://YOUR-SERVICE.onrender.com/webhook`, enter the same verify token, verify/save, and subscribe the Page to the `messages` webhook field.
7. Ensure the app has the permissions needed for your use case, including `pages_messaging`, and complete any required app review / business verification before serving people outside test roles.
8. Send a test message to the Page from an allowed test account.

## Environment variables

| Variable | Purpose |
|---|---|
| `FACEBOOK_VERIFY_TOKEN` | Secret string used for webhook verification |
| `FACEBOOK_APP_SECRET` | Meta app secret for webhook signature validation |
| `FACEBOOK_PAGE_ACCESS_TOKEN` | Token used to send Page messages |
| `OPENAI_API_KEY` | API key for AI-generated replies |
| `OPENAI_MODEL` | Model name, default `gpt-4o-mini` |
| `GRAPH_API_VERSION` | Meta Graph API version, default `v23.0` |
| `BUSINESS_PHONE` | Staff contact shown in fallback replies |

## Test locally

Requires Node.js 20+.

```bash
cp .env.example .env
# Fill in your private values
npm install
npm start
```

Meta needs a public HTTPS webhook, so localhost alone cannot receive real Messenger webhooks.

## Customize business knowledge

Edit `SYSTEM_PROMPT` in `app.js` to include only verified business details such as:
- Services actually offered
- Approved voucher prices and validity
- Service coverage and hours
- Installation / troubleshooting workflow
- Escalation contact

Never put customer passwords, tokens, or private account data in the prompt.

## Troubleshooting

- **Webhook verification fails:** make sure the URL ends in `/webhook` and the verify token exactly matches.
- **Replies do not send:** check the Page access token, Page ID/account connection, permissions, app mode, webhook subscription, and Meta error logs.
- **Signature errors:** ensure requests reach Express unchanged; a proxy/middleware must not parse/rewrite the raw request body before signature verification.
- **No AI reply:** verify `OPENAI_API_KEY` and model access in your hosting environment.
