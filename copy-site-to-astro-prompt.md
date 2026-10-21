# Copy a website to flat files in Astro

A prompt for an AI coding agent. It turns a live website into flat HTML files that Astro serves locally and builds to `dist/`.

Works with any agent that can read files, write files and run shell commands (`curl`, `node`, `npm`). For example Cursor, Claude Code, OpenAI Codex, GitHub Copilot agent mode, Gemini CLI, Windsurf, Cline or Aider.

## How to use it

Open this repo in your agent tool. Start a new chat or session. Type only this, with your URL:

```text
Read copy-site-to-astro-prompt.md and copy my website https://example.com
```

The first chat asks questions and writes a task file. After that, run one job per chat. The agent prints the exact prompt for the next job when it finishes each job.

---

## For the agent

You will copy a live website into flat HTML files that Astro serves and builds. The copy must look and behave like the original. This is not a redesign.

Do not make git commits. Do not run browser tests unless the user asks for it.

All paths below are relative to the root of this repo (the folder that contains `copy-site-to-astro-prompt.md`).

If the user gives you a URL and there is no `sites/<folder>/TASKS.md` for it yet, start with phase 0.

### The user is probably not technical

- Write to the user in plain words. Explain technical terms the first time you use them.
- When the user must do something, give numbered steps. One action per step.
- When the user must run a command, give it in a copyable code block and say where to run it (which folder, which app).
- Never assume the user knows git, the terminal, FTP or their hosting control panel. Ask what they have access to.

### Asking the user

When you need answers from the user:

- If your tool has a structured question feature (for example a multiple-choice question tool), use it.
- Otherwise, write the questions as a numbered list with lettered options and a suggested default for each. Then stop and wait for the answers. Do not guess.

### Phase 0. Investigate and ask

Do this in the first chat. Do not build anything yet.

1. Fetch the front page, `robots.txt` and the sitemap with `curl`. Try `sitemap.xml`, `sitemap_index.xml` and `wp-sitemap.xml`.
2. Find out what the site runs on. Look for `generator`, `wp-content`, Wix, Squarespace, Webflow, Shopify, Joomla, Drupal or static HTML. Note the theme, SEO plugin and `server` header.
3. Count the pages in the sitemap. Split them into pages, blog posts, archives and other.
4. Note analytics, tracking pixels, cookie banners, forms, search fields, login, web shop, comments and calls to `admin-ajax.php`, `wp-json` or other APIs.
5. Ask the user (see "Asking the user"). Only ask about what you cannot decide yourself. Typical questions:
   - Folder name under `sites/` (suggestion: the domain with hyphens, for example `example-com`)
   - Which pages to include (everything in the sitemap, pages only, pages plus blog, a hand-picked list)
   - Category, tag and author archives (never, only when visibly linked, all)
   - Forms (leave them dead, point them to an external service, remove them)
   - Analytics, tracking pixels and cookie banner (keep unchanged, remove)
   - Search field (leave it, remove it)
   - Web shop or login, if found (stop and agree on scope, copy only the visible pages)
   - Should share URLs (canonical, Open Graph) keep pointing to the production domain (default is yes)
   - Hosting afterwards (same web host as today, Netlify, Cloudflare Pages, other, I don't know). If the same host: is it Apache or LiteSpeed (supports `.htaccess`), Nginx, or unknown. Use the `server` header as the suggested answer.
   - How the user reaches the files on the web host (control panel file manager such as cPanel or Plesk, FTP/SFTP program, I don't know)
6. Write `sites/<folder>/TASKS.md` from the template below. Fill in the facts, the URL list and the answers. Remove jobs that make no sense and add jobs if the site needs them (for example a form replacement).
7. Reply briefly with the path to the file and the prompt for job 1 in a copyable code block (see "Next-step prompt"). Do not start job 1.

If the site is large (more than about 60 pages), split job 2 and job 3 into several jobs by section (for example job 2a, 2b, 2c), so each chat can finish its job.

### Rules for all jobs

- One job per chat. If an earlier job is missing, stop and say which one.
- Update the status and log in `TASKS.md` when the job is done. Log with the date and concrete numbers.
- Do not change content, classes, markup or text. Copy what the server sends.
- No image optimization and no conversion to other formats.
- No new Astro components for layout, menu or content.
- If reality differs from the task file, write it in the log and continue only if it does not change the scope. Otherwise stop and ask.

### Next-step prompt

When a job is done and the log is updated, end your reply with the prompt for the next job that still has status "Not started", in a copyable code block. Use exactly this form, with the real folder and job number:

```text
Read sites/<folder>/TASKS.md and do only job <N>. Update the status and log in the file when the job is done. Do not start the next job.
```

Add one line above the block: "Start a new chat and paste this:".

If the job failed or stopped, do not print a next-step prompt. Say what blocks it instead.

When the last job is done, print no prompt. Say that all jobs are done, and remind the user to check the live site in a browser and to keep the backup.

---

## Template for TASKS.md

Copy everything inside the code block below to `sites/<folder>/TASKS.md` and fill in the fields in `<…>`.

````markdown
# <domain> as flat files

Temporary task list. Delete this file when the site is frozen and checked.

Copy the site as it looks on <domain>. Do not redesign anything.

## How to start a job

New chat. Type only this, with the job number:

    Read sites/<folder>/TASKS.md and do only job N. Update the status and log in the file when the job is done. Do not start the next job.

One job per chat. If an earlier job is missing, stop and say which one.

Do not make git commits.

When a job is done, end the reply with the prompt for the next job that still has status "Not started", in a copyable code block, preceded by the line "Start a new chat and paste this:". If the job failed or stopped, print no prompt and say what blocks it. After the last job, print no prompt. Remind the user to check the live site in a browser and to keep the backup.

The user is probably not technical. Use plain words, numbered steps and copyable commands, and say where to run each command.

## Fixed decision

<CMS/theme> delivers the finished page. Astro must not build the pages as components.

- HTML, CSS, JS, fonts and images are copied as the server sends them.
- Astro is only a local server and build. Files in `site/public/` are copied unchanged to `dist/`.
- Same paths. `https://<domain>/about/` becomes `public/about/index.html`.
- Only the files the pages actually use.
- Title, description, canonical, Open Graph and Twitter cards stay as they are.
- No new code for menu, layout or content.
- No image optimization and no conversion to other formats.

Known facts from <date>. Check the live site again if something does not match, and write the difference in the log.

- <CMS, theme, child theme, SEO plugin>
- <server>. Sitemap: `<sitemap-url>`.
- <analytics, pixel, cookie script>
- <forms, search field, ajax calls>
- <last content change in the sitemap>

Decisions from phase 0:

- Archives: <answer>
- Forms: <answer>
- Analytics and cookie banner: <answer>
- Search field: <answer>
- Hosting: <answer>
- Access to the web host files: <answer>

## URLs to include

Pages:

- <url>

Blog:

- <url>

Archives are fetched only according to the decision above. Add the URL here before fetching it.

Not fetched:

- <admin, login, API, feeds, templates, author archive and so on>

## Job 1. Astro skeleton

Status: Not started

Create `sites/<folder>/site/`.

- `package.json` with `dev`, `build` and `preview` scripts. Use the current stable major version of Astro.
- `astro.config.mjs` with `site: 'https://<domain>'`, `output: 'static'`, `trailingSlash: 'always'`, `build.format: 'directory'`.
- No sitemap integration. The pages live in `public/`, so the sitemap is written in job 5.
- No files in `src/pages/`. An `index.astro` would overwrite `public/index.html`.
- An empty `public/` with a `.gitkeep`.
- `.gitignore` with `node_modules`, `dist` and `.astro`.
- `npm install` in `site/`.

Done when `npm run build` writes a `dist/` without errors. A warning about a missing `src/pages` is expected.

Log:

## Job 2. Save HTML

Status: Not started

Requires job 1.

Fetch every URL under "URLs to include". Save the response as `site/public/<path>/index.html`. The front page is `site/public/index.html`.

- Save the body as it comes. Do not change content, classes or SEO tags.
- Absolute URLs to <domain> may stay for now. Job 3 makes the files local.
- Follow links in the saved HTML. If a real subpage is missing from the list, add it and fetch it.
- Write in the log whether pages have analytics, pixels, cookie scripts, forms, search fields or ajax calls.

Done when every URL on the list exists as `index.html`, and the title and canonical are still there.

Log:

## Job 3. CSS, JS, fonts and images

Status: Not started

Requires job 2.

Go through all saved HTML files and the CSS and JS files they load. Fetch what the pages show and run.

Include:

- stylesheets, scripts, fonts, favicon
- `img`, `srcset`, `source`, `poster`
- `url()` and `@import` in CSS
- lazy-load attributes such as `data-src`, `data-srcset`, `data-bg` and `data-bg-url`, when they point to a file
- Open Graph images
- external fonts (for example Google Fonts), saved locally next to the stylesheet that uses them

How to save a file:

- `https://<domain>/path/file.png?ver=1` becomes `site/public/path/file.png`
- Query strings are removed from file names and from references
- HTML entities such as `&#038;` are read as `&` before download

In HTML and CSS that control the look and load files, `https://<domain>/...` must become a root-relative path (`/path/...`), so the local server does not load from the live site.

These must keep the absolute production URL:

- canonical
- `og:url`
- `og:image` and other share URLs
- SEO schema (JSON-LD)

Done when a search in `site/public` finds no stylesheet, script or image paths that still point to <domain>, except share tags and schema. Write the number of files per type and any leftovers in the log.

Log:

## Job 4. Remove WordPress/CMS leftovers (login, API and feed links)

Status: Not started

Requires job 3.

In the saved HTML files, remove only tags that point to the CMS backend. Do not touch the visible layout.

Remove links and tags for:

- <for WordPress: `wp-json`, oEmbed, `xmlrpc.php`, EditURI, RSS and comment feeds, `generator`, `rel="https://api.w.org/"`>

Keep SEO tags, canonical, description, Open Graph, Twitter cards, favicon and font preloads.

Do not delete CSS, JS or images the pages use.

Handle forms, search field, analytics and cookie banner according to the decisions at the top. If the decision is "leave it", only write the findings in the log.

Done when a search in the HTML finds none of the removed endpoints.

Log:

## Job 5. Sitemap, robots.txt and web server settings

Status: Not started

Requires job 4.

Put these files in `site/public/`:

- `robots.txt` with `User-agent: *`, an empty `Disallow:` and `Sitemap: https://<domain>/sitemap.xml`
- `sitemap.xml` with only the URLs that exist as `index.html`. Same addresses as on the live site.
- A hosting file according to the decision at the top. Apache: `.htaccess` with `DirectoryIndex index.html` and denial of `*.php` and CMS login. Netlify or Cloudflare Pages: `_redirects` or `_headers`, if needed.

Run `npm run build` and check that the files are in `dist/`.

Done when the build is green and the sitemap only lists pages that exist locally.

Log:

## Job 6. Check against the live site

Status: Not started

Requires job 5.

No browser. Use curl and search the files. The user checks in the browser afterwards.

Check against `npm run preview` (the built `dist/`). The Astro dev server can answer 404 for folders with `index.html` in `public/`, so do not use it as the reference.

For each URL on the list:

- the local `index.html` exists
- title, description and canonical are the same as on <domain> (ignore CRLF vs LF)
- CSS, JS, fonts and images respond locally with status 200
- share tags still point to `https://<domain>/...`
- external files that are already dead on the live site are noted as a difference and not fixed

Fix only broken local file paths.

Done when the log has one line per URL with ok or a difference.

Then guide the user to test the copy on their own computer:

1. Open a terminal in `sites/<folder>/site/` (explain how in their agent tool).
2. Run `npm run build && npm run preview` (copyable block).
3. Open the address the command prints (usually `http://localhost:4321/`) in a browser.
4. Click through the menu and the most important pages. Compare them side by side with the live site.
5. Stop the preview with `Ctrl+C` in the terminal.

Ask the user to report anything that looks wrong before job 7.

Log:

## Job 7. Backup and go live

Status: Not started

Requires job 6.

You cannot reach the web host. The user does the steps. Guide them one part at a time and wait for "done" before the next part. Adapt the steps to the hosting and access answers at the top. Use plain words.

### Part 1. Confirm the local test

Ask if the user tested the copy with `npm run preview` as described in job 6, and if everything looked right. If not, stop and help with that first.

### Part 2. Backup (required)

Do not continue until the user confirms they have a working backup stored on their own computer, not only on the web host.

Explain that a backup must contain both the files and the database (WordPress and most CMSs store the content in a database). Offer the options that fit their access:

- The backup feature in the hosting control panel (for example cPanel "Backup" or Plesk "Backup Manager"). Download the backup files.
- A backup plugin in WordPress (for example UpdraftPlus). Download the backup files.
- By hand: download all website files with the file manager or an FTP program, and export the database with phpMyAdmin.

Ask the user to check that the downloaded files open and are not empty. Suggest also asking the web host whether they keep automatic backups.

### Part 3. Move the old site into a protected folder (WordPress or another PHP CMS on the same host)

Skip this part if the site moves to a new host (for example Netlify or Cloudflare Pages).

The old files stay on the server as an extra backup, but nobody can open them from the web. The database is not touched.

Only do this on Apache or LiteSpeed, where `.htaccess` works. On Nginx or an unknown server, tell the user to ask the web host to move the old files outside the public web folder instead, and skip to part 4 when that is done.

Generate a folder name with a random part, for example `old-site-k7f2q9xm`. Then give the steps:

1. Open the public web folder in the file manager or FTP program (often `public_html`, `www`, `httpdocs` or `htdocs`). Turn on "show hidden files", so files starting with a dot are visible.
2. Rename the existing `.htaccess` to `wordpress.htaccess.txt`.
3. Create the folder `old-site-<random>`.
4. Move all files and folders of the old site into that folder. For WordPress that is `wp-admin`, `wp-content`, `wp-includes`, every `wp-*.php` file, `index.php`, `xmlrpc.php`, `license.txt`, `readme.html` and `wordpress.htaccess.txt`.
5. Do not move `.well-known`, `cgi-bin` or folders that belong to other sites or subdomains. If unsure about a file or folder, leave it and ask.
6. In `old-site-<random>`, create a file named `.htaccess` with this content (copyable block):

   ```apache
   <IfModule mod_authz_core.c>
       Require all denied
   </IfModule>
   <IfModule !mod_authz_core.c>
       Order allow,deny
       Deny from all
   </IfModule>
   ```

7. Check the protection: open `https://<domain>/old-site-<random>/wp-config.php` in a browser. It must show "403 Forbidden" or a similar "access denied" page. Also run `curl -I` on that URL yourself and confirm status 403.

If the check does not give 403, stop. Tell the user to ask the web host for help, or to move the folder out of the public web folder. Do not continue while the old files are reachable.

### Part 4. Upload the new files

1. Upload the contents of `sites/<folder>/site/dist/` to the public web folder. The files inside `dist/`, not the `dist` folder itself. Include hidden files such as `.htaccess`.
2. Netlify or Cloudflare Pages: upload the `dist` folder with their drag-and-drop upload instead. Connecting the domain (DNS) happens outside this job. Tell the user to follow the host's guide or ask the host.
3. If the host or Cloudflare has a cache, tell the user to clear it.

### Part 5. Check the live site

1. Run `curl -I` yourself on every URL in the list and on `robots.txt` and `sitemap.xml`. All must give status 200.
2. Fetch the front page and check that it is the new flat file (for example, no WordPress `generator` tag) and that its CSS, JS and images give status 200.
3. Ask the user to open the site in a private browser window and click through the menu and the most important pages.

### If something goes wrong

Give the user these steps to roll back:

1. Delete the uploaded new files from the public web folder (not `.well-known` or `old-site-<random>`).
2. Move everything from `old-site-<random>` back to the public web folder.
3. Delete the protective `.htaccess` and rename `wordpress.htaccess.txt` back to `.htaccess`.
4. If that does not work, restore the backup from part 2 or ask the web host.

Done when the live site answers 200 on every URL, the old files are protected or moved, and the user has confirmed the backup. Write the folder name `old-site-<random>` in the log so the user can find it later.

Log:

## Outside these jobs

DNS changes, deleting the old files and deleting the database do not happen here. Keep the backup and the protected folder until you are sure the new site works.
````
