---
title: "bot commands"
date: 2026-05-17
draft: false
---

all commands are discord slash commands. type `/` in any channel where the bot is present to see them.

---

## `/subscribe`

**who can use it:** server admins (manage server permission required)

subscribes the current channel to daily lunch menu updates. the bot will:

1. Create a `notify-menu` role in your server automatically
2. start posting the menu every weekday at **9:00 am jst**
3. tell your members about `/notify` so they can opt into pings

**usage:** run this in whichever channel you want the menu to appear in.

---

## `/unsubscribe`

**who can use it:** server admins (manage server permission required)

stops daily menu posts to your server. your server's data is preserved, you can re-subscribe any time with `/subscribe`.

---

## `/notify`

**who can use it:** everyone

toggles the `notify-menu` role on or off for yourself. run it once to opt in, you'll get pinged when the daily menu is posted. run it again to opt out. no admin needed.

---

## `/preview`

**who can use it:** everyone

shows today's menu as an ephemeral message (only visible to you). useful for a quick check without waiting for the 9 am post.

---

## `/nextmenu`

**who can use it:** everyone

shows the menu for the next available weekday. handy on fridays when you want to plan ahead.

---

## `/status`

**who can use it:** everyone

shows whether your server is subscribed, which channel receives updates, and when the subscription was set up.

---

## required bot permissions

when you add the bot to your server, it needs:

- **send messages**: to post the daily menu
- **manage roles**: to create and assign the `notify-menu` role

these are the minimum permissions. the bot does not read messages or access any other channel data.
