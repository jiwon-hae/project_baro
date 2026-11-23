# Suno and Higgsfield MCP setup

Both providers publish official remote MCP servers. You need an MCP client that supports remote HTTP and OAuth, plus an account with each provider. Authorize each account in your browser. The URLs in [.env.example](.env.example) are public references for a future Baro client. Copying the file alone does not register the servers or sign you in.

| Provider | Official endpoint | Account and credits |
| --- | --- | --- |
| Suno | `https://api.suno.com/mcp` | A Suno account on any plan. Generations use plan credits. Check commercial-use eligibility before using a track in monetized videos. |
| Higgsfield | `https://mcp.higgsfield.ai/mcp` | An active paid Higgsfield subscription. MCP generations use plan credits. |

[Suno setup](https://help.suno.com/en/articles/14551425) · [Suno commercial use](https://help.suno.com/en/articles/9601665) · [Higgsfield setup](https://higgsfield.ai/creator-hub/help-center/integrations/how-do-i-connect-higgsfield-to-ai-agent)

## Connect in Codex

Run these once in your terminal. Each command adds a server to your Codex user configuration. Complete the browser authorization when prompted.

```sh
codex mcp add suno --url https://api.suno.com/mcp
codex mcp add higgsfield --url https://mcp.higgsfield.ai/mcp
codex mcp list
```

If sign-in was skipped or expired, run `codex mcp login suno` or `codex mcp login higgsfield`. Start a fresh Codex session and check `/mcp` in the CLI. Then ask each provider for its credit balance to verify account access without generating media. Keep OAuth tokens in the client's credential storage.

Higgsfield has announced that CIMD will be required for OAuth client registration starting January 1, 2027. If registration fails, update Codex and try `codex mcp login higgsfield --oauth-client-registration cimd`.

[Codex MCP setup](https://learn.chatgpt.com/docs/extend/mcp?surface=cli) · [Higgsfield registration change](https://higgsfield.ai/blog/higgsfield-mcp-moves-to-cimd)

## Connect in Claude or ChatGPT

- **Claude web or desktop** — Open Connectors, add a custom connector for each URL, then sign in to the provider.
- **ChatGPT** — Add Suno as a custom MCP server through Plugins if your workspace supports it. Install the official Higgsfield plugin and authorize your Higgsfield account. [Suno connection instructions](https://help.suno.com/en/articles/14551425) · [Higgsfield connection page](https://higgsfield.ai/mcp)

## Use the tools for the Baro pilot

1. **Suno theme** — Ask Suno to generate one instrumental theme with a short recognizable hook, no vocals, and a sparse section under narration. Review it in your Suno Library. Download the approved audio, cut the intro, bed, and outro, and store them in the media workspace's `theme_song/` folder.
2. **Higgsfield shot** — Ask Higgsfield to propose settings and report the credit cost if available for one five-second shot. For example, use a slow push-in on an approved reference frame. Confirm the shot and then ask it to generate with audio disabled if the model supports it. If a reference is needed, use the provider's upload flow. Download the finished clip from Higgsfield Assets and associate it with the shot ID in the episode timeline.
3. **Assembly** — Use the downloaded assets with the local FFmpeg workflow in [ROADMAP.md](ROADMAP.md). For a future unattended worker, use the separate Higgsfield developer API for programmatic generation and keep reusing the approved local Suno assets. [Higgsfield API quick start](https://open.higgsfield.ai/quick-start)
