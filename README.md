# Math Blog

Source code for [lukakirigin.com](https://lukakirigin.com).

The website's design and infrastructure are based on [Gregory Gundersen's blog](https://gregorygundersen.com/blog/2020/06/21/blog-theme).

## Development

The website is built using Jekyll and hosted on GitHub Pages.

To run locally:

```bash
bundle install
bundle exec jekyll serve
```

Posts are written in Markdown in `_posts/`. The `_site/` directory contains generated HTML.

## Notes

The site uses MathJax for mathematical notation and `jekyll-scholar` for bibliography support. 