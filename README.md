# Language Machines Network website

Built with [Hugo](https://gohugo.io) and the [Congo](https://jpanther.github.io/congo/) theme, deployed automatically to GitHub Pages.

## Site structure

- `content/en/_index.md` — homepage text
- `content/en/news/` — news & announcements; each file here shows up on the homepage. Add a new post with `hugo new content en/news/my-post.md`
- `content/en/calendar/_index.md` — reading group schedule (upcoming + archive)
- `content/en/cite-club/_index.md` — intro text for the Cite Club page. The publication/reading list itself is pulled live from Zotero at build time (see below) — you don't edit it as a file.
- `content/en/links/_index.md` — the link list
- `content/en/who-we-are/_index.md` — organizer bios and photos

All of these are plain Markdown files — open them in any text editor (or directly on github.com) and edit the text between the `---` front matter and the rest of the page.

## Cite Club / Zotero

The Cite Club page fetches your public Zotero group library (ID `6112114`) at build time and splits items into two lists:

- **Our Publications** — any item tagged `network-publication` in Zotero
- **Reading List** — everything else in the library

To move an item between the two lists, just add or remove that tag on the item in Zotero. No need to touch the website code — the next build will pick it up automatically (builds run on every push, and once a day on a schedule, see below).

If you'd rather use a different tag name, change `publicationTag` in `config/_default/params.toml`.

## Adding people (Who We Are)

Each organizer is a `person` block in `content/en/who-we-are/_index.md`, e.g.:

```
{{< person name="Anna Weichselbraun" role="Role/title here." photo="Anna.png" >}}
Bio text goes here, and can use normal Markdown.
{{< /person >}}
```

To add someone new, copy one whole block (from the opening `{{< person ... >}}` tag to the closing `{{< /person >}}` tag) and fill in their details. `photo` can be a filename of an image placed directly in `content/en/who-we-are/` (as in the example above), or a full image URL.

## Previewing changes locally

Before pushing to GitHub, you can preview changes in a browser on your own computer:

1. Install Hugo (extended version, v0.166.0 or later) — on a Mac, the easiest way is `brew install hugo`.
2. In this folder, run:
   ```
   hugo server
   ```
3. Open the URL it prints (usually `http://localhost:1313`). The preview updates live as you edit files.

This preview is a reliable stand-in for what will actually deploy: the site's CSS is a pre-built stylesheet (see note below), not something regenerated at build time, so `hugo server` renders pages using the exact same styling GitHub Actions uses — no surprises between what you see locally and what goes live.

**Note for anyone editing the theme's templates/layouts:** this site's GitHub Actions workflow only runs `hugo --minify` — it does not run a Tailwind CSS build step. That means the compiled stylesheet (`themes/congo/assets/css/compiled/main.css`) is fixed at whatever the theme shipped with; any *brand-new* Tailwind utility-class combination added to a custom layout or shortcode (one not already used elsewhere in the theme) will silently have no effect, both locally and once deployed. When custom layout work needs new styling, write plain CSS in `assets/css/custom.css` instead (see the `.person` rules there for an example) rather than inventing new Tailwind classes.

## Deployment

Every push to `main` triggers `.github/workflows/deploy.yml`, which builds the site and publishes it to GitHub Pages. It also rebuilds once a day on its own so Cite Club stays in sync with Zotero even if nobody edits the repo. You can also trigger a rebuild manually from the repo's Actions tab ("Run workflow").

## Connecting your custom domain

Once you've registered your domain:

1. Edit `static/CNAME` and replace `example.com` with your actual domain, then commit and push.
2. Edit `baseURL` in `config/_default/hugo.toml` to match (e.g. `https://yourdomain.com/`).
3. At your domain registrar, add these DNS records (exact steps vary by registrar):
   - If using an apex domain (`yourdomain.com`): four `A` records pointing to GitHub Pages' IPs — `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - If using a subdomain (`www.yourdomain.com`): a `CNAME` record pointing to `<your-github-username>.github.io`
4. In the repo's Settings → Pages, enter your custom domain and enable "Enforce HTTPS" once it's available.

GitHub's docs have more detail: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site
