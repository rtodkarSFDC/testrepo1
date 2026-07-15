# Claude Code Setup & Guidance

This document describes how to use Claude Code with this Quantum Leap engagement.

## MCP Server Status

The `.mcp.json` file wires Claude Code to two MCP servers:

### Salesforce DX Server

**Status**: ✅ Connected after MCP restart  
**Tools**: Metadata retrieve/deploy, Apex execution, SOQL queries, org management  
**Authentication**: Uses `DEFAULT_TARGET_ORG` (your `storm` alias after `sf org login web`)

**Verify connection**:
```bash
claude mcp list
```

Expected output:
```
✅ Salesforce DX    (connected, 50+ tools)
   atlassian        (pending OAuth)
```

### Atlassian Server (Optional)

**Status**: Pending one-time OAuth (only needed if using Jira reporting)  
**When to authenticate**: First time you use `/sync-jira`  
**How**: Claude Code will prompt you to complete Atlassian OAuth in a browser

No API tokens needed — it's purely browser-based login.

## Claude-Salesforce Workflows

### 1. Retrieve Metadata Interactively

Ask Claude to fetch metadata for a component:

> "Retrieve the Account object definition and all its custom fields"

Claude uses the Salesforce DX server to:
- Query org metadata
- Display field definitions, validation rules, sharing rules
- Show Apex triggers, Flows, etc.

### 2. Deploy Safely

> "Deploy the new Opportunity validation rule from force-app to the storm org"

Claude:
- Identifies files to deploy
- Previews changes
- Executes deployment
- Runs tests to verify

### 3. Generate Customizations

> "Create an Apex trigger that prevents duplicate Account names"

Claude:
- Writes the trigger in `force-app/main/default/triggers/`
- Shows the code for review
- Deploys to your org

### 4. Query & Analyze Data

> "Query all Accounts created in the last 30 days and show me the top 10 by opportunity count"

Claude:
- Writes SOQL queries
- Executes via the Salesforce DX server
- Analyzes results and suggests actions

### 5. Test Execution

> "Run all Apex tests in the `force-app` directory and show coverage"

Claude:
- Runs tests against your org
- Reports results and coverage %
- Flags failing tests

## Troubleshooting

### MCP Server Not Connecting

**Problem**: `claude mcp list` shows "⏸ Pending approval" or "✗ Failed"

**Cause 1**: Claude Code hasn't restarted yet after `.mcp.json` was written.  
**Fix**: Restart Claude Code, then run `claude mcp list` again.

**Cause 2**: Node.js version too old.  
**Fix**: Check `node --version` — must be ≥ 22.19. Update if needed.

**Cause 3**: Malformed `.mcp.json`.  
**Fix**: Verify `.mcp.json` is valid JSON and matches the format in `README.md`.

### Salesforce Tools Show "Org Not Authenticated"

**Problem**: Claude tries to run a Salesforce command but gets "no authenticated org"

**Cause**: `sf org login web --alias storm` was never run, or the session expired.  
**Fix**: 
```bash
export PATH=~/.npm-global/bin:$PATH
sf org login web --alias storm
```

Then try the Claude command again.

### Deployment Fails

**Problem**: Claude tries to deploy but gets a permission or metadata error

**Typical causes**:
- Your user lacks Deploy permission in the org
- Metadata has validation errors (missing dependencies)
- Conflicting changes in the org since metadata was retrieved

**Fix**:
1. Ask Claude to show the error details
2. Fix the issue locally or in the org
3. Retry the deployment

## GitHub Workflow

After each change Claude makes:

```bash
# 1. Review the change
git diff

# 2. Stage and commit
git add force-app/
git commit -m "Add Opportunity validation rule for duplicate prevention"

# 3. Push to GitHub
git push origin main
```

Claude can do this automatically if you ask:

> "Deploy the changes and commit them to GitHub"

## Scoping & Planning

Drop Scopezilla outputs into `./scopezilla/` for Claude to reference:

```
scopezilla/
├── epic_summary.md
├── stories/
└── requirements.md
```

Claude will read these to understand scope and propose phased delivery.

## Jira Reporting (Optional)

When Jira integration is enabled, use `/sync-jira` to:

- Upload scope & epic definitions
- Report delivery progress
- Link GitHub commits to Jira tickets
- Sync team status

First time: `/sync-jira` will prompt for Atlassian OAuth (one-time browser login).

## Tips & Best Practices

1. **Always commit before major deploys**: So you have a rollback point
2. **Use descriptive commit messages**: "Add validation rule for duplicate accounts" not "update"
3. **Retrieve before deploying**: Sync metadata to avoid conflicts
4. **Test locally first**: Ask Claude to show code before deployment
5. **Review diffs carefully**: Especially for destructive changes (field deletions, etc.)

## Quick Ref: Key Files

| File | Purpose |
|------|---------|
| `.mcp.json` | MCP server config (Salesforce + Atlassian) |
| `sfdx-project.json` | Salesforce project manifest |
| `force-app/` | Metadata source tree |
| `manifest/package.xml` | Deployment package definition |
| `.gitignore` | Files excluded from Git |
| `package.json` | Dev dependencies & scripts |

## Next Steps

1. ✅ MCP setup complete
2. Run `export PATH=~/.npm-global/bin:$PATH && sf org login web --alias storm` to authenticate
3. Restart Claude Code and verify `claude mcp list` shows ✅ Salesforce DX
4. Fill in engagement details in README.md (Client, Project, GitHub URL)
5. Drop Scopezilla outputs into `./scopezilla/` if available
6. Start asking Claude to retrieve and manage your Salesforce metadata!
