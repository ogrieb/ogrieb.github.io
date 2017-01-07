---
layout: default
title: "Site Launch, Learning Curve"
excerpt: "Welcome, this is what I had to learn to launch this site"
published: true
---

Welcome to my very first post on my freshly launched blog. Yay!

Having never run a website before - and last having written some html back in school some 15+ years ago - I am a bit proud of myself. Getting here was a nice little learning adventure over the last two days.

So, two days ago I finally decided to set up this site. I just started at the [GitHub Pages](https://pages.github.com/) site, which made it look easy enough. I setup my account, a repository, briefly familiarized myself with Git(Hub) and I quickly ended up with a 'Hello World' displayed on my site... but, that wasn't a blog yet.

Then, I started reading over all the referenced pages and also started crawling over the [Jekyll](https://jekyllrb.com/) site itself. Being a bit overwhelmed at all that Jekyll seemed to encompass, I was then quite happy to find them referencing [Jonathan McGlone's marvelous guide](http://jmcglone.com/guides/github-pages/) to GitHub Pages.

His guide really helped me to get a basic undertanding of how Git, GitHub Pages, html content, content styles (CSS), Jekyll generated content and Markdown come together. Thus, I finally ended up with something that actually resembled a blog: his exemplarily content that I started to play around with and to understand.

Still, that wasn't _my_ blog yet. His exemplarily included content styles weren't that pretty and I had already seen some of the much nicer GitHub Pages' - respectively Jekyll's - template styles. The [Cayman theme](https://github.com/pages-themes/cayman) in particular had taken my fancy.

Here is where I got a bit confused. I had so far never done anything with content styles, so I didn't really know how to get McGlone's marvelous blog structure styled differently. I understood, that he included the CSS files explicitly in the repository.

When I generated my repository freshly with the GitHub Pages' provided Cayman theme, though, there was no explicit CSS content... only a corresponding entry in the `_config.yml`:

```
theme: jekyll-theme-cayman
```

Besides that file, there was only the theme's showcasing `index.md`, which only included markdown content for the page's body. The layouting html as well as the CSS content seemed to have become included implicitly, i.e. hidden.

So I was a bit lost, how to costumize the page's layout itself? The page's title in the header for example was shown as 'ogrieb.github.io' and I had no idea where that parameter was set. The guide had the page-header layout - and with that, all included 'liquid tags' as I would learn to call them - explicitly set in the now abscent `_layouts` folder.

Reading the Cayman theme's [README](https://github.com/pages-themes/cayman/blob/master/README.md) (again - notice the part about costumizing...) then cleared up those questions. And here we are!

Cheers  
Oliver Grieb  
December 25th, 2016
