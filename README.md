# Stresix MCP server

Load test your app from Claude Code, Cursor or any MCP client before you launch.

Tell your agent "load test this app before I launch". Stresix builds your GitHub repo or Docker image on a fresh cloud server, ramps simulated users at it from a second server in the same datacenter, and hands the report back to the agent: what ran out first, p95 latency, error rate and what server size to run it on. No k6 script to write.

The server is hosted at `https://stresix.com/mcp` (MCP over Streamable HTTP). Nothing to install.

## Setup

1. Sign in at [stresix.com](https://stresix.com) with GitHub, Google or email.
2. Open Settings, then Keys for agents, and create a key. It is shown once.
3. Add the server to your client.

**Claude Code**

```sh
claude mcp add --transport http stresix https://stresix.com/mcp \
  --header "Authorization: Bearer YOUR_STRESIX_KEY"
```

**Cursor** (`~/.cursor/mcp.json`, or `.cursor/mcp.json` in a project)

```json
{
  "mcpServers": {
    "stresix": {
      "url": "https://stresix.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_STRESIX_KEY" }
    }
  }
}
```

**Windsurf** (`~/.codeium/windsurf/mcp_config.json`)

```json
{
  "mcpServers": {
    "stresix": {
      "serverUrl": "https://stresix.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_STRESIX_KEY" }
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`)

```json
{
  "servers": {
    "stresix": {
      "type": "http",
      "url": "https://stresix.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_STRESIX_KEY" }
    }
  }
}
```

## Tools

| Tool | What it does |
| --- | --- |
| `start_load_test` | Builds and load tests a GitHub repo URL or Docker image. Takes the port, up to 10 GET paths, a branch, env vars the app needs to boot and the run size. |
| `get_test_status` | Building, starting servers, or running with live users, requests per second, p95 and error rate. |
| `get_test_report` | The verdict when the run is done: users held, p95, error rate, what ran out first, what to change and the server size to pick. |

## Pricing

| Run | Load | Target server | Cost |
| --- | --- | --- | --- |
| Lite | 500 users, 4 minutes | 2 vCPU / 4 GB | Free once per person |
| Standard | 2,500 users, 10 minutes | 4 vCPU / 8 GB | 1 credit |
| Massive | 10,000 users, 15 minutes | 8 vCPU / 16 GB | 3 credits |

Credits come in packs: $10 for 5, $40 for 25.

## Safety

- A paid run never starts on its own. The tool answers with the price and your balance first, and the agent has to ask you before calling it again with `confirm_spend: true`.
- With no credits left, the tool says to top up at stresix.com and starts nothing.
- Each key can start 4 runs an hour, on top of the account's daily limit, so an agent stuck in a loop cannot spend a balance.
- Load only ever goes to the app Stresix builds, on its own server in a private network. It cannot be pointed at a live site.
- A key can start runs and read their status and reports. It cannot buy credits or change the account. Revoke it in Settings and it stops working at once. Only a hash of each key is stored.

## Example

> **You:** load test this app before I launch, I'm on a 2 vCPU box
>
> **Agent:** reads the code, finds the app listens on 3000 and that `/`, `/api/products` and `/search` hit the database, then calls `start_load_test` with the repo URL
>
> **Stresix:** Started a Lite run (500 users for 4 minutes against 2 vCPU / 4 GB). This is the free run, nothing was charged.
>
> **Agent:** polls `get_test_status`, then calls `get_test_report` and summarizes what ran out first and what to change.

More at [stresix.com/mcp](https://stresix.com/mcp/).
