---
title: "how it works"
date: 2026-05-17
draft: false
---

## the pipeline

the cafeteria sends a weekly email every friday containing a link to a pdf menu. this bot picks that up and automates everything else.

```
[University Email]
      |
      v
[n8n: Gmail Search] --> [Playwright API: Fetch SharePoint PDF]
      |
      v
[Claude API: Extract menu as JSON]
      |
      v
[PostgreSQL: Store menu items]
      |
      v
[n8n: Daily Discord Poster @ 9am JST]
```

## tech stack

| Component | Technology |
|---|---|
| Workflow automation | n8n |
| PDF fetching | Playwright + Express (Node.js) |
| AI extraction | Claude (Anthropic API) |
| Database | PostgreSQL |
| Bot framework | discord.js |
| Infrastructure | Proxmox (self-hosted) |

## step by step

### 1. email detection (saturday)

every saturday, an n8n workflow checks a gmail account for the weekly menu email. when found, it extracts the sharepoint link from the email body.

### 2. pdf fetching

because the pdf is hosted on microsoft sharepoint behind authentication, a playwright-powered express api handles the browser session and returns the raw pdf bytes.

### 3. menu extraction with claude

the pdf is sent to the claude api with a prompt asking it to extract structured dish data: dish name, category, subcategory, calories, protein, fat, and allergens. the result is a clean json array ready for storage.

### 4. database storage

each dish is stored in a postgresql `menu_items` table keyed by date. the schema is simple and efficient, queries by date take milliseconds.

### 5. daily posting (monday-friday, 9 am jst)

a second n8n workflow (and the discord bot's built-in scheduler) runs every weekday morning. it queries the database for that day's menu, formats it with emojis and nutrition info, and sends it to every subscribed discord server.

### 6. per-server subscriptions

each discord server independently subscribes to a specific channel. the bot manages per-guild notification roles so members can self-opt into pings.

