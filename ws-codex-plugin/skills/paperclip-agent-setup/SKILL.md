---
name: paperclip-agent-setup
description: Diagnose and configure Paperclip agents and AI connections, including custom OpenAI-compatible gateways. Use when creating an agent, connecting a model provider, or fixing why an agent cannot run; do not use for general Codex App or CLI setup.
---

# Paperclip Agent Setup

Configure the requested Paperclip agent against the intended provider and model, then verify the saved configuration. Keep provider connection, agent adapter, runtime binding, project authentication, and lifecycle state separate while diagnosing; a `connected` connection alone does not prove an agent can use a model.

## End-to-end workflow

Use this sequence for setup and troubleshooting requests:

1. **Classify the request.** Decide whether the user needs a provider connection, an agent created or updated, or an existing runtime diagnosed. Confirm the intended company and agent before changing records.
2. **Inspect the live instance.** Read the deployed Paperclip version, current API schema, adapter guidance, and relevant non-secret configuration. Treat the instance's current docs as authoritative because routes, flags, and adapters can change.
3. **Plan the smallest supported change.** Reuse a compatible connection when available. Confirm the provider protocol, model ID, adapter, authentication method, and desired lifecycle state before applying configuration.
4. **Apply through Paperclip controls.** Use the supported UI, API, or agent-hire flow. Keep connection setup, adapter configuration, runtime binding, project authentication, and lifecycle actions separate. Never write credentials into files or bypass validation.
5. **Read back and verify.** Re-read the saved connection and agent settings, run the environment check when available, and confirm any approval or paused state. Only run inference when requested.
6. **Report the outcome.** State what changed, what was verified, the agent's lifecycle state, and any remaining credential or project-authentication action.

## Inspect before changing

1. Confirm the Paperclip instance is reachable and note its version. Use the instance's current `/api/openapi.json` and `/api/llms/agent-configuration.txt` as the source of truth for available API routes and adapters; schemas and feature flags can change between releases.
2. Identify the intended company by name and ID. Read its agents, AI connections, connection pools, projects, and environments. Do not infer the company from the first API result when several exist.
3. For the selected AI connection, inspect only non-secret fields: provider, auth method, ownership, status, base URL, protocol, supported model IDs, and agent assignment. Never print or log credential values.
4. Confirm the user's intended agent name, role, model ID, provider protocol, and authentication method. Reuse an existing agent only when it matches that intent. If no agent exists and the intended role is unclear, ask before inventing one; a CEO is appropriate only when the user wants the company's first executive agent.

## Configure the provider connection

For a custom OpenAI-compatible gateway, confirm from the provider or user whether the adapter should use Responses, Chat Completions, or another supported protocol. Do not infer protocol compatibility from the word “OpenAI-compatible.” Configure the connection using the current API schema, normally with provider `openai`, method `api_key`, ownership `shared`, and routing fields for gateway kind, protocol, base URL, auth mode, and model IDs.

Example routing values for the Nonelinear setup in this workspace (replace them for other teams):

```json
{
  "kind": "gateway",
  "protocol": "responses",
  "baseUrl": "https://api.nonelinear.com.cn/v1",
  "auth": "bearer",
  "models": [{ "id": "gpt-6-luna", "label": "GPT-6 Luna" }]
}
```

Do not put a real key in this skill, source control, logs, or chat. Enter it through Paperclip's credential form or another approved secure input. If an edit or reconnect API call says it requires the selected credential, do not try to read the stored secret or bypass the API; ask the user to re-enter the key in Paperclip. A connection with an empty `models` list is incomplete even if its status says `connected`.

## Bind the agent correctly

Choose an adapter whose runtime and protocol match the requested model. Read its current adapter document before setting fields. For example, `codex_local` uses `adapterConfig.model` and supports `modelReasoningEffort`; do not assume it can reuse the Codex App sign-in or a machine-wide Codex configuration.

Use the binding form that matches the current Paperclip schema:

- A direct shared connection references the provider, method, `mode: "shared"`, connection ID, and grant ID returned by Paperclip.
- A `mode: "router"` binding references an enabled **connection pool**. It is not the same as an individual AI connection ID. Check the current `enableAiConnectionRouters` setting and pool records first. Create or enable a pool only when the requested workflow needs pool routing and its owning plugin is available; do not turn on experimental flags speculatively.

Use Paperclip's agent-hire workflow when available so the instance can apply its managed instructions, defaults, and approval behavior. Do not create a generic CEO or other role just to make the connection test pass. Set the explicit model ID in the adapter configuration and bind the intended AI connection in `runtimeConfig` as allowed by the live schema.

## Diagnose authentication and lifecycle errors

- If Paperclip reports `ai_connection_incompatible` or that project authentication conflicts with a selected AI connection, inspect the selected project and environment authentication settings and compare them with the provider, method, and model protocol. Reconcile the conflicting setting through supported Paperclip controls; do not bypass validation or edit the database directly.
- If a router binding reports that a connection pool must be enabled, verify that the binding contains a real pool ID and that the pool is enabled. Do not substitute the underlying connection ID.
- If an agent is `paused` or `pending_approval`, inspect its approval and lifecycle state. Do not approve, resume, or weaken sandbox/approval settings unless the user requested that action. “No approval prompts” does not automatically mean “disable the sandbox.”

## Verify and report

After changes, re-read the connection and agent configuration and check that the model ID, base URL, protocol, binding, company, and adapter match the request. Redact secrets from all output. Use Paperclip's adapter environment-check endpoint when available to catch local prerequisites and authentication conflicts. Treat an environment check as distinct from a real model response.

Run a minimal inference only when the user requested runtime verification or the task clearly requires it. It may incur provider charges. Do not repeatedly retry an unchanged authentication or compatibility error; report the exact blocker and the specific secure input or setting needed to continue.

In the handoff, state what changed, what was verified, whether the agent is active or paused, and any remaining credential or project-authentication step. Do not claim the agent works solely because the connection status is `connected` or because its configuration record was created.
