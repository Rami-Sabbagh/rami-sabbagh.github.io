
# https://rami-sabbagh.com/

Welcome to the source-files of my personal blog 👋

The website is statically generated using [HUGO](https://gohugo.io/) with the nice [Blowfish](https://blowfish.page/) theme.

## CLI Recipes

- Start local server with live reload and drafts enabled: `hugo serve -D`.
- Build site for publishing: `hugo`.
- Create new post: `hugo new content posts/YYYY-MM-DD-my-new-post.md`.

## Writing Tips

- Check [Shortcodes](https://blowfish.page/docs/shortcodes/) for enhancing content within posts.
    - There's one for [GitHub Repository Card](https://blowfish.page/docs/shortcodes/#github-card), good for fancy display of repositories.
    - There's a fancy [`timeline`] shortcode for listing events!
    - For typewriter effect on text: [`typeit`](https://blowfish.page/docs/shortcodes/#typeit).
    - For lightweight embedding of YT content: [`youtubeLite`](https://blowfish.page/docs/shortcodes/#youtube-lite).
    - [Lead](https://blowfish.page/docs/shortcodes/#lead): used to bring emphasis to the start of an article. It can be used to style an introduction, or to **call out an important piece of information.** Simply wrap any Markdown content in the `lead` shortcode.
    - For Arabic/RTL content, there's a short-code to switch direction within the post: [`rtl`](https://blowfish.page/docs/shortcodes/#ltrrtl).
    - For including Markdown fragments from external sources, use [`mdimporter`](https://blowfish.page/docs/shortcodes/#markdown-importer).
    - For color palettes, theres [`swatches`](https://blowfish.page/docs/shortcodes/#swatches).
- It's possible to link posts on other blogging platforms, and published research articles by defining [externalUrl](https://blowfish.page/docs/content-examples/#external-links) in the front-matter.
- When there's a series of posts, they better be [defined as so](https://blowfish.page/docs/series/#series-behavior) in Blowfish.
- It's possible to override theme parameters for posts within a directory using the [`cascade`](https://blowfish.page/docs/content-examples/#list-pages) parameter within the front-matter of the `_index.md` for that directory.

## Theme Documentation Links

- [Blowfish Content Samples](https://blowfish.page/samples/).
- [Blowfish Front-matter](https://blowfish.page/docs/front-matter/).
- [Blowfish Multi-authors](https://blowfish.page/docs/multi-author/).
- [Blowfish Advanced Customization](https://blowfish.page/docs/advanced-customisation/).