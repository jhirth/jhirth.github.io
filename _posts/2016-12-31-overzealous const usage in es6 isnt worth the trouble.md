---
title: Overzealous `const` Usage in ES6+ isn't Worth the Trouble
tags: [javascript]
---

ES6 (aka ES2015) introduced the `let` and `const` keywords. Both let you create block-scoped variables. The difference is that those created with `const` are read-only. Well, the binding is. It doesn't make objects immutable. It only prevents you from assigning something else to that variable.

Immutability is all the rage when it comes to functional programming. Naturally, using `const` wherever you can became popular in some circles.


> I always use `const` and change it to `let` if I get a squiggly.<br>
> &mdash; *someone on Reddit*

Sounds like mindless busywork to me, but what does pressing all those extra keys actually improve?

If you see some `const` declaration, you can be sure that nothing else will ever be assigned. However, if you see some variable somewhere further down the file, you won't immediately know if it was created via `let` or `const`. You have to tool-tip it or use *Go to Definition* to access this off-screen information.

## Tooling could do it better

An editor could simply display *single-write* and *multi-write* variables differently. For example, multi-write variables could be italic.

So, `foo` would be single-write while *`bar`* would be multi-write.

With this analyzer-based highlighting in place, you can always use `let` and you would still be able to tell if something is single-write at the point of its declaration. Additionally, you could now directly see anywhere in the file if some variable is single-write or not.

![syntax highlighting example]({{ '/assets/images/2016-12-31-const.svg' | absolute_url }})

Makes sense, right?

Assigning function expressions to `const`s also became popular:

```javascript
const foo = () => {
  // ...
};
function bar() {
  // ...
}
```

The `const` option is longer and also more annoying to write than using a regular function declaration. Using `const` for functions is supposed to guard against accidental redeclarations, but current tools like ESLint or TypeScript's compiler already catch that.

Now that `const` was "freed up", it can be used for numerical (etc) constants again, which means you can also use ugly `ALL_CAPS` to mark them as constants&nbsp;&ndash; just like in the good old days.

Personally, I'd prefer to leave keeping track of the "constness" of variables to machines rather than doing this myself. Deciding up front whether some variable should be declared via `const` or `let` introduces some mental overhead at a rather inconvenient time. I don't think this distraction is worth it given that you only get a questionable improvement in return.

Even always going with `const`, and then only changing it to `let` when you get a squiggly, is something which sidetracks you as you try to get some work done.

## Comprehension will always be a bottleneck

In Dart's early days, when the Dart Editor was still a thing, there was the tremendously useful *Purple of Doom*™. Whenever the type couldn't be inferred, the variable in question got colored purple. Once you got rid of all the purple, your program was fully typed, which is highly desirable for any code which is used in production.

Basic syntax highlighting is great, but this kind of smart highlighting gave your reading comprehension a nice boost. Getting immediate access to this elaborately acquired information felt like brain augmentation.

Since code is read far more often than it's written, anything which immediately gives you a clearer picture is a welcome improvement. Anything which gets the relevant parts of your codebase quicker or more accurately into your brain is worth exploring. Fully grasping the current situation is always the first step. Optimizing this is certainly worth it.