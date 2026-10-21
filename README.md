# copy-site-to-astro

A prompt that makes an AI coding agent copy a live website into flat HTML files served by Astro. Same look and behavior. Not a redesign.

The whole tool is one file: [`copy-site-to-astro-prompt.md`](copy-site-to-astro-prompt.md).

## Why use it

- **Faster site.** Plain files load quickly. No database, no PHP.
- **Nothing to maintain.** No WordPress updates, no plugin updates, no security patches.
- **Safer.** There is no login page or admin panel to hack.
- **Cheaper hosting.** Plain files can run on almost any web host, often for free (for example Netlify or Cloudflare Pages).

## What you get

A folder with your website as plain HTML files, plus the CSS, JavaScript, fonts and images it needs. It looks and works like your current site, but there is no WordPress (or other CMS) behind it.

[Astro](https://astro.build) is a free tool that serves these files on your own computer while you test, and builds the final folder you upload to your web host.

## What stops working

Anything that needs a server or a database stops working in the copy. For example:

- Contact forms
- Search
- Login and member areas
- Web shops and payments
- Comments

The agent tells you what it finds. You can then ask your own agent to build a replacement, for example a contact form that uses a form service. The copy is ordinary code, so you or your agent can keep building on it.

If you want to edit content after the copy, you edit the files (or ask your agent to). There is no admin panel.

## Time and cost

It depends on the site and your agent. A normal WordPress site takes about 30 minutes. Large sites take longer and use more tokens.

## No support. Use at your own risk.

- I do not offer support. Issues and questions may go unanswered.
- You use this prompt at your own responsibility.
- AI agents make mistakes. You must check the output yourself before you publish or deploy anything.
- Only copy websites you own or have the rights to copy.

The software is provided "as is", without warranty of any kind. See [LICENSE](LICENSE).

## What I have tested

I have only tested this prompt in Cursor. I have used it to copy a number of WordPress sites built with the Avada theme, and it has worked well for those. Other agent tools, CMSs and themes should work in principle, but I have not tried them.

## Requirements

- An AI coding agent. That is an AI tool that can read and write files and run commands on your computer. If you have none, use [Cursor](https://cursor.com). Others also work: Claude Code, OpenAI Codex, GitHub Copilot agent mode, Gemini CLI, Windsurf, Cline or Aider.
- [Node.js](https://nodejs.org) (the LTS version). It includes npm. Your agent can help you install it.
- `curl`. It is already installed on macOS, Linux and Windows 10/11.

## The easy way

Open an empty folder in your agent tool, start a new chat and write:

```text
Clone https://github.com/plauge/copy-site-to-astro, read copy-site-to-astro-prompt.md and copy my website https://example.com
```

Replace `https://example.com` with your own website. The agent does the rest and tells you what to do next.

## How to use it (step by step)

1. Clone this repo and open it in your agent tool.

   ```bash
   git clone https://github.com/plauge/copy-site-to-astro.git && cd copy-site-to-astro
   ```

   No git? Click **Code → Download ZIP** on GitHub, unzip it and open the folder in your agent tool.

2. Start a new chat or session and type only this, with your URL:

   ```text
   Read copy-site-to-astro-prompt.md and copy my website https://example.com
   ```

3. The agent investigates the site, asks you a few questions and writes a task file to `sites/<folder>/TASKS.md`. It then prints the prompt for job 1.
4. Start a new chat for each job and paste the prompt the agent printed. After each job, the agent prints the prompt for the next one.
5. There are 7 jobs:
   1. Set up the Astro project
   2. Save the pages
   3. Save CSS, JS, fonts and images
   4. Remove WordPress/CMS leftovers (login, API and feed links)
   5. Sitemap, robots.txt and web server settings
   6. Compare with the live site, and test the copy on your own computer
   7. Backup and go live

   Large sites may have job 2 and job 3 split into smaller jobs.

## Before you go live: take a backup

**Always take a full backup of your current website (files and database) and store it on your own computer before you replace anything.** Job 7 will not continue until you confirm you have one.

In job 7, the agent guides you step by step:

1. Confirm that the local test looked right.
2. Take a backup of files and database.
3. For WordPress on the same web host: move the old WordPress files into a folder protected by an `.htaccess` file. The old site stays on the server as an extra backup, but nobody can open it from the web.
4. Upload the new flat files from `sites/<folder>/site/dist/`.
5. Check the live site.

If something goes wrong, the agent gives you steps to roll back.

## Why one job per chat?

The copy is split into several jobs instead of one big prompt. This keeps the number of tokens in each chat as low as possible.

An AI agent gets worse as its context window fills up. It forgets earlier instructions, skips steps and makes more mistakes. A new chat for each job means the agent starts every job with a clean, small context and stays sharp.

The task file keeps status and a log, so a new chat always knows where to continue.

## License

[MIT](LICENSE)
