Files behind Luka Kirigins Blog [lukakirigin.com](http://lukakirigin.com). 

Format and infrastructure is based on Gregory Gundersen's Blog: For details, see [this post](http://gregorygundersen.com/blog/2020/06/21/blog-theme).

## Local development and production builds

Use Ruby 3.3.12 (see `.ruby-version`) and Bundler 2.6.9 (see
`Gemfile.lock`). Install and build with:

```sh
bundle install
JEKYLL_ENV=production bundle exec jekyll build
```

For a local preview, run `bundle exec jekyll serve`. The source is the repository
root and the default generated output is `_site/`. To validate without changing
the existing generated files, use:

```sh
JEKYLL_ENV=production bundle exec jekyll build --destination /tmp/math-blog-pages-validation --disable-disk-cache
```

The locked dependencies include Jekyll 4.4.1, `jekyll-scholar` 7.3.0,
`jekyll-feed` 0.18.0, `jekyll-sitemap` 1.4.0, and WEBrick for local serving.
Kramdown/GFM, Rouge, and the bibliography processing dependencies are installed
transitively. MathJax is loaded by the existing browser-side include; it needs
no extra build dependency. No Node build is required.

## GitHub Pages deployment

`.github/workflows/pages.yml` builds on pushes to `master` and supports manual
runs on `master`. It installs the locked gems through `ruby/setup-ruby`'s
Bundler cache, runs Jekyll with `JEKYLL_ENV=production`, uploads `_site/`, and
deploys it with the official Pages actions. Build jobs can read Pages metadata;
only the deploy job receives `pages: write` and `id-token: write`. Deployments
run one at a time. No personal access token or deployment secret is needed.

Complete the migration after reviewing these changes locally:

1. Open the repository's [Settings → Pages](https://github.com/lukadamiank-oss/Math-Blog/settings/pages).
2. Under **Build and deployment → Source**, switch **Deploy from a branch** to
   **GitHub Actions**. Use the workflow in this repository; do not add a second
   default Jekyll workflow.
3. Under **Custom domain**, keep or enter `lukakirigin.com` and save if needed.
   Do not change DNS as part of this migration.
4. Commit the reviewed source changes and push to `master`. Open **Actions →
   Build and deploy Jekyll to GitHub Pages** and check that both `build` and
   `deploy` succeed. Once the workflow is on the default branch, you can also
   choose **Run workflow → master** to retry it manually.
5. If deployment is blocked by an environment rule, open **Settings →
   Environments → github-pages** and ensure `master` is permitted to deploy.
6. In **Settings → Pages**, enable **Enforce HTTPS** when GitHub has issued the
   certificate and the option becomes available. Verify `https://lukakirigin.com`.

The root `CNAME` already contains `lukakirigin.com` and is copied to the build
artifact. For Actions-based deployments, GitHub uses the custom domain saved
in Pages settings, not the artifact's `CNAME`. `_config.yml` therefore also
sets `url: https://lukakirigin.com` and `baseurl: ""`; URLs must not have a
`/Math-Blog` prefix. The existing post permalink pattern is unchanged.

See GitHub's [custom workflow documentation](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)
and [custom domain documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site).

### Bibliography

`jekyll-scholar` is required because `_layouts/default.html` renders
`{% bibliography --cited %}` for every post. GitHub's restricted branch-based
builder does not load this plugin, which causes the unknown-tag error even
when the error is attributed to a post.

Deleting `_bibliography/references.bib` caused a separate missing-file error
in a normal Bundler build. The current posts contain no `{% cite %}` tags or
other keyed Scholar references. The deleted file in Git history contained
only the template's `bishop2006pattern` sample, so that entry remains deleted.
An empty, comment-only database now satisfies the layout's bibliography tag.
Add real BibTeX entries there when adding citations; do not delete the database
while the layout uses it. No current citation keys are unresolved.

### Validation and existing generated files

Local validation on macOS with Ruby 3.3.12 and Bundler 2.6.9 passed:

- `bundle install` and the production Jekyll build.
- Homepage, all four post pages, CSS, 13 images, and two MP4 videos are present.
- All 18 unique local asset URLs resolve in the generated output.
- `CNAME` is preserved; feed and sitemap XML parse and use the HTTPS site URL.
- Citation and cited-bibliography rendering pass using the historical sample
  in an isolated temporary fixture, without adding it to published content.
- Workflow YAML, branch triggers, job dependency, and deploy permissions check.

The Linux Actions build, artifact upload, and live deployment still require
the first GitHub run. Existing navigation links to `/blog` have no corresponding
index page; this predates the migration and is left unchanged.

`_site/` was already tracked before this migration. The new `.gitignore` prevents
new untracked output from being added but does not untrack existing files.
The workflow regenerates the output and does not commit it. Local validation
uses `/tmp` to leave the tracked `_site/` files unchanged. When reviewing the
migration, you may remove the generated files from Git tracking while keeping
local copies with `git rm -r --cached _site`, then review those staged deletions
before committing. There is no need to publish a generated-output branch.
