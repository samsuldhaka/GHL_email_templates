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

### Step 5 — Create GHL email template (optional)
If the user also wants the template created in GHL, use `ghl_emails_create-template`:

- `type`: `"html"`
- `name`: A descriptive name (e.g., `"Canada Day Email"`)
- `title`: Same as name
- `builderVersion`: `"2"`
- `templateDataUrl`: The raw GitHub URL from AGENTS.md

This loads the HTML from the raw URL into a GHL email template. The template can then be used in campaigns and automations.

### Step 6 — Confirm
Tell the user the template is pushed, tracked in AGENTS.md, and (if requested) created in GHL. Provide the raw URL so they can reference it elsewhere.

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
- For GHL template creation, use `templateDataUrl` (not `importURL`). The `importURL` field only works with Mailchimp/ActiveCampaign URLs when `type: "import"`.
- After creating a GHL template, the raw URL remains useful for blog posts (`ghl_blogs_create-blog-post` with `rawHTML`), email sending (`ghl_conversations_send-a-new-message` with `html`), or other GHL tools.
