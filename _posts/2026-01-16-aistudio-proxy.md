# Google AI Studio protects API keys with a Google-discoverable proxy that's as exposed as the key itself

Google introduced the [Build mode](https://aistudio.google.com/build) vibe coding feature in Google AI Studio, which allows building and deploying web apps with a few prompts and clicks, in late 2025.

We all know that we shouldn’t put API keys into the frontend of an app, but don’t worry: According to the [documentation](https://ai.google.dev/gemini-api/docs/aistudio-build-mode?hl=en), in deployments from AI Studio, *"AI Studio acts as a proxy, replacing the placeholder with the end user’s API key, ensuring your key remains private."*

This page further states that moving logic to a server-side component for API key protection is *"not needed if you deploy using Cloud Run"* because the proxy handles it. The closest that the page comes to warning the user is that "While you can deploy your app to Cloud Run for a public URL, this setup will use your API key for all users' Gemini API calls", which implies that this is only a risk of app-related Gemini queries being billed to your account.

**Or... worry?**

While the API key is hidden, the proxy is completely exposed.

Even if you build an app that uses **zero AI features**, this proxy is deployed at a predictable URL. It accepts arbitrary requests to arbitrary Gemini models (including e.g. Veo 3.1, where one 8-second video currently costs [$3.20](https://ai.google.dev/gemini-api/docs/pricing?hl=de#veo-3.1)) without any rate limits or restrictions beyond what the key itself has.

Since Google API keys don’t allow setting hard spending limits (and Google Cloud billing alerts lead to warnings but don't stop usage), the outcome can be catastrophic.

## Find vulnerable endpoints with a single Google search
A single Google (or Twitter, or Hacker News) search for the URL scheme under which these apps are published (which anyone can find easily enough by testing it out - namely, "us-west1.run.app" does the trick, some apps also appear in other regions) reveals plenty of live apps whose authors apparently trusted the documentation. An attacker could use these open proxies to incur thousands of dollars in costs - generating fake videos or spam - billed directly to the developer's credit card.

As it stands, this proxy provides no security benefit compared to putting the API key in the frontend. It is essentially **"cargo cult security"**: it mimics the appearance of safety (hiding the credential) while ignoring the actual threat (unauthorized usage).

## How to mitigate this?
A long-term solution would be a bring-your-own-key flow within OAuth. Instead of the developer footing the bill, users should authorize a spending limit during Google OAuth, and the app should receive a new key billed to the user.

# Update (reported March 18):
## Unauthorized file access
The Gemini API allows uploading files (which can be put into context), which are then accessible. I confirmed that the open proxy also allows downloading those files.

## Amplified impact by official promotion
- Oct 27: A Google DevRel [showcased](https://x.com/meteatamel/status/1982781124116914508) a public app developed with the vulnerable flow (technically a Google-owned deployment)
- Dec 31 (after report submission): Google announced a vibecoding competition called [New Year, New You](https://dev.to/challenges/new-year-new-you-google-ai-2025-12-31) that required online publishing and encouraged the vulnerable deployment flow.

## Related: Truffle Security report
The findings above were inspired by a [Truffle security report](https://trufflesecurity.com/blog/google-api-keys-werent-secrets-but-then-gemini-changed-the-rules) published on February 25, which points out a similar issue: Google API keys were documented as not secrets for a long time, but adding Gemini to a project exposed the File API and Gemini access to them. By scanning the Common Crawl dataset, Truffle found almost 3000 thousand API keys with open Gemini access lying around in public, including in Google-owned properties. I didn't perform such an exhaustive list for my vulnerability, assuming that pointing to the Google search page would be enough - scoped [search on Twitter](https://x.com/search?q=us-west1.run.app%20until%3A2025-02-31)) appears to produce at least hundreds of apps at least as well, with most published after I disclosed the issue (and nothing was being done to update the documentation). I recall finding one app with document analysis features belonging to a bank on Google.

## Disclosure and reward
The disclosure experience was a bit bumpy (see timeline).

**Pro-Tips** for my future self are to look for the issues pointed out in the update (data access rather than mere billing abuse, official Google deployments or DevRel encouragements of the vulnerable flow). Submitting a concrete, long list of vulnerable endpoints rather than gesturing at the Google search may have been helpful as well.

## Disclosure and vulnerability timeline
*   **Oct 27, 2025:** Google DevRel [showcases](https://x.com/meteatamel/status/1982781124116914508) a public app developed with the vulnerable flow
*   **Nov 30, 2025:** Reported to Google. (The initial report contained neither the file access vulnerability nor the Oct 27 and Dec 31 promotional examples).
*   **Dec 15, 2025:** Someone (else) posts a [Reddit comment](https://old.reddit.com/r/Bard/comments/1pdxgpv/how_does_an_app_built_with_ai_studio_hide_the/nu3u4bz/) (which I discovered later) pointing out the basic issue (without cross-referencing the documentation or Google discoverability), but doesn't get traction
*   **Dec 31, 2025:** Google-sponsored ["New Year, New You" competition](https://dev.to/challenges/new-year-new-you-google-ai-2025-12-31) announced, which requires publishing and posting a site and encourages the vulnerable deployment flow
*   **Jan 13:** Reward denied because the issue is considered to be "due to insufficient or incorrect documentation", asserting that the proxy setup itself is a valid design.
*   **Jan 13:** [Truffle security report](https://trufflesecurity.com/blog/google-api-keys-werent-secrets-but-then-gemini-changed-the-rules) (published Feb 25) gets classified as "Single-Service Privilege Escalation, READ" (Tier 1, $20,000).
*   **Jan 31:** Closed with status INTENDED_BEHAVIOR.
*   **Feb 15:** Confirmed that the relevant parts of the documentation haven't changed.
*   **Feb 25:** Truffle Security report gets published and goes viral on Hacker News one day later.
*   **Feb 26:** No fix, but by now, the publishing app flow has changed. It includes a warning that "Before launching to real customers, verify your app's external software dependencies." In line with the DevRel post and the competition, this confirms to a user that launching these exposed apps to the public internet isn't just an edge case, but Google's expected, supported use case.
*   **Mar 18:** Abuse Reward ($1,337) issued with the following justification: "Exploitation likelihood is medium. Issue qualified as an abuse-related methodology with medium impact."
*   **Mar 18:** Confirmed that Google had changed the deployment flow to prevent the vulnerable behavior (to my memory, breaking all AI features in new deployments at some point, though existing apps remained exposed).
*   **Mar 18:** An email is sent to AI studio users announcing enforceable spending caps.
*   **Mar 18:** Appealed pointing out facts in Update which make the situation comparable to the Truffle report
*   **Mar 31:** Classification appeal rejected (no reasoning given)

## Test it out (deprecated)
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
