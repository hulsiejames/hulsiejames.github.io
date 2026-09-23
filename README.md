# hulsiejames.github.io

James Hulse's personal Quarto website for articles, analysis and reproducible
vignettes. The published site will live at <https://hulsiejames.github.io>.

## Preview locally

```powershell
quarto preview
```

Run a full production build before publishing:

```powershell
quarto render
```

The generated `_site/` directory is intentionally ignored. GitHub Actions
renders the source and publishes it to the `gh-pages` branch after changes are
pushed to `main`.

## Add an article or vignette

1. Copy `templates/vignette.qmd` to `articles/<short-name>/index.qmd`.
2. Replace the example metadata and write the article.
3. Add R, Python, Julia or Observable JS cells as needed.
4. Run `quarto preview` and check the page on both desktop and mobile widths.
5. Run `quarto render`, commit the source (and `_freeze/` outputs when code was
   executed locally), then push to `main`.

The Writing page and homepage listings update automatically from article
metadata. Set `draft: true` in an article's front matter to keep unfinished work
out of the published site.

## Reproducible computation

The project uses `freeze: auto`. Computational outputs can therefore be run
locally and checked into `_freeze/`, while GitHub only needs Quarto to rebuild
the site. This keeps older articles stable and avoids recreating every R or
Python environment on each deployment.
