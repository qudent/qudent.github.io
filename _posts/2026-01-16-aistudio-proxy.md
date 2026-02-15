# Google AI Studio protects API keys with a Google-discoverable proxy that's as exposed as the key itself

Google introduced the [Build mode](https://aistudio.google.com/build) vibe coding feature in Google AI Studio, which allows building and deploying web apps with a few prompts and clicks, in late 2025.

We all know that we shouldn’t put API keys into the frontend of an app, but don’t worry: According to the [documentation](https://ai.google.dev/gemini-api/docs/aistudio-build-mode?hl=en), in deployments from AI Studio, *"AI Studio acts as a proxy, replacing the placeholder with the end user’s API key, ensuring your key remains private."*

This page further states that moving logic to a server-side component for API key protection is *"not needed if you deploy using Cloud Run"* because the proxy handles it. The closest that the page comes to warning the user is that "While you can deploy your app to Cloud Run for a public URL, this setup will use your API key for all users' Gemini API calls", which implies that this is only a risk of app-related Gemini queries being billed to your account.

**Or... worry?**

While the API key is hidden, the proxy is completely exposed.

Even if you build an app that uses **zero AI features**, this proxy is deployed at a predictable URL. It accepts arbitrary requests to arbitrary Gemini models (including e.g. Veo 3.1, where one 8-second video currently costs [$3.20](https://ai.google.dev/gemini-api/docs/pricing?hl=de#veo-3.1)) without any rate limits or restrictions beyond what the key itself has.

Since Google API keys don’t allow setting hard spending limits (and Google Cloud billing alerts lead to warnings but don't stop usage), the outcome can be catastrophic.

## Find vulnerable endpoints with a single Google search
A single Google search for the URL scheme under which these apps are published reveals plenty of live apps whose authors apparently trusted the documentation. An attacker could use these open proxies to incur thousands of dollars in costs - generating fake videos or spam - billed directly to the developer's credit card.

As it stands, this proxy provides no security benefit compared to putting the API key in the frontend. It is essentially **"cargo cult security"**: it mimics the appearance of safety (hiding the credential) while ignoring the actual threat (unauthorized usage).

## How to mitigate this?
A long-term solution would be a bring-your-own-key flow within OAuth. Instead of the developer footing the bill, users should authorize a spending limit during Google OAuth, and the app should receive a new key billed to the user.

## Disclosure timeline
*   **Nov 30:** Reported to Google.
*   **Jan 13:** Google replied that this is "due to insufficient or incorrect documentation" (i.e., the proxy setup is considered to be working as intended).

## Test it out
Go to https://aistudio.google.com/build, deploy a basic web app, and verify the open proxy:

```bash
curl -X POST \
  "https://<your-app-url>/api-proxy/v1beta/models/gemini-3-pro-preview:generateContent" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "role": "user",
      "parts": [
        {"text": "Hello! Confirm you are Gemini 3 Pro running via an open proxy."}
      ]
    }]
  }'
```

Or list available models:
```bash
curl -X GET "https://<your-app-url>/api-proxy/v1beta/models?pageSize=1000"
```
