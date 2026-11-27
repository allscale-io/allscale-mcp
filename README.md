<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/allscale-logo-dark.svg">
    <img src="assets/allscale-logo-light.svg" alt="AllScale" width="320">
  </picture>
</p>

# AllScale MCP Server

AllScale MCP is a remote Model Context Protocol service that connects a compatible AI assistant to your AllScale merchant account. Ask it to check balances, look up transactions, manage invoices and claim links, create checkout links and prepare payments in plain language.

AllScale provides businesses with a global dollar account, with balances held and settled in USDC and USDT. Merchants get paid through invoices, checkout links and Claim Links, and pay vendors and contractors from the same account.

This repository holds the public information for the hosted AllScale MCP server. The server itself is operated by AllScale; there is nothing to install or run locally.

## Connect

| Item | Value |
| --- | --- |
| Server URL | `https://mcp.allscale.io/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth. Sign in to AllScale in your browser; no API key is needed |

You need an AllScale merchant account and an AI client that supports remote MCP and OAuth.

**Claude (web or desktop)**

Open Settings → Connectors → Add custom connector. Name it AllScale, paste the server URL and connect. Sign in to AllScale to authorize access. Custom connector availability depends on your Claude plan and workspace settings.

**Claude Code**

```bash
claude mcp add --transport http -s user allscale https://mcp.allscale.io/mcp
```

Then enter `/mcp`, choose allscale and authorize in your browser. Omit `-s user` to limit the setup to your current project.

**Codex CLI**

```bash
codex mcp add allscale --url https://mcp.allscale.io/mcp
codex mcp login allscale
```

The second command opens the browser for AllScale authorization.

**Cursor and other compatible clients**

Add AllScale as a remote server in your client's MCP configuration:

```json
{
  "mcpServers": {
    "allscale": {
      "url": "https://mcp.allscale.io/mcp"
    }
  }
}
```

Configuration location and prompts vary by client.

**WorkBuddy**

Open Experts · Skills · Connectors → Connectors, click the edit button in the top-right corner, then click **Add MCP** and add:

```json
{
  "mcpServers": {
    "allscale": {
      "type": "streamableHttp",
      "url": "https://mcp.allscale.io/mcp"
    }
  }
}
```

Save, then click **Connect** next to allscale.

**Hermes Agent**

Install the package, enable it, then add the server to your profile's config so Hermes can run the sign-in:

```bash
hermes plugins install allscale-io/allscale-mcp --no-enable
hermes plugins enable allscale
```

```yaml
mcp_servers:
  allscale:
    url: https://mcp.allscale.io/mcp
    auth: oauth
    trust: untrusted
```

```bash
hermes mcp login allscale
```

The package holds only the server URL, with no executable code and no stored credentials. Hermes keeps the sign-in token.

**OpenClaw**

```bash
openclaw mcp set allscale '{"url":"https://mcp.allscale.io/mcp","transport":"streamable-http","auth":"oauth"}'
openclaw mcp login allscale
```

The second command prints a sign-in link. The [AllScale skill](skills/allscale/SKILL.md) on ClawHub adds usage guidance for the agent.

**Authorize**

Your client opens a browser window. Sign in to AllScale, then choose your merchant, an optional checkout store, and the permissions to grant. Start with read-only permissions if you only need balances, transactions and existing records.

To check the connection, ask: "What are my AllScale wallet balances?"

## What you can ask

| You say | What happens |
| --- | --- |
| "What are my AllScale wallet balances?" | Wallet balances by asset and network |
| "Show my last 10 transactions and the largest one in detail." | Recent transactions, with one in full detail |
| "Create a 500 USDC invoice for Alice, due in 30 days." | The invoice is created and sent, and you get its payment link |
| "Make a 50 USD payment link for the workshop." | A one-time checkout link for your store |
| "Prepare this invoice for payment. I'll review it in AllScale." | A pending payment that you approve in AllScale with your passkey |

## Tools

The server provides 15 tools. Each tool operates within the permissions granted to the connection.

**Account, balances and transactions (read)**

| Tool | Returns |
| --- | --- |
| `whoami` | Merchant, store and permission status |
| `wallet_list` | Wallet balances by asset and network |
| `transaction_list` | Transactions, newest first |
| `transaction_get` | One transaction in detail |

**Invoices and claim links (read / write)**

| Tool | Does |
| --- | --- |
| `invoice_list` | Lists issued or received invoices |
| `invoice_get` | Shows one invoice in detail |
| `claim_link_list` | Lists Claim Links and their status |
| `claim_link_get` | Shows one Claim Link in detail |
| `invoice_create_and_send` | Creates a USDT or USDC invoice and emails it to the customer. Requires write permission |
| `invoice_update` | Updates an invoice's memo or extends an overdue invoice's due date. Requires write permission |

**Checkout links (write)**

| Tool | Does |
| --- | --- |
| `checkout_link_create` | Creates a one-time checkout link. Requires a checkout store selected when you authorize |

**Payment preparation (passkey approval)**

| Tool | Prepares |
| --- | --- |
| `payment_prepare` | A USDT or USDC transfer to a wallet address |
| `invoice_pay_prepare` | Payment of an invoice another business sent you |
| `claim_link_prepare` | A funded Claim Link |
| `pending_action_status` | Checks the result of a prepared action |

## Security

- **One merchant per connection.** The assistant can access only the merchant account you select during sign-in.
- **Permissions you choose.** Read-only access cannot change records. Reconnect to grant additional permissions.
- **You approve payments.** Payment tools create a pending action. Funds move only after you approve in AllScale with your passkey.
- **Disconnect at any time.** Review or remove connections in AllScale under Settings → Connected AI apps.
- **Licensed and registered.** AllScale holds a Money Service Operator (MSO) licence in Hong Kong, is registered as a Money Services Business (MSB) with FINTRAC in Canada, and screens transactions with built-in AML checks.
- **Report a security issue.** Email tianying@allscale.io. See [SECURITY.md](SECURITY.md).

## Troubleshooting

**A tool says I don't have permission.**
Ask your assistant to check `whoami` for missing permissions. Reconnect and select the required permissions. You can remove the old connection in Settings → Connected AI apps.

**I can't create a checkout link.**
Your connection needs a checkout store. Reconnect and select one on the authorization page.

**How do I reconnect or revoke access?**
In Claude Code, use `/mcp` to reauthorize. In Codex, run `codex mcp login allscale`. Remove a connection at any time in AllScale under Settings → Connected AI apps.

## Links

- Product page: https://www.allscale.io/mcp
- User guides: https://docs.allscale.io/
- Developer docs: https://developer.allscale.io/
- Terms of Use: https://www.allscale.io/agreement
- Privacy Policy: https://www.allscale.io/policy

## Legal

AllScale is a financial technology developer, not a bank and does not provide digital assets custodian services.

The files in this repository are released under the [MIT License](LICENSE). The AllScale name and logos are trademarks of AllScale and are not covered by this license.
