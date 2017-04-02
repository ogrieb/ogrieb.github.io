---
layout: default
title: "Include TeX/LaTeX Formulas in Jekyll with MathJax"
excerpt: "How to include TeX/LaTeX formatted formulas into a Jekyll blog post with a convenient MathJax include tag."
published: true
---

# Include LaTeX Formulas in Jekyll with MathJax

Just as I missed including figures the 'LaTeX' way in my posts. I was also missing an easy way to annotate math formulas, just as in LaTeX. In [this]({{ site.url }}/2017/01/28/figure-include) recent post I presented my solution to conveniently included figures with a Jekyll `include` tag.

The commonly recommended way to display LaTeX annotated formulas on a web page is by using the JavaScript display engine [MathJax](http://docs.mathjax.org/en/latest/mathjax.html). The MathJax script is easiest included from a Content Delivery Network (CDN) as recommended by the [official documentation](http://docs.mathjax.org/en/latest/start.html#using-a-content-delivery-network-cdnhttp://docs.mathjax.org/en/latest/start.html#using-a-content-delivery-network-cdn):

```html
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
```

In a generic web site there are two essential ways to include such a script tag, either in a html page's `header` or `body`. In a Jekyll setup such as this site, including it in the header would infer to change the respective pages' layout.

My site for example is based on the [Cayman theme](https://github.com/pages-themes/cayman) and so far, the main page as well as the blog posts are still based on its [default layout](https://github.com/pages-themes/cayman/blob/master/_layouts/default.html). Hence, 'just' including the script in this header would then effect all of my pages. To have it not included on every page, it would need to be parameterized or I would need to set up a MathJax dedicated layout altogether. I didn't like either option, as I really like the whole LaTeX philosophy of _not_ having to care for the layout so much.

Thus, I prefer to explicitly include the MathJax script in each page's `body` with the following Jekyll include tag:

{% raw %}
```html
<script type="text/x-mathjax-config">
    {% comment %} Enable $...$ in-line delimiters and escaping of dollar sign
    in normal text with \$, similar to real LaTeX. {% endcomment %}
    MathJax.Hub.Config({
        tex2jax: {
            inlineMath: [['$','$'], ['\(', '\)']],
            processEscapes: true}});
</script>
{% comment %} Configure for TeX/LaTeX input only. {% endcomment %}
<script type="text/javascript" async
    src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS_CHTML">
</script>
```
{% endraw %}
