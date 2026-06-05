---
title: "bot commands"
date: 2026-05-17
draft: false
---

all commands are discord slash commands. type `/` in any channel where the bot is present to see them.

every command now shows a **campus picker** (two buttons: uzumasa / kameoka) after you run it — tap your campus first, then the command runs for that campus.

---

## `/subscribe`

**who can use it:** server admins (manage server permission required)

subscribes the current channel to daily lunch menu updates for your chosen campus. the bot will:

1. show a campus picker (uzumasa / kameoka)
2. create a `notify-menu-uzumasa` or `notify-menu-kameoka` role in your server automatically
3. start posting that campus's menu every weekday at **9:00 am jst**
4. tell your members about `/notify` so they can opt into pings for that campus

**usage:** run this in whichever channel you want the menu to appear in. you can subscribe to both campuses — just run `/subscribe` again and pick the other campus (same channel or different, it's up to you).

---

## `/unsubscribe`

**who can use it:** server admins (manage server permission required)

shows a campus picker, then stops daily menu posts for the chosen campus. your server's data is preserved, you can re-subscribe any time with `/subscribe`.

run it again for the other campus if you want to unsubscribe from both.

---

## `/notify`

**who can use it:** everyone

shows a campus picker, then toggles the `notify-menu-uzumasa` or `notify-menu-kameoka` role on or off for yourself. run it once to opt in, you'll get pinged when that campus's menu is posted. run it again to opt out. no admin needed.

if your server is subscribed to both campuses, run `/notify` twice — once for each campus — to control pings independently.

---

## `/preview`

**who can use it:** everyone

shows a campus picker, then displays today's menu for the chosen campus as an ephemeral message (only visible to you). menus include **prices** (in yen), nutritional info (calories, protein, fat), and allergen warnings.

a **"⭐ rate today's dishes"** button appears at the bottom — tap it to leave a 1–5 star rating on any dish.

---

## `/nextmenu`

**who can use it:** everyone

shows a campus picker, then displays the menu for the next available weekday. handy on fridays when you want to plan ahead. includes the same price, nutrition, and allergen info as `/preview`, plus a **"⭐ rate these dishes"** button.

---

## `/status`

**who can use it:** everyone

shows a campus picker, then displays whether your server is subscribed to that campus, which channel receives updates, and when the subscription was set up. if your server is also subscribed to the other campus, it shows that too.

---

## required bot permissions

when you add the bot to your server, it needs:

- **send messages**: to post the daily menu
- **manage roles**: to create and assign the `notify-menu-uzumasa` / `notify-menu-kameoka` roles

these are the minimum permissions. the bot does not read messages or access any other channel data.

---

## dish ratings

every menu post (daily 9 am posts and `/preview` / `/nextmenu` responses) includes a **"⭐ rate today's dishes"** button. tapping it opens an ephemeral flow:

1. pick a dish from the dropdown
2. rate it 1–5 stars

when that dish appears in a future menu, it shows its average rating: **⭐ 4.2 (12)**. ratings are per-user per-dish per-day — you can only rate a dish once per day, but you can change your rating.

---

## halal menu upload

the bot supports halal menu management via claude vision ai. an admin uploads a photo of the monthly halal menu poster to the `#halal-menu-upload` channel, and the bot's claude vision integration (`claude-sonnet-4-6`) extracts the dish names, dates, and day names as structured data. halal items then appear in regular menu posts under a 🟢 **halal** category.
