# Mule Custom Policy Template

![MuleSoft](https://img.shields.io/badge/MuleSoft-4.1.1-00A0DF?logo=mulesoft&logoColor=white)

A minimal, deployable skeleton for building a **custom MuleSoft API Gateway policy** and publishing it to Anypoint Exchange as a reusable, pluggable asset. Out of the box the policy wraps every request through `execute-next` and rewrites the response payload — it exists as the scaffolding you extend when the built-in Anypoint policies (rate limiting, client ID enforcement, etc.) don't cover a custom requirement, without having to set up the Exchange asset structure from scratch each time.

## How this differs from my other API/security repos

This is the only repo in the cluster packaged as an actual `mule-policy` Maven artifact — it's a *policy*, not an application, meant to be applied to any number of APIs from Exchange. `mule-custom-offlinepolicy` looks similar by name but is really a standalone Mule application that simulates offline behavior; the `apidev-*` repos are client applications demonstrating inbound/outbound auth patterns, not gateway policies at all.

## Tech Stack

- MuleSoft Runtime 4.1.1 (Mule Policy packaging)
- Mule HTTP Policy module (`http-policy`)
- Maven (`mule-maven-plugin`) for build and Exchange publishing
- Anypoint Exchange as the distribution target

## How it works

The policy is defined as an `http-policy:proxy` block (`src/main/mule/template.xml`) that:

1. Wraps the inbound HTTP request via `<http-policy:source>`.
2. Calls `<http-policy:execute-next/>` to let the request continue through the rest of the API's flow chain.
3. Overwrites the response payload (currently a placeholder `"Hello World!"`) on the way back out.
4. Has stub `on-error-continue` / `on-error-propagate` error-handler blocks ready for real error-handling logic.

The policy's metadata lives in three descriptor files that Exchange needs to register and configure it:

- `my-custom-policy.yaml` — the policy definition (id, category, configuration schema)
- `my-custom-policy.json` — an example applied-policy configuration (which API/order it binds to)
- `mule-artifact.json` — minimum Mule runtime version

## Getting Started

Requires Maven and a MuleSoft Anypoint Platform account (Exchange access) to actually deploy.

```bash
# Build the policy artifact
mvn clean package

# Publish to Anypoint Exchange (update the groupId / exchange.url in pom.xml first)
mvn deploy
```

The included `settings.xml` shows where Exchange server credentials go for the Maven deploy — replace the placeholder `username`/`password` with your own Anypoint credentials (or better, inject them via Maven's encrypted settings) before publishing.

To actually see the policy in action, apply it to an API in Anypoint API Manager after publishing, or test the flow logic locally against a Mule 4.1.x runtime.

## Project Structure

```
├── src/main/mule/template.xml   # the policy flow (http-policy:proxy)
├── my-custom-policy.yaml        # Exchange policy definition/metadata
├── my-custom-policy.json        # example applied-policy config
├── mule-artifact.json           # min Mule runtime version
├── pom.xml                      # Maven build + Exchange deploy config
└── settings.xml                 # Maven server credentials template
```
