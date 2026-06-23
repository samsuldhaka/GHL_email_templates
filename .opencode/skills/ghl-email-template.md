# GHL Email Template Skill

Pushes HTML email templates to the GitHub repo and tracks them in `AGENTS.md` with their raw GitHub URLs. The raw URLs can be used with GHL MCP server tools (e.g., `ghl_emails_create-template` with `importURL`).

## When to use

- User asks to create, add, or save an email template
- User asks to push a file to the GHL email templates repo
- User asks to register or index a template for GHL use
- User provides an HTML file path and wants it tracked in AGENTS.md

## Pre-requisites

- The file must exist in the workspace directory (`E:\GHL-automation`)
- Git remote `origin` must point to `https://github.com/samsuldhaka/GHL_email_templates.git`
- The `AGENTS.md` file must exist at the repo root with a valid JSON structure

## Workflow

### Step 1 — Accept input from user
The user will specify:
- The file path (e.g., `st-patricks-email.html`) — required
- Optionally, a description for the template

### Step 2 — Push the file to GitHub
Stage, commit, and push the specified file to the `main` branch:
```bash
git add <filename>
git commit -m "Add <filename> email template"
git push origin main
```

### Step 3 — Update AGENTS.md
Read `AGENTS.md`, parse the JSON, and append a new entry:

```json
{
  "name": "<filename>",
  "url": "https://raw.githubusercontent.com/samsuldhaka/GHL_email_templates/refs/heads/main/<filename>"
}
```

Add it to the `files` array, then write the updated JSON back to `AGENTS.md`.

### Step 4 — Push AGENTS.md
Stage, commit, and push the updated index:
```bash
git add AGENTS.md
git commit -m "Update AGENTS.md with <filename>"
git push origin main
```

### Step 5 — Confirm
Tell the user the template is pushed and registered, including the raw URL so they can use it with GHL tools.

## AGENTS.md structure

The file is pure JSON (not wrapped in markdown fences):

```json
{
  "base_url": "https://raw.githubusercontent.com/samsuldhaka/GHL_email_templates/refs/heads/main/",
  "files": [
    {
      "name": "canada-day-email.html",
      "url": "https://raw.githubusercontent.com/samsuldhaka/GHL_email_templates/refs/heads/main/canada-day-email.html"
    }
  ]
}
```

## Notes

- Always use `git mv` if renaming files to preserve history
- The base URL follows the pattern: `https://raw.githubusercontent.com/{owner}/{repo}/refs/heads/{branch}/`
- Only push files that are in the workspace directory
