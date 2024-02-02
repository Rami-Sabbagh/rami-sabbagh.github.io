
# https://rami-sabbagh.com/

Welcome to the source-files of my personal blog 👋

The website is statically generated using [HUGO](https://gohugo.io/) with the nice [Eureka](https://themes.gohugo.io/themes/hugo-eureka/) theme.

## Running locally

Install the latest version of GO, and HUGO.

Clone this repository, install dependencies using:

```sh
go mod download
```

Then run the preview server with drafts enabled by:

```sh
hugo serve -D
```

## Theme reference

- [Content Management (Create new content)](https://www.wangchucheng.com/en/docs/hugo-eureka/content-management/).
- [Creating Multilingual Posts](https://www.wangchucheng.com/en/docs/hugo-eureka/multilingual-mode/).

## Creating a new post

```bash
hugo new posts/YYYY-MM-DD-post-title-in-snake-case.md
# or for multilingual
hugo new content/<language_code>/posts/<YYYY-MM-DD-post-title-in-snake-case.md>
```

For Arabic copy the file manually to `content/ar/posts` and translate it.

## Updating Eureka

This blog is built using my own fork of hugo-eureka, to have updates the fork has to be updated first
(by merging changes from the main repo).

```sh
go get -u github.com/Rami-Sabbagh/hugo-eureka
```

### Notes for developing the fork

- Rebuild the assets by running `hugo` (without parameters) in the root of the cloned `hugo-eureka` repository.
    - Check the files in `/resources/_gen/`.
- The line which compiles the stylesheets is in `layouts\partials\head.html`.
