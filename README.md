# voyant

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Voyant.io is a brand-context platform that turns a company's positioning, messaging, personas,
products, pricing, and competitive intelligence into structured context streams that any AI tool or
agent can read at generation time, so AI-produced copy stays on-message instead of drifting or
hallucinating claims.

- Website: https://www.voyant.io/
- API docs (Swagger UI): https://voice-forge-production.up.railway.app/docs
- OpenAPI: https://voice-forge-production.up.railway.app/openapi.json
- MCP server: https://voice-forge-production.up.railway.app/mcp

## What is in this profile

| Artifact | What it holds |
|---|---|
| `openapi/` | The provider's own OpenAPI 3.1.0, saved verbatim — 783 operations, 79 tags, 266 schemas |
| `mcp/` | The hosted `voyant-mcp` 1.1.0 server (15 tools, anonymously introspectable) and the tool crosswalk binding each tool to its backing operationId |
| `well-known/` | The two real documents served at `/.well-known/` — a training-policy `llms.txt` and an inference-control `context.txt` — plus the full negative probe |
| `llms/` | The published llms.txt, verbatim |
| `authentication/`, `security/` | Derived auth profile and probed TLS/DNS posture |
| `errors/`, `conventions/`, `data-model/`, `lifecycle/`, `conformance/` | Derived from the contract and from live probes |
| `skills/` | Three generated agent skills, every operationId verified against the spec |
| `agentic-access/` | Recommended `x-agentic-access` contracts for all 787 classified operations |

## Notes on the evidence

`www.voyant.io` is a single-page app that answers **HTTP 200 with the same HTML shell for every
unknown path**. Every probe in this profile was diffed against a control path
(`/zzz-control-nonsense-xyz789`) before being credited. Under that test the root `/llms.txt`,
`/apis.json`, `/.well-known/security.txt` and both A2A agent-card paths are soft 404s and are
recorded as absent; `/.well-known/llms.txt`, `/.well-known/context.txt` and `/openapi.json` are
real. No A2A agent card was found, so no `a2a/` artifact was written.

The company is Voyant.io; the API is served from a Railway hostname under the internal service name
**VoiceForge**, and both names appear in the live contract.
