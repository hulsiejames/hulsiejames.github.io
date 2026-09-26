# hulsiejames.github.io

James Hulse's personal Quarto website for articles, analysis and reproducible
vignettes. The published site will live at <https://hulsiejames.github.io>.

## Authoring and publishing guide

See [AUTHORING.md](AUTHORING.md) for the complete workflow for:

- creating writings and reproducible vignettes;
- adding projects to the Projects page;
- including images, data and interactive outputs;
- previewing and checking changes locally; and
- uploading to GitHub and publishing the live site.

The shortest version is: edit the source on `main`, run `quarto render`, commit
and push. GitHub Actions then builds and publishes the site automatically.

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

For worked examples, front-matter guidance and exact Git commands, use the
[full authoring guide](AUTHORING.md).

## Reproducible computation

The project uses `freeze: auto`. Computational outputs can therefore be run
locally and checked into `_freeze/`, while GitHub only needs Quarto to rebuild
the site. This keeps older articles stable and avoids recreating every R or
Python environment on each deployment.
