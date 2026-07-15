# Quantum Leap Engagement

This repository contains the configuration, metadata, and deployment artifacts for the Salesforce Quantum Leap engagement.

## Engagement Details

**Client**: (fill in)  
**Project**: (fill in)  
**Sponsor**: (fill in)  
**Salesforce Org**: storm.c3206e73925a53@salesforce.com  
**GitHub Repository**: (fill in with your GitHub repo URL)

## Project Structure

```
.
├── .mcp.json                    # Claude Code MCP server configuration
├── sfdx-project.json            # Salesforce DX project definition
├── force-app/                   # Salesforce metadata source
├── manifest/                    # Deployment package.xml
├── config/                      # Scratch org definition
├── scopezilla/                  # Engagement scope documents
└── scripts/                     # Sample scripts and queries
```

## Getting Started

### Prerequisites

- **Node.js** ≥ 22.19
- **Claude Code** CLI or IDE extension
- **Git** for version control
- **GitHub** repository access

### Initial Setup

```bash
npm run setup
```

This installs:
- `@salesforce/cli` — Salesforce CLI
- `@earendil-works/pi-coding-agent` — PI coding agent
- `sf-pi` extension — PI skills for Salesforce
- `forcedotcom/sf-skills` — Salesforce agent skills for Claude

### Authorize Your Salesforce Org

```bash
export PATH=~/.npm-global/bin:$PATH
sf org login web --alias storm
```

This creates an authenticated session to your sandbox. The alias `storm` matches your org username.

### Verify MCP Server Connection

After restarting Claude Code, confirm the Salesforce DX MCP server is connected:

```bash
claude mcp list
```

You should see:
- ✅ **Salesforce DX** (connected, showing N tools)
- **atlassian** (pending OAuth for optional Jira sync)

## GitHub Integration

This repository is version-controlled and backed by GitHub. All metadata, configurations, and deployment artifacts are tracked here.

### Workflows

1. **Metadata Tracking**: Retrieve org metadata and commit to this repo
2. **Deployment**: Deploy changes to orgs via `sf project deploy`
3. **Branching**: Create feature branches for isolated work; merge to `main` for production
4. **Audit Trail**: Git history shows who changed what, when, and why

### Setting Up Remote

If not already configured, add your GitHub remote:

```bash
git remote add origin https://github.com/<your-org>/<your-repo>.git
git branch -M main
git push -u origin main
```

## Key Salesforce CLI Commands

### Retrieve Metadata from Org

```bash
export PATH=~/.npm-global/bin:$PATH
sf project retrieve --manifest manifest/package.xml --org storm
git add -A && git commit -m "Retrieve metadata from storm"
git push origin main
```

### Deploy Metadata to Org

```bash
export PATH=~/.npm-global/bin:$PATH
sf project deploy --source-dir force-app --org storm
```

### Query Data

```bash
sf data query --query "SELECT Id, Name FROM Account LIMIT 10" --org storm
```

### Run Apex Tests

```bash
sf apex test run --tests '*_Test' --org storm --code-coverage
```

## Claude Code Features

Once MCP is connected, you can:

- Query and retrieve Salesforce metadata
- Generate Apex and Flow automation
- Deploy changes and run tests
- Analyze data and suggest optimizations

See `CLAUDE.md` for detailed guidance.

## Jira Sync (Optional)

If using Jira for delivery tracking:

1. Complete one-time `/mcp` OAuth in Claude Code (Atlassian login)
2. Use `/sync-jira` to report scope and progress

No credentials needed — it's browser-based OAuth.

## Common Dev Tasks

- **Lint code**: `npm run lint`
- **Format code**: `npm run prettier`
- **Run tests**: `npm run test:unit`
- **Watch tests**: `npm run test:unit:watch`

## Resources

- [Salesforce CLI Docs](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/)
- [Claude Code Documentation](https://github.com/anthropics/claude-code)
- [MCP Salesforce DX Server](https://github.com/salesforcecli/mcp)

