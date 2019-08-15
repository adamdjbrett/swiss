# Migrating [adamdjbrett.com](adamdjbrett.com) to version 2.0

## 2.0
- Switching to the Swiss theme by @broccolini
  - I loved [Moon by @taylantatli](https://taylantatli.github.io/Moon/) but it was time for a refresh.
- Atom, RSS, Sitemap, robots.txt, humans.txt by @Phlow
- to exclude pages form navigation in the swiss theme use these instructions from [tahirtaous](https://www.tahirtaous.com/exclude-pages-jekyll-navigation-menu-minima-theme/).
  - you must change navigation in two places:
      - `_includes` and update `header.html`
      - `_layouts` and update `home.html`
- Set up [yamlCV](https://github.com/AnotherKamila/yamlCV/) by @AnotherKamila
- Add image gallery from [Jekyll Codex](https://jekyllcodex.org/without-plugin/image-gallery/)
- modified `post_block.html` to use `{{ post.excerpt }}` instead of `{ post.content | strip_html | truncatewords:30 }}`

## 1.0
- [Moon by @taylantatli](https://taylantatli.github.io/Moon/)
- [md-cv](https://github.com/blmoore/md-cv)
