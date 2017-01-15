---
title: Minimalistic Header Fragment Links with Jekyll
tags: [javascript, jekyll, css]
---

Github and many other sites show a link to a particular section when you hover a heading. This lets visitors deep-link particular sections. Jekyll, a popular static website generator, doesn't do this by default, however.

Kramdown, the Markdown parser which is generally used by Jekyll and thus on Github Pages, already does most of the work. It "slugifies" the heading's text and adds it as "id" attribute to the heading tag. This means you could already jump there via fragment links.

All that's missing is some JS and CSS for giving users direct access to these fragment links. Of course this kind of thing has been done before, but I wanted something just as minimalistic as this blog's design. There is no need for fancy icons&nbsp;&ndash; a simple '#' will do. Furthermore, I don't care about older browsers, which means I can cut it down even more.


## In brief

If you write this kind of Markdown:

```markdown
## Hello, World!
```

Kramdown generates this kind of markup:

```html
<h2 id="hello-world">Hello, World!</h2>
```

The only thing that's missing is a link to "#hello-world" which appears on hover.

So, let's add this fragment link with a bit of JavaScript which we inline at the bottom of the relevant template(s):

```javascript
for(let h of document.querySelectorAll('h1[id], h2[id], h3[id], h4[id], h5[id], h6[id]')) {
    h.innerHTML += ` <a href="#${h.id}" aria-hidden="true">#</a>`;
}
```

And some extra rules to the SCSS:

```scss
h1, h2, h3, h4, h5, h6 {
    &[id] > a {
        display: none;
    }
    &[id]:hover > a {
        display: inline;
    }
}
```

In case you're unfamiliar with SCSS' *parent selector reference* (`&`), this produces selectors like "h1[id] > a", "h2[id] > a", and so on.

## The uglified truth

The actual `<script>` tag I'm using in my *post.html* template looks like this:

```html
<script>for(let h of document.querySelectorAll('h2[id],h3[id],h4[id]'))h.innerHTML+=` <a href="#${h.id}" aria-hidden="true">#</a>`</script>
```

It skips "h1", because `<h1>` (without id) is only used for the title of the page. That `<h1>` is always at the very top of the page anyways. There would be no point in being able to link it. This heading's "section" is the page in its entirety.

"h5" and "h6" were omitted, because I'll never go that deep into any rabbit hole in a blog post. There won't be any documents with that kind of structure.

The second string could be golfed down to `<a href=#${h.id} aria-hidden=true>#`, but that looks just too wrong for my taste. It would work because neither attribute value needs quotes and because the `<a>` is auto-closed inside the heading.

The CSS I'm actually using is much shorter, but it's also way too unspecific for common use cases:

```css
[id] > a {
    display: none;
}
[id]:hover > a {
    display: inline;
}
```

I can get away with these shotgun selectors, because there aren't any other ids. There aren't any widgets, ads, or third-party scripts on this site. There aren't even any other first-party scripts for that matter. On this site, ids are only used for fragment links. If that ever changes, I'll have to fix this.