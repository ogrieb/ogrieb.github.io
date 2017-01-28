---
layout: default
title: "Include Figures with Captions in Jekyll"
excerpt: "How to include figures with captions into a Jekyll blog post."
published: true
---

# Include Figures with Captions in Jekyll

For my studies and for some of my text-focused work projects, I preferred to set text in [Latex](https://www.latex-project.org/). Here, it's [GitHub Flavored Markdown](https://help.github.com/categories/writing-on-github/). Which I am really starting to like. It's simple, light weight and very effective. One feature I missed for a post recently, was setting figures with a caption. Something that comes quite natural in Latex, but is not part of Markdown's or Jekyll's default features set.

Googling for it, I found some [good](https://superdevresources.com/image-caption-jekyll/) [descriptions](http://kevinmcgillivray.net/captions-for-images-with-jekyll/) how it can be done. The general idea is to once write your own html code snippet for it, to save it as a parameterized [include file](https://jekyllrb.com/docs/includes) and then to insert it where required by using the corresponding `include` tag.

The two examples I linked above used the html `img` [tag](https://www.w3.org/wiki/HTML/Elements/img) with an underlying caption, where they style the actual caption with CSS. I found the more abstract html `figure` [together](https://www.w3.org/wiki/HTML/Elements/figure) with the `figcaption` [tags](https://www.w3.org/wiki/HTML/Elements/figcaption) more straightforward to use, as this combination doesn't require CSS. After I build my own first version, I found another image syntax with complex formatting in [Jekyll's own documentation](http://jekyllrb.com/docs/includes/#passing-parameters-to-includes).

{% raw %}
  In Handlebars, {{ this }} will be HTML-escaped, but {{{ that }}} will not.
  {{ site.url }}/assets/{{ include.img }}
{% endraw %}

From these three concepts I derived my own image respectively figure syntax:

```html
<figure>
    {% raw %}{% comment %}{% endraw %} Link on image only included when set, as the browser
    would still attempt a mouse-over. {% raw %}{% endcomment %}{% endraw %}
    {% raw %}{% if include.url %}{% endraw %}
        <a href="{% raw %}{{ include.url }}{% endraw %}">
    {% raw %}{% endif %}{% endraw %}
            {% comment %} Alt-text is defaulted to image name. {% endcomment %}
            <img src="{% raw %}{{ site.url }}/assets/{{ include.img }}{% endraw %}"
                alt="{% raw %}{{ include.alt | default: include.img }}{% endraw %}"
                width="{% raw %}{{ include.width }}{% endraw %}"
                height="{% raw %}{{ include.height }}{% endraw %}"/>
    {% if include.url %}
        </a>
    {% endif %}
    {% raw %}{% comment %} Caption can be Markdown formatted. {% endcomment %}{% endraw %}
    <figcaption>{% raw %}{{ include.cap | markdownify }}{% endraw %}</figcaption>
</figure>
```

Observe, that as final touches the alt-text is defaulted to the image name and the caption is filtered with `markdownify`, so that it is expected as a Markdown formatted string.

Oliver Grieb  
January 23rd, 2017
