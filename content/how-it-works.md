---
title: "how it works"
date: 2026-05-17
draft: false
---

## the pipeline

the cafeteria sends a weekly email every friday containing links to pdf menus for two campuses (uzumasa and kameoka). this bot picks them up and automates everything else.

```
[University Email (uzumasa + kameoka links)]
      |
      v
[n8n: Gmail Search] --> [Playwright API: Fetch SharePoint PDFs]
      |
      +-------------------+--------------------+
      |                                        |
      v                                        v
[Uzumasa PDF]                          [Kameoka PDF]
      |                                        |
      v                                        v
[Claude API: Extract              [Claude API: Extract
 dish name, nutrition,             dish name, nutrition,
 allergens, prices]                allergens, different layout]
      |                                        |
      +-------------------+--------------------+
                          |
                          v
            [PostgreSQL: menu_items table
              (campus column: uzumasa / kameoka)]
                          |
                          v
            [Daily Discord Poster @ 9am JST]
                          |
          +---------------+---------------+
          |                               |
          v                               v
[Uzumasa subscribers]           [Kameoka subscribers]
 (notify-menu-uzumasa)           (notify-menu-kameoka)
          |                               |
          +---------------+---------------+
                          |
                          v
            [⭐ Rate today's dishes button
               on every menu post]

[separate path — halal upload]
[#halal-menu-upload channel]
      |
      v
[Claude Vision API: extract
 halal dish data from image]
      |
      v
[PostgreSQL: menu_items
 (category: 'Halal')]
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

every saturday, an n8n workflow checks a gmail account for the weekly menu email. when found, it extracts the sharepoint links for both campuses from the email body.

### 2. pdf fetching

because the pdfs are hosted on microsoft sharepoint behind authentication, a playwright-powered express api handles the browser session and returns the raw pdf bytes. there is one scraper workflow per campus — uzumasa and kameoka have different pdf layouts, so each uses its own claude prompt tailored to that menu structure.

### 3. menu extraction with claude

each pdf is sent to the claude api with a campus-specific prompt asking it to extract structured dish data: dish name, category, subcategory, calories, protein, fat, price (in yen), and allergens. the result is a clean json array ready for storage.

### 4. database storage

each dish is stored in a postgresql `menu_items` table keyed by date and campus. the `campus` column (uzumasa or kameoka) differentiates the two menus. the schema is simple and efficient — queries by date and campus take milliseconds.

### 5. daily posting (monday-friday, 9 am jst)

the n8n poster workflow and the discord bot's built-in scheduler run every weekday morning. they query the database for that day's menu per campus, format it with emojis, prices, nutrition info, and aggregate ratings, then send it to every subscribed discord server — grouped by campus so each subscriber only gets the menus they opted into. every menu post includes a **"⭐ rate today's dishes"** button.

### 6. per-server subscriptions

each discord server independently subscribes to a specific campus. the bot manages per-guild campus-specific notification roles (`notify-menu-uzumasa` and `notify-menu-kameoka`) so members can self-opt into pings for one or both campuses.

### 7. halal menu upload (on request)

the halal menu is posted separately as a monthly paper poster. an admin takes a photo, uploads it to the `#halal-menu-upload` channel, and the bot:

1. downloads and compresses the image (max 1800px wide, jpeg)
2. sends it to claude sonnet (`claude-sonnet-4-6`) for vision extraction
3. claude returns structured json with dish names, dates, and day names
4. items are inserted into `menu_items` with category set to `halal`

halal items then appear in the daily menu posts under their own 🟢 **halal** section.

### 8. dish ratings

every menu post includes a **"⭐ rate today's dishes"** button. users can pick a dish from a dropdown and rate it 1–5 stars. ratings are stored in a `dish_ratings` table.

when a dish appears again on a future menu, its aggregate rating is shown inline: **⭐ 4.2 (12)**. this builds a useful reference over time for which dishes are worth getting.

