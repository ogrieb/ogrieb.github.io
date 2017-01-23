---
layout: default
title: "Include Figures with Captions in Jekyll"
excerpt: "How to include figures with captions into a Jekyll blog post."
published: true
---

# Include Figures with Captions in Jekyll

For my studies and for some of my text-focused work projects, I preferred to set text in Latex. For these posts here, it's Markdown. Which I am really starting to like, simple, light weight but very effective. One feature I missed for a post recently, was setting figures with a caption. Something that comes quite natural in Latex, but is not part of the Markdown or Jekyll default features set.

Googling for it, I found some [good](https://superdevresources.com/image-caption-jekyll/) [examples](http://kevinmcgillivray.net/captions-for-images-with-jekyll/) of how it can be done. The general idea is to once write your own html code snippet for it, to save it as an [include file](https://jekyllrb.com/docs/includes) and then to insert it where required by using the corresponding `include` tag.

The two examples I linked above still used the html `img` [tag](https://www.w3.org/wiki/HTML/Elements/img), where they style the caption with CSS. I found the newer html `figure` [tag](https://www.w3.org/wiki/HTML/Elements/figure) more straightforward to use.
