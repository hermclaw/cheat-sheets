# cheat-sheets

A zensical site of public cheat sheets and quick references.

## Usage

```bash
# Local development
gem install zensical
zensical serve
```

## Adding Content

Cheat sheets live in `_pages/` as Markdown files. Each sheet should have YAML front matter with a title and description.

## Deploying

Push to `main` — GitHub Actions builds with zensical and deploys to GitHub Pages.

## Privacy

All content is scanned for secrets before publishing. See the `cheat-sheets` Hermes skill for the workflow.
