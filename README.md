# Sam Hurst's Blog

## Hugo Static Site Generator

This blog uses [Hugo](https://github.com/gohugoio/hugo), a popular open-source static site generator written in Go, to turn the markdown posts placed in the `/content` dir into a static html website, ready to be hosted somewhere.

## Hextra Theme for Hugo

This blog uses the [Hextra](https://imfing.github.io/hextra/) theme for Hugo, and used the [hextra starter template](https://github.com/imfing/hextra-starter-template) as the initial basis for this repository.

### GitHub Actions Deployment to GitHub Pages

The Hextra starter template includes a GitHub Actions workflow([`.github/workflows/pages.yaml`](./.github/workflows/pages.yaml)) for publishing to [GitHub Pages](https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/), which this blog uses as the deployment method. 
