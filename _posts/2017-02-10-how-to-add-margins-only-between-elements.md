---
title: How to Add Margins Only Between Elements
tags: [css]
---
When it comes to nice-looking layouts, effective whitespace usage is key. There should be little space between elements you want to visually group together and significantly more space between elements you want to separate.

Of course, your whitespace usage should be consistent across all pages. All of your building blocks should behave in the same predictable manner. It shouldn't matter in which order they are put into containers. Conveniently for us, CSS makes this fairly easy if we follow some self-imposed rules.


If you're aware of the *Contrast, Repetition, Alignment, Proximity* (:poop:) rules, you've probably already guessed that this is all about *proximity* (or the lack thereof) and *repetition* (aka consistency). We'll achieve these two goals by handling all the intercomponent spacing in the same generic manner and letting our containers take care of their padding.

There are two ways to go about this: You can either give all of your building blocks a bottom margin except for the last child&nbsp;&ndash; or&nbsp;&ndash; you can give them a top margin except for the first child. We'll go with the latter.

## Make elements distance themselves

Using top margins makes more sense because it allows more significant elements, like headings and bigger widgets, to push themselves further away. For example, you might want to separate a heading from the previous paragraph and use little spacing between that headline and the next paragraph to group them. Headings logically belong to the paragraphs which follow, that's why it makes sense to visually group them into distinct sections:

![visual grouping of elements]({{ '/assets/images/2017-02-06-grouping.svg' | absolute_url }})

Since we made an exception for the first child, there is no superfluous margin at the very top. The container which houses those elements will now always have the padding you've asked for.

## Example CSS

I'll only do it here for `<h1>` and paragraphs to give you a general idea how it can be done:

```css
h1 {
    margin: 0;
}
h1:not(:first-child) {
    margin-top: 3rem;
}
p {
    margin: 0;
}
p:not(:first-child) {
    margin-top: 1rem;
}
```

If you're using a reset, nuking the default margins a second time won't be necessary.

In the past, when I couldn't use CSS3's `:not()`, I did it the other way around:

```css
h1 {
    margin: 3rem 0 0; /* Note: rem units are a CSS3 feature. */
}
h1:first-child {
    margin-top: 0;
}
p {
    margin: 1rem 0 0;
}
p:first-child {
    margin-top: 0;
}
```

An SCSS mixin is also an option:

```scss
@mixin marginTop($size: 1rem, $margin: 0) {
    margin: $margin;
    &:not(:first-child) {
        margin-top: $size;
    }
}
h1 {
    @include marginTop(3rem);
}
p {
    @include marginTop();
}
```

## Beware of invisible elements

This may come as a surprise, but `<script>` and `<style>` tags count as children. They are regular elements in the DOM which can be selected and styled like any other element. So, if there is some script at the very top of some container, that script element will be the first child, which means that whatever block which follows will have a top margin.

If you can't explain where some margin is coming from, check if the DOM does indeed look as you assumed.

If there is a `<script>` tag in the way, the preferred solution is to put it below (or inside) the widget.

If you can't change the markup, you can use a more specific selector as a last resort:

```css
script:first-child + h1 {
    margin-top: 0;
}
```

Kinda hideous, but if you only need it for 1 or 2 special cases it's probably alright.

## Why not owls?

Some people use the so-called "[lobotomized owl selector](http://alistapart.com/article/axiomatic-css-and-lobotomized-owls)" (`* + *`) for this, but it's annoyingly broad and matches way more than it should. While it's true that top/bottom margins do nothing for elements whose `display` property is set to `inline`, you still get that margin with other `display` values like `inline-block` or `list-item`.

I'm not a fan of undoing things, because, when you look at one of those property-value pairs, you can't really tell where that previous value was set or if it's still there. I try to avoid things which have the potential to become useless cruft in the future. I'd rather have a bit of repetition and let compression take care of the redundancy.

The owl doesn't help much anyways. You have to undo it in some places and override the margin in others. Unsurprisingly, it's also affected by invisible elements in the exact same way as more explicit selectors.