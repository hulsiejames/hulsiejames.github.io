# Authoring and publishing the website

This guide covers the normal workflow for adding writing, reproducible
vignettes and projects to <https://hulsiejames.github.io>.

## How the repository is organised

```text
.
├── articles/                 # Published writing and vignettes
│   └── article-name/
│       └── index.qmd
├── assets/                   # Site-wide images and other static files
├── templates/
│   └── vignette.qmd          # Starting point for a new article
├── _freeze/                  # Saved computational results, when created
├── _quarto.yml               # Site navigation and shared settings
├── projects.qmd              # The Projects page
├── writing.qmd               # Automatic listing of articles
└── index.qmd                 # Homepage and latest-writing listing
```

Work on the `main` branch. The `gh-pages` branch is generated automatically and
should not be edited by hand. The `_site/` directory is also generated and
should not be committed or uploaded.

## Create a new writing or vignette

### 1. Choose a short folder name

Use lowercase words separated by hyphens, for example:

```text
articles/rail-demand-by-time-of-day/
```

Once an article is published, avoid changing its folder name because that
would change its public URL.

### 2. Copy the template

From the repository root in PowerShell:

```powershell
New-Item -ItemType Directory -Path "articles\rail-demand-by-time-of-day"
Copy-Item "templates\vignette.qmd" "articles\rail-demand-by-time-of-day\index.qmd"
```

Open the new `index.qmd` file and update its front matter:

```yaml
---
title: "Rail demand by time of day"
subtitle: "Optional extra context"
description: "One or two sentences used in listings and search results."
author: "James Hulse"
date: 2026-09-26
categories: [transport, rail, analysis]
draft: true
toc: true
code-fold: true
---
```

Important fields:

- `title` is the visible article title.
- `description` appears on the homepage and Writing page, so make it specific.
- `date` should be a fixed `YYYY-MM-DD` value rather than `today`.
- `categories` power the filters on the Writing page.
- `draft: true` keeps unfinished work out of the production site. Change it to
  `false`, or remove it, when the article is ready to publish.

### 3. Write the article

The template suggests a useful structure:

1. the question;
2. the data and its provenance;
3. the method and assumptions;
4. the results and interpretation;
5. limitations; and
6. instructions for reproducing the analysis.

Use ordinary Markdown for headings, links, lists and tables. Keep code close to
the claim or output it supports.

### 4. Add article-specific files

Keep small files used by only one article beside its `index.qmd`:

```text
articles/rail-demand-by-time-of-day/
├── index.qmd
├── chart.png
└── data/
    └── sample.csv
```

Reference them with paths relative to the article:

```markdown
![Daily rail demand by hour](chart.png)
```

Do not commit confidential, personal or licensed data that cannot be shared
publicly. For large public datasets, document the source and provide a download
script or stable release link instead of storing a large copy in the website
repository.

## Add code and interactivity

### Observable JavaScript

Observable JS is a good default for interactive controls and charts that need
to work on the static GitHub Pages site. It runs in the reader's browser and
does not need a server.

````markdown
```{ojs}
viewof multiplier = Inputs.range([0.5, 2], {
  value: 1,
  step: 0.1,
  label: "Multiplier"
})

Plot.plot({
  marks: [
    Plot.barY(
      [{name: "A", value: 10 * multiplier},
       {name: "B", value: 16 * multiplier}],
      {x: "name", y: "value"}
    )
  ]
})
```
````

See `articles/interactive-network-demo/index.qmd` for a complete working
example.

### R

Use an R cell inside the `.qmd` file:

````markdown
```{r}
#| label: fig-example
#| fig-cap: "An example figure"

plot(cars)
```
````

Run the article locally using the R packages it requires. The repository uses
`freeze: auto`, so Quarto can save executed results in `_freeze/`. Commit those
saved results along with the source. For a substantial analysis, use `renv` and
commit its lockfile so the package environment can be recreated.

### Python

Python content can use Jupyter-style cells:

````markdown
```{python}
#| label: python-summary

values = [4, 7, 9, 12]
sum(values) / len(values)
```
````

Render locally with the required Python and Jupyter environment. Commit the
source, any environment or requirements file, and the resulting `_freeze/`
files. Do not commit `.venv/`.

### Server-backed interactivity

GitHub Pages only hosts static files. Observable JS, htmlwidgets and supported
Jupyter widgets can work in static pages, but a live Shiny application requires
separate server hosting. A vignette can link to or embed a separately hosted
application if needed.

## Preview a writing locally

From the repository root:

```powershell
quarto preview
```

Quarto prints a local address and watches for saved changes. Before publishing,
check:

- the title, description, date and categories;
- headings and the table of contents;
- code, figures, tables and interactive controls;
- links and data-source attribution;
- the page at desktop and narrow/mobile widths; and
- that no private files or credentials are included.

Stop the preview with `Ctrl+C` in the terminal.

Then run the same full build used for publishing:

```powershell
quarto render
```

Fix any render error before committing. The output appears in `_site/` for
local inspection, but `_site/` is ignored by Git and is not uploaded.

## Publish a writing

When the article is ready:

1. Set `draft: false` or remove the `draft` field.
2. Run `quarto render` successfully.
3. Review the changed files.
4. Commit the source and push `main`.

Example PowerShell commands:

```powershell
git status
git diff
git add "articles/rail-demand-by-time-of-day"
git add "_freeze"
git diff --staged
git commit -m "Add rail demand vignette"
git push origin main
```

If the article has no locally executed R, Python or Julia code, `_freeze/` may
not contain anything new; in that case, omit that `git add` command.

After the push:

1. Open the repository's **Actions** tab on GitHub.
2. Wait for **Quarto Publish** to finish successfully.
3. Open <https://hulsiejames.github.io> and check the new article.

The article is discovered automatically. It will appear on the Writing page,
in search and in the RSS feed. The newest pieces also appear on the homepage.

## Add a new project

Projects are currently maintained manually in `projects.qmd`; they are not an
automatic listing.

### 1. Add a project card

Copy one of the existing `.project-card` blocks inside `.project-grid` and
replace its content:

```markdown
::: {.project-card}
### Project name

A short, factual explanation of the project, its purpose and your role.

[View the project](https://example.org/) · [Source on GitHub](https://github.com/example/project)
:::
```

Keep every project card inside the surrounding block:

```markdown
::: {.project-grid}

<!-- project cards go here -->

:::
```

If there is no live site or public repository, remove the irrelevant link
rather than leaving a placeholder.

### 2. Optionally add a project case study

For a longer explanation, create a normal article in `articles/` and add a
category such as `project` or `case study`. Link the project card to that
article:

```markdown
[Read the case study](articles/project-name/)
```

This approach gives the case study the same code, citation, table-of-contents
and interactive capabilities as any other vignette.

### 3. Preview and publish the project update

```powershell
quarto preview
quarto render
git add "projects.qmd"
git add "articles/project-name"
git diff --staged
git commit -m "Add project name to website"
git push origin main
```

Only add the `articles/project-name` command when a case-study article was
created.

## Make a small edit directly on GitHub

For a simple typo or a short text change, it is possible to open the source file
on GitHub, select the pencil icon, commit the change to `main`, and let the
publishing action run. Local editing is preferable for new articles, data,
images or code because it allows a full preview and render first.

Never edit the generated `gh-pages` branch. Any manual change there will be
replaced the next time the publishing workflow runs.

## If publishing fails

Open the failed **Quarto Publish** run in the repository's Actions tab and read
the first failed step. Common causes are:

- invalid YAML at the top of a `.qmd` file;
- a missing image, data file or link target;
- an R or Python result that was not rendered and frozen locally;
- code that works interactively but fails during a complete render; or
- an accidental file path that only exists on one computer.

Reproduce the problem locally with `quarto render`, fix it, commit the fix and
push again. A new successful run will replace the failed deployment.

## Pre-publish checklist

- [ ] The article or project has a clear title and description.
- [ ] `draft` is removed or set to `false` when publishing.
- [ ] Data sources, dates, licences and limitations are documented.
- [ ] The page works at desktop and mobile widths.
- [ ] `quarto render` completes successfully.
- [ ] No credentials, private data, local absolute paths or large generated
      files are staged.
- [ ] `git diff --staged` contains only the intended change.
- [ ] The GitHub Actions run succeeds after pushing.
- [ ] The live page is checked at <https://hulsiejames.github.io>.
