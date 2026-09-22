# Welcome to [Slidev](https://github.com/slidevjs/slidev)!

To start the slide show:

- `npm install`
- `npm run dev`
- visit <http://localhost:3030>

Edit the [slides.md](./slides.md) to see the changes.

Learn more about Slidev at the [documentation](https://sli.dev/).


## Notes

- **slidev** is a slides maker and presenter designed for developers
    - **text-based** (Markdown), themable, dev-friendly (code-highlighting), interactive (embed Vue components), recoding/camera views, portable (e.g. pdf export, SPA), hackable 
-  public slide show   > http://localhost:3030/
-  presenter mode      > http://localhost:3030/presenter/
-  slides overview     > http://localhost:3030/overview/
-  export slides       > http://localhost:3030/export/
- Navigation: next animation/slide (`right`, `space`), prev animation/slide (`left`, `shift space`), next/prev slide with `up`/`down`  
- TOC: `<Toc minDepth="1" maxDepth="1" />`
    - title inferred from slide content, or overridden by `title` and `level` in frontmatter
- Code highlight: 3 backticks + lang name + `{2,3}` to highlight lines 2 and 3
- Shiki magic move: animations across multiple code snippets
- Components: you can use Vue components inside your slides
    - `<Tweet id="1390115..."/>, <BlueSky/>, <Youtube/>, <Counter :count="10" />`
- Themes (specify in frontmatter `---` to `---`):
    : `theme: default`