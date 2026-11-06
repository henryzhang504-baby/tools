# ws-codex-plugin

A Codex plugin that guides safe setup and troubleshooting for Paperclip agents and AI connections, including custom OpenAI-compatible gateways.

## Install from a local checkout

From the repository root, register this directory as a local marketplace and install the plugin:

```powershell
codex plugin marketplace add .\ws-codex-plugin
codex plugin add ws-codex-plugin@ws-codex-plugin-local
codex plugin list
```

The plugin includes a marketplace manifest at `.agents/plugins/marketplace.json`. Its skill is in `skills/paperclip-agent-setup/`.

## Workflow

The skill follows a consistent agent-setup workflow:

1. Identify whether the request concerns a provider connection, agent configuration, or runtime troubleshooting.
2. Inspect the target Paperclip instance and its live API/configuration docs; confirm the company and intended agent.
3. Reuse a matching connection when possible, then configure the adapter, model, and runtime binding using supported Paperclip controls.
4. Read the saved configuration back and run available environment checks. Run inference only when requested.
5. Report the resulting configuration, lifecycle state, verification performed, and any remaining secure credential step.

See [`SKILL.md`](./skills/paperclip-agent-setup/SKILL.md) for the detailed procedure and safety constraints.

## Source reference

This plugin is an independent Codex integration guide for the [Paperclip project](https://github.com/paperclipai/paperclip). It is not an official Paperclip component. The Paperclip project is cited as the upstream product and API reference; this repository does not include Paperclip application code.

## License

This plugin is licensed under the MIT License. See [`LICENSE`](./LICENSE).

To publish a packaged release, push a tag such as `ws-codex-plugin-v1.1.0`. The repository workflow creates a GitHub release and attaches the plugin ZIP.
