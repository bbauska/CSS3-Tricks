Before you continue reading this article, there are a few terms and concepts i assume you should be aware of prior. Please, if you don’t have knowledge of these, i urge you to take a second to read them up (or skim through them — to refresh your memory). These terms and concepts will be important to the rest of this article and are as follows: 
<a href="https://web.dev/learn/css/specificity/">
Specificity</a>, 
<a href="https://developer.mozilla.org/en-US/docs/Glossary/Block-level_content">Block-Level<a>, 
<a href="https://adrianroselli.com/2015/10/html-source-order-vs-css-display-order.html">Source Order</a>, 
<a href="https://www.freecodecamp.org/news/css-position-property-explained/">CSS Positioning</a>, 
<a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Quirks_Mode_and_Standards_Mode">Quirks Mode<a>, 
<a href="https://css-tricks.com/css-ruleset-terminology/">CSS RuleSet</a>, 
<a href="https://people.goshen.edu/~paulmr/physix/385/pub/css2.php">CSS Layout Containers</a>.

This article also assumes that you know the basic CSS properties and some of their values and require no primer or formal introduction to them.

Finally, after reading up on these terms and concepts above with full understanding, you can continue reading this article presently.

### Be careful about adding a relative positioning to the body tag 🤨
This first principle is straightforward and adhering to it will save you stress in the future. You can however set position: relative; on the html tag if you want to (depending on the design you’re working on or you just want tag that are positioned absolutely without insets not flying off the screen) but never ever do so on the body tag. Why ? It’s is the reference for all absolutely positioned element anyway so it serves no purpose.

Also, setting position:relative; on the body seems to be a relic from the IE v5.x (Internet Explorer 5) days for IE v5.x in quirks mode and fix a nasty bug. Finally, it helps if you don’t mess with the rules concerning containing blocks by adding a position: relative; to the body tag.

### Never set rigid width and/or height values on an element with absolute/fixed units 😬
This second principle ensures that you are not painting yourself into a corner as you write your CSS code. This principle applies whether you use utility classes (e.g TailwindCSS) or not. If there is one lesson i have learnt from my years of building software, it is this:

Do everything you can now to make it easy for very likely changes that can be made to software code to be made now and in the future.

This applies very well to CSS too. You see when you set rigid with values for width and/or height , you are making it extremely difficult to make changes later on especially when trying to make that element responsive or scaling the font-size of the text inside the element.

It’s better to either take advantage of normal flow (i.e width: auto) or prefer (min-width / min-height) and/or (max-width / max-height ) to just width or height . This is also very important to note when styling layout container and widgets like buttons and text boxes.

If you must use width or height properties, then use relative units like vw , vh , % or rem. Although, sometimes it’s okay to use px for width like for the fixed width of the menu bar of a dashboard page.

```
/* This makes it hard to style this button later in a responsive way */

button {
  display: inline-block;
  width: 320px;
}

button.full-width {
  /* '%' is not an absolute unit, so it's okay to use `width` here with it */
  width: 100%;
}

/* Media query = less than or equal to 420px */
@media screen (max-width: 420px) {
  button {
    /* Now, i have to override the width so, the button looks better on mobile */
    width: 190px;
  }
}
```

Now, compare the above with this:

```
/* DO THIS INSTEAD !!!!!!! */

button {
  display: inline-block;
  max-width: 320px; /* width can now be less than or equal to 320px */
  min-width: fit-content; /* `white-space: nowrap` on steriods */
}

button[title] {
  min-width: max-content;
}

button[title] span {
  white-space: nowrap;
  text-overflow: eliipsis;
}

button.full-width {
  /* '%' is not an absolute unit, so it's okay to use `width` here with it */
  width: 100%;
  max-width: none; /* Revert the max-width to default value */
}

/* NOTICE: We don't need a media query at any screen size any more */
/* The button resizes properly to fit (even it's container) at any screen size */
```

As mentioned earlier, it’s also even more important to do this when styling layout containers. You don’t want to be too overwhelmed by multiple break-points for layouts. In fact, you don’t want to consider too many break-points at all for your layouts. What you want is to have the feel of responsiveness across multiple screen sizes to be gentle and fluid (not sharp and sudden).

I personally only consider multiple break-points when i have tight guideline on how the UI should look on many distinct screen sizes.

See below:

```
.wrapper {
  width: 300px;
  padding: 0 30px;
  box-sizing: border-box;
}

/* Media query = greater than or equal to 420px */
@media screen (min-width: 420px) {
   /* NOTICE: I have to set width to take up available space as screen size increases */
  .wrapper {
    width: 100%;
  }
}
```

Compare the above to this below:

```
/* DO THIS INSTEAD !!!!!!! */

.wrapper {
  display: flex;
  /* default is `width: auto`; easier to make normal flow work for you */
  padding: 0 30px;
}

@media screen (max-width: 420px) {
  /* NOTICE: The only thing i need to adjust is padding on smaller screens */
  .wrapper {
    padding-left: 10px;
    padding-right: 10px;
  }
}
```

The exception to this is when you need a certain layout container or wrapper to have a specific width for 
most screen size ranges.

```
/* We only use a rigid value with absolute units because this is a sidebar */
/* It could be a sidebar for an admin dashboard */

main:has(.sidebar) {
  display: flex;
}

.sidebar {
  flex: 0 0 auto;
  min-width: 0; /* Fixes a flex issue */
  width: 350px;
}
```

### Always add margins and borders indirectly to any element 🤠
Adding margins directly to any element using CSS is any easy win in the short-term but a horrific decision in the long-term. The reason is simple: You have to do a lot of work later on to clean-up and override the margins when you use that same element in a different context mostly under a different parent tag (read as parent selector).

```
/* DON'T DO THIS !!! */

.call-to-action {
  margin-left: 20px;
  border: 2px solid cyan;
  cursor: pointer;
}

/* When you want to use the button in a specific or different context
   from the earlier one, you have to override the margin defined earlier */

.newsletter .call-to-action {
  margin-right: 40px;
  /* The margin-left of 20px is still active - so this is an override */
  margin-left: 10px;
  border: 1px solid brown;
}
```

So, it’s much safer, better, stress-free and cleaner to add margins indirectly via a parent selector like so:


```
/* DO THIS INSTEAD !!!!!!! */

.call-to-action[disabled] {
  cursor: not-allowed;
}

/* `.news-letter-subscription` is the parent selector */

.newsletter-subscription .call-to-action {
  margin-right: 40px;
  /* No active margin-left CSS style - So, no overrides here */
  margin-left: 10px;
  border: 1px solid brown;
}


/**
 * If we move the element with class `.call-to-action` into another parent tag, 
 * it doesn't move with the margin from where it was before.
 *
 * It drops that margin and picks up the new one
 */

.contact-form .call-to-action {
  margin-top: 10px;
  margin-left: 25px;
  border: 1px solid cyan;
}

/* This is cleaner, the margins are never overrriden; just appended */
```

<a href="https://tailwindcss.com/">
Tailwind CSS</a> is guilty of this and sadly there’s no way to fix it because of the philosophy of atomic/functional CSS that Tailwind lives by. This leads to needing to override margins at other breakpoints or in other page contexts.

### Only use unit-less number values for line heights 🤓
Well, it’s commonplace to setup line-height using unit values like px, % or em. However, it’s actually not advised to use unit-based values as a value for this CSS property. This is because the value of line-height is closely tied to the font-size of the element on which it is declared and can create some unexpected results.

This is also more accessible.

```
/* DON'T DO THIS */

body {
  line-height: 5px;
}

/* DO THIS INSTEAD */

body {
  line-height: 1; /* No units */
}
```

See more in this MDN excerpt.

### Always set properties inherited by default on the closest parent to element (block or inline) to the target text node(s) or on the target element itself 😊
When it comes to all CSS properties are inheritable (by default), there are only two categories (as all of them are used for either one of two types of formatting):

	1. Display formatting (display variations — e.g. empty-cells, caption-side, visibility, border-collapse, list-style-type, position, overflow, cursor, opacity)
	2. Visual formatting (content variations — e.g. font-size, font-weight, font-style, font-variant, line-height, widows, orphans, text-align, color, cursor, direction, text-transform, letter-spacing, quote)

The rule of thumb is to always ensure that you define inheritable CSS properties on the closest block-level element to the child element containing text or an inline elements’ child element which you are targeting with those inheritable styles.

For instance: I see people do this:

```
/* TOO FAR FROM TARGET TEXT OR INLINE ELEMENT THAT WOULD NEED IT */

body {
  quote: "«" "»" "‹" "›";
}
```

It should be this:

```
/* PERFECT! */

q {
  quotes: "«" "»" "‹" "›";
}

q::before {
  content: open-quote;
}

q::after {
  content: close-quote;
}
```

Another example:

```
/* AGAIN, TOO FAR FROM TARGET TEXT OR INLINE ELEMENT THAT WOULD NEED IT */

body {
  color: black;
  text-align: center;
  min-height: 100%;
}
```

The color and text-align should be defined on the element very close to the text that should have said style like so:

body {
  min-height: 100%;
}

span {
  color: black;
  text-align: center;
}
There’s an exception to this guideline however, which is for font-family and maybe line-height on the body tag.

body {
  font-family: sans-serif;
}
For the font-size property, it is not recommended to set a default font-size on your root (html) except you don’t intend to support users who modify their base font-size browser settings.

But, you could set it if you hope to utilize fluid typography sizing on you web page (i.e. the font-size changes according to both the size of the viewport and browser setting for font-size).

See below: 👇🏾👇🏾👇🏾👇🏾👇🏾

/* @INFO: Taken from Kyrieon CSS  source */
/* @CHECK: */ 

/* @SEE:  https://stackoverflow.com/a/27315792 */


/* Safari <8 and IE <11 */
/* @NOTE: Safari 8 and below does not support `transform: translate(0,50%)` */
/* @NOTE: IE 11 and below doesn't support ... */ 
@supports (not (transform: translate(0,50%)) and (font-size: calc(0.1vw + 0.1rem))) 
  or (not (border-image: inherit) and (font-size: calc(0.1vw + 0.1rem))) {
  /* Fluid Font-size */
  @media screen and (max-width: 24em){
    html[data-page-typography="fluid"] {
      font-size: calc( 1em + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
  @media screen and (min-width: 25em){
    html[data-page-typography="fluid"] {
      font-size: calc( 1em + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
  @media screen and (min-width: 50em){
    html[data-page-typography="fluid"] {
      font-size: calc( 1em + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
  @media screen and (min-width: 75em){
    html[data-page-typography="fluid"] {
      font-size: calc( 1em + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
  @media screen and (min-width: 100em){
    html[data-page-typography="fluid"] {
      font-size: calc( 1em + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
  @media screen and (min-width: 125em){
    html[data-page-typography="fluid"] {
      font-size: calc( 1em + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
  @media screen and (min-width: 150em){
    html[data-page-typography="fluid"] {
      font-size: calc( 1em + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
}

/* All Modern browsers Safari>9, Chrome>12, Firefox>6, Edge>16 */
@supports (font-size: calc(0.1vw + 0.1rem)) {
  /* Fluid Font-size */
  @media screen and (min-width: 25em) {
    html[data-page-typography="fluid"] {
      font-size: calc(1rem + 0.5vw);
    }
  }

  @media screen and (max-width: 24em) {
    html[data-page-typography="fluid"] {
      font-size: calc(16px + (24 - 16) * (100vw - 400px) / (800 - 400) );
    }
  }
}
Now, in the sprit and promise of CSS, you can collect and factor out all inheritable CSS properties and rulesets (common to a set of nested elements) to elements much higher up in the nesting hierarchy. However, you should only do this after properly establishing clear and concrete commonalities in these elements that would share this inheritable CSS.

For example:

.comment {
  font-size: 0.68rem;
  letter-spacing: -0.0034em;
  color: #333;
}

.citation {
  font-size: 0.68rem;
  text-indent: 0.0625em;
  color: #333;
}
Now, from the above, you can see that both the .citation and .paragraph classes have some commonality in the inheritable CSS properties which can be factored out.

This will become 👇🏾👇🏾

.feed-block > .comment, /* Use direct descendant selectors '>' */
.feed-block > .citation {
  font-size: 0.68rem;
  color: #333;
}

.comment {
  letter-spacing: -0.0034em;
}

.citation {
  text-indent: 0.0625em;
}
I prefer going through the motions of clearly detecting commonality over. time before factoring it out like so. Why ? because, i don’t want to employ the idea of removing obvious duplications (DRY) too early or too prematurely.

It’s okay to use absolute units for horizontal/inline margins and paddings, media-queries, box-shadows, text-shadows, border-radii, outlines and borders 🤗
Never make it a habit to use px (pixel), ex, ch or centimeter unit (cm) for all widths, heights, paddings and font-sizes. Why ? It doesn’t scale and can cause problems later on especially where responsive layouts are a requirement or you have to make the web page more legible on certain screens or media.

Use rem unit for margins, paddings and font-sizes because they build on top of the user’s preferences and not override them the way using px does.

Using px, ch, cm or rem for everything can be very bad from an accessibility standpoint.

Also, use % units for widths and heights (dimensions) of block elements that are farther away from the body tag and whose dimensions are relative to their direct parent. Then, use vh or vw for block elements that are very much closer to the body tag or even the body tag itself. There are moments when you might want to use fixed units (e.g. px or ch) for widths or heights, for such moments, do not use the width property. Simply make use of the max-width/min-width property or max-height/min-height property instead (as stated earlier).

I also see people use viewport units (vw) or the percentage unit (%) for font-size. This is very bad for accessibility too. You might want to use the clamp() function or the calc() when using rem, vw or % for font-size.

Furthermore, use em for visual formatting values that don’t relate directly to the font e.g. text-indent, word-spacing, letter-spacing as these have to be relative to the closest block-level parent of the element.

For instance:

/* `ch` is a '0' character fixed unit so, we don't use `width` to set it */

.textblock p {
  min-width: 23ch; /* 23 '0' (zero) character width dimension */
}

/* I want to center this information card but want it to be 
   responsive by default as well */

.feed {
  /* I can't use the 'width' property as i want to default responsiveness */
  /* width: 600px; */
  
  /* I don't want this card to overflow horizontally on mobile screens */
  max-width: 600px;
  /* Give it equal spacing at the horizontal edges always */
  margin: 0 auto;
  /* beautify... */
  background-color: #333333;
  padding: 0 1.5rem;
}
Finally, never set widths or heights using fixed/absolute units on buttons and form inputs except you require them to fill up the entire horizontal/vertical space on either side. Use padding and font-size instead to size your buttons for different screen sizes: mobile, desktop, tv e.t.c.

For instance:

/*  !!! USING SOME UNSEMANTIC CLASS NAMES FOR BREVITY !!! */

button,
.button,
[role="button"] {
  user-select: none;
  cursor: pointer;
  touch-action: manipulation;
}

[role="presentation"] {
  touch-action: none;
  will-change: contents;
}

aside,
[role="complementary"],
[role="scrollbar"] {
  will-change: scroll-position;
}

object[role="application"] {
 clear: both;
}

.button.x-small { /* No width or height used for sizing the button */
  padding: 0.1rem 20px;
  font-size: 0.7rem;
}

.button.small { /* No width or height used for sizing the button */
  padding: 0.2rem 32px;
  font-size: 0.9rem;
}
Also, Tailwind has a bad habit of using mostly rem for everything else except media-queries which i totally detest and i believe is a bad practice because sometimes i want to set a root (html) font-size other than the browser default of 16px. Please, only use px for media-queries (you can perhaps use em/rem for container-queries) when you are not taking the browser font-size settings (or browser zoom) into account. Otherwise, use em for all media-queries (though this wasn’t completely feasible until Safari v15 fixed a nasty bug).

Also, using ch for media-queries is perfectly fine and very accessible in certain cases (especially when aligning viewport width with user font-size settings on the browser)

@media (max-width: 100ch) { /* @NOTE: `ch` is a fixed/absolute unit */
  /* When the viewport can only hold 100 characters in a line without breaks */
  ...
}
Lastly, you probably do not want to use viewport units beyond these 5 tags: <html>, <body> and <header>, <footer>, <main> for accessibility purposes.

Always effect full viewport height on body and html tags 💪🏾
Normal flow is a concept that doesn’t include vertical expansion of the content area/box of any block-level element. This is the first thing that doesn’t seems obvious when writing CSS for a particular web page in development. The reason why is that no one considers how it can become a hindrance later on. When you don’t setup the height of the body tag, you run into problems when you use fluid heights (in % only) on grand children tags (tags nested inside) of the body tag. If the body tag doesn’t fill the height of the viewport, then the tags inside the body don’t stretch to a height you’d probably desire (since % heights in CSS are relative to the parent tag) when coding up your CSS. If you use viewport units instead of percent units, then you can force the height of the nested tags in the body tag to stretch as you would like but it will be problematic when viewed on mobile or in a responsive situation.

There are many solutions to this out there including this one. However, i feel they are lacking. The only tag i feel comfortable setting viewport units on is the html tag. Every other tag can have a fluid dimension with a percent unit including the body tag. So my own solution is as setup here.

Never use CSS positioning when building page layout containers ❌
This is in fact a very important thing to take note of. When making layouts and setting up layout containers for a 2-column or 3-column web page, it’s important to make use of either flex box or grid layout systems and avoid using positioning (position: absolute; especially) to achieve what flex box or grid can also achieve. The problems you are guaranteed to run into when you want to start building in responsiveness to the web page is maddening.

Keep it really simple for layouts.

For example: 👇🏾👇🏾👇🏾

Take a look at this: https://codepen.io/isocroft/pen/mdxWdYP. If you look at the output of the pen, it looks like it has parts of the layout positioned (as fixed or absolute) but it’s not.

There are exceptions to this but they should only apply to modals, popups, flying menus or a floating navigation.

Use mostly closed-range CSS media queries 🤩
There are two broad ways to write your media queries: open-range and closed-range.

@media screen (max-width: 45rem) { /* OPEN-RANGE */
  ...
}

@media screen (min-width: 0rem) and (max-width: 45rem) { /* CLOSED-RANGE */
  ...
}
There seems to be a very innocuous difference between using one over the other but the amount of unnecessary clean-up work you give to yourself when you use open-range media queries isn’t worth it. The CSS rule you assign to a particular CSS selector spans multiple breakpoints when you use open-range media queries so you always have to override them at a larger breakpoint.

Become a Medium member
For instance:

/* For this media query, the margin of 20px applies to all breakpoints

   like: 1024px, 1336px, 1680px and so on and so forth till infinity */

@media screen (min-width: 768px) {
  .newsletter .call-to-action {
      margin: 20px;
   }
}

@media screen (min-width: 1024px) {
  .newsletter .call-to-action {
     /* The margin of 20px is still active at this breakpoint */

     /* What if i don't want a margin on this element at all at 
        this breakpoint ? */

     /* I have to override it here */

     margin: 0; /* !!! unnecessary CSS code !!! */
   }
}
But with closed range media queries, i don’t need to clean-up after myself as i write CSS especially when coding mobile-first solutions.

/* For this media query, the margin of 20px doesn't apply to all 

   breakpoints */

@media screen (min-width: 768px) and (max-width: 1023px) {
  .newsletter .call-to-action {
      margin: 20px;
   }
}

@media screen (min-width: 1024px) and (max-width: 1410px) {
  .newsletter .call-to-action {
    /* The margin of 20px is NOT active at this breakpoint */

    /* No extra CSS code needed to override anything */
  }
}
Tailwind CSS has popularised the use of open-range (min-width) media queries to employ mobile-first styles. Luckily, there’s a way to use close-range styles in Tailwind CSS by using the max-* modifiers for breakpoint ranges which is better than using any of the md:, sm:, lg:, xl: breakpoint modifiers by itself.

There are sometimes when an open-range media query might be necessary when working with max-width or min-width . Yet, under the condition that the minimum for max-width is meant as zero or the maximum for min-width is meant as infinity.

!important — is a tool of very last resort 😎
One of the reasons i really disliked Bootstrap v2 and v3 was the criminal and flagrant use of the !important rule. It was a pain to be honest.

Overriding and building on top of the style definitions of Bootstrap 2 and Bootstrap 3 was a nightmare because of this. The !important rule helps you easily override CSS rule in a ruleset with much low selector specificity than in an existing ruleset. However, it should be use with caution and should be the very last resort after all other cleaner methods have been employed like reordering the source order of the CSS rulesets in question.

Read more here.

Say hello to “shrink-to-fit” triggers — they come in handy a-times✅
A shrink-to-fit trigger is a specific CSS property-value pair that causes the content area/box of an element to collapse from bounds or edges tightly around its’ contents and in so doing assumes the size of the sum of the sizes of its’ content.

Very popular examples include display: inline-block OR display: inline-flex OR position: absolute OR width: fit-content. You can use shrink-to-fit triggers to safely make a block-level element (which may contain other block-level elements) to behave like an inline element (while also being able to expand in size horizontally) and allow other elements stay beside it on a web page.

We can use shrink-to-fit triggers within utility classes:

.static\:flex-\[shrink-to-fit\] {
    flex: 1 1 auto;
    position: static;
    /* shrink-to-fit trigger => */display: inline-flex;
    min-height: 0;
    min-width: fit-content;
}

.static\:block-\[shrink-to-fit\] {
    position: static;
    /* shrink-to-fit trigger => */display: inline-block;
    min-height: 0;
    min-width: fit-content;
}
  
.absolute-\:flex\[shrink-to-fit\] {
    flex: 1 1 auto;
    /* shrink-to-fit trigger => */position: absolute;
    isolation: isolate;
    min-width: fit-content;
}
For more examples, take a look at these 2 extra ones:

https://codepen.io/isocroft/pen/gOOeqWa
https://codepen.io/isocroft/pen/WNwgqOb
When writing CSS, the form (skin/look) should always follow the function (context/use-case) and not the other way around🎉
There is a principle or guideline in architecture and industrial design that goes like this: Form follows function. It basically states that the design, style of a building should follow from what that building is meant for.

One can tell if a person is a novice with CSS from the way they write CSS. Usually, the aim for the novice is to just quickly style the element on the screen without any regard for its’ context or use case. This leads to creating a convoluted mess of distinct CSS ruleset definitions that need to be overridden too many times later on.

A good example of this (as was have mentioned earlier) is defining a CSS margin directly on an element. This is an attempt to style the element in its’ current context or use case without consideration for other contexts and use cases where it may appear once again. Now, you have to override the margin that was initially set for the former context to fit this newer context.

Another bigger example of this is the excessive use of utility classes (especially in CSS frameworks like Tailwind) and its many drawbacks. You see back in the day, during the days of HTML 3.2 and early days of HTML 4, we had presentational tags and attributes that were very similar in concept to utility classes e.g. <font> , <center> , <s>, <i> , bgcolor , cellpadding , color , align , valign . These tags and attributes defined styles for each element much the way inline styles are defined for an element. The issue with this back then was the sheer amount of needless repetition to make elements look, feel and be partially or totally alike.

I fear that with frameworks like Tailwind, it will become hard and quite difficult to manage CSS definitions of sufficient scale and diverse use-case. Tailwind (and other like it) via utility-classes seem to have solved 4 problems with authoring CSS:

Duplicated Code — CSS styles that have been duplicated in error under 2 different selector names/titles
Dead Code — CSS styles that no longer referenced within markup because the markup that previously referenced them have been deleted or updated over time.
Faster Development — CSS styles are applied quickly to the markup without the need to alternate frequently between the CSS file and HTML (or JSX) file.
Inconsistent Nomenclature for selectors — CSS styles are packaged well enough to avoid team members stepping all over themselves.
Furthermore, the cost of using utility classes excessively is felt elsewhere right in the markup especially when you need to apply styles based on the parent element (or ancestor) state, loads of unused utility classes (if you don’t use a build tool to help you out) and nasty specificity issues. However, I do believe that utility classes have merit but only for things in CSS that do not change easily or often e.g. layout and structure.

Let me explain better with an analogy: You see, building a web page using CSS is a lot like building a house. There are parts of the house that do not change easily or often like the structural works (e.g. the foundation, beams/lintels). Then, there are the parts that do change easily (e.g. the windows, doors, roofing, paint job) and can also be changed often. While using CSS, layouts (and other things related to structure) don’t change easily or often.

However, colors, backgrounds, images, text and sizes can change easily and often.

Furthermore, If you want to name a class that has something to do with structuring content (width, height, display, min-height, max-width, e.t.c.) or setting up layout (flex, block, grid, margin, padding, border e.t.c). Don’t spend too much time trying to come up with a semantic class name, simply utilize utility class names (or utility classes) for this. Only when you wish to do things around visual formatting (e.g. outline, text-transform, font-weight, color, text-align, background-image, background-color, text-indent, font-style, text-decoration) and display styling or formatting (e.g. list-style-type, border-collapse, visibility) you should utilize clear semantic class names.

I believe that using proper Semantic classes with a mix of functional attribute selectors for the things that change easily and often while using Utility classes for the things that hardly change, will bring about better CSS authoring that we can rely on even at scale.

For example (CSS for a news website/PWA): 👇🏾👇🏾👇🏾👇🏾

/* Let's assume we were building a news website/PWA */



/* custom UTILITY CLASSES here for layout or just use [ TAILWIND CSS ] */

.display-block {
    display: block;
}

.display-flex {
    display: box;
    display: -ms-flex;
    display: -moz-flex;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
}

.flex-row-no-wrap,
.flex-column-no-wrap {
    -ms-flex-wrap: nowrap;
    flex-wrap: nowrap;
}

.flex-flexible {
  flex: auto; /* Override back to default */
  -webkit-flex: auto;
  -moz-flex: auto;
  -ms-flex: auto;
}

.flex-flexible.flex-fitted-\[\=125px\] {  
  box-sizing: border-box; /* 'box-sizing' doesn't work with 'flex-basis' */
  max-width: 11.875rem;
}

.flex-fixed-\[\=350px\] {
  flex-basis: 21.875rem;
}

.flex-fixed-collaspable {
  flex-shrink: 0;
  -webkit-flex-shrink: 0;
  min-width: 0;
}

.flex-fluid {
  flex-basis: auto; /* Override back to default */
  flex: 1;
  -webkit-flex: 1;
  -moz-flex: 1;
  -ms-flex: 1;
}

/* SEMANTIC CLASS SELECTORS + SEMANTIC (FUNCTIONAL) ATTRIBUTE SELECTORS 
   for colors & font-sizes */

/* The form here is a `message` with clear semantics, 
    the function here is the context or use case */

/**
 * The use cases (or contexts) here for the news website include:
 *
 * 1. messages that appear in the news headlines
 * 2. messages based on citations or quotes from people
 * 3. messages that appear as breaking news or other notifications
 * 4. comments on a news story as a type of message
 */

.message,
.message:focus, /* `:focus` now for older browsers */
.message:focus-visible {
  outline: none;
  border-color: transparent;
}

/* Seperate the function from form - news headline messages */
.news-headlines .message,
[role="marquee"] .message,
.news-headlines[role="marquee"] .message {
  font-size: 0.0625em;
  font-weight: 700;
  text-transform: uppercase;
  margin-top: 0.021rem;
  margin-bottom: 0.021rem; 
  color: white;
}

/* Seperate the function from form - citation messages */
.citation .message,
[role="note"] .message,
.citation[role="note"] .message,
blockquote .message {
  font-style: italic;
  text-transform: capitalize;
  text-decoration: underline;
  margin: auto;
  color: inherit;
}

/* Seperate the function from form - notification messages */
.breaking-news.notification .message,
.notification .message,
[role="alert"] .message,
.notification[role="alert"] .message {
  text-transform: none;
  font-weight: 400;
  background-color: white;
}

[role="alert"] .message[data-variant="success"] {
  color: green;
}

[role="alert"] .message[data-variant="error"] {
  color: red;
}

.notification .message {
  color: cyan;
}

/* Seperate the function from form - comment messages */
.comments [role="comment"][id^="convo-thread-"],
.comments .message,
.comments[id] .message,
.comments[id] .message[role="comment"][id^="convo-thread-"] {
  border-left: 2px solid brown;
  text-indent: 0.0055em;
  line-height: normal;
}

/* Hyper-links to external content */
a[target="_blank"] {
  display: inline-block;
  clear: both;
}

a[href][target="_blank"][rel="external"]:before {
  content: "\f400";
  float: left;
  margin-right: 4px;
}

@media (any-hover: hover) and (pointer: fine) {
  /* Reveal the button only on hover */
}

@media (any-hover: none) and (pointer: coarse) {
  /* Show the button all the time */
}
I do earnestly think that the future of CSS authoring on the web will require a healthy balance of Semantic and Utility classes/attributes which are arranged within CSS source files and folders by feature and not by type. This will reduce the occurrence of duplicated code and dead code and make it easier to find semantic styles when you need to change/modify them.

Always use context-aware styling 🤗
Tailwind CSS has made utility-classes not just a first-class citizens but also the one and only way to style web pages. This has led (sadly) to overusing utility classes (as stated earlier). This has gotten to the point where it becomes very hard to update/modify a long space-separated list of utility classes when needed without special code editor tricks to help you along.

Tailwind CSS is definitely quicker to write and easier to maintain (relative to past alternatives). But, it has its’ own specific maintenance issues.

BEM and OOCSS also have their issues too.

There’s one thing that makes CSS hard to write in the long-term (whether you use Tailwind CSS or not) and that is what i call “lengthy cascade override chains”. Excessively overriding styles across multiple selectors (within or outside media queries) can only lead to one thing — frustration and stress with writing CSS!

What is a cascade override chain ?

A cascade override chain is a situation where the CSS rules within two or more selector definitions that pertain to the same HTML tag/element (or set of HTML tags/elements) are overridden in turn as the rules of the cascade logic are applied by the browser.

Employing BEM leads to tight coupling (via class names), lots of duplication and bloat. There’s no emphasis on context and classes are used for all styling without paying proper attention to specificity from other selectors. However, BEM is really good at helping to avoid accidental side-effects based on the global cascade. Yet, the cost at which it does well to avoidd accidental side-effect is great.

On the other hand, OOCSS emphasises premature separation of concerns. It states that structure should be separated from skin. However, under what context should the separation happen to make it very meaningful ? That is a more important question. Still, OOCSS helps very well with very high levels of style reuse.

In addition to all these, BEM and OOCSS encourage lengthy override chains.

Which has led me to a novel way of writing CSS called “Context-Aware Styling”.

Context-aware styling works well with ITCSS (The only existing methodology in CSS that should still be in use even today).

Context-aware styling works by separating form from function which is a more mature way to separate concerns. It highlights the need to use utility classes only for things that hardly change (structure e.g. display, margin, width) and semantic class names + semantic and ARIA attribute selectors for thing that change a lot (skin e.g. color, background-color, font-style).

One of the reasons why people find the cascade in CSS annoying is because of how easy it is to abuse it. It takes a lot of experience and discipline to stop this abuse. I have found that when cascade override chains are kept to a minimum, much of the issues with writing CSS disappear. Don’t write more CSS to undo/override existing CSS if you can avoid it and still achieve the same result. Writing more CSS this way is very inefficient.

It is known that one of the most fundamental mistakes made by the earlier designers of CSS is that the cascade logic of CSS was made global and it’s not easy to scope it. However, the problem can be mitigated by dividing each part of the page into contexts. What you want to achieve by doing this is write less CSS overall with very little cascade overrides. This way of writing CSS eliminates much cognitive overhead and confusion as to why certain styles were applied (when debugging CSS) and which set of styles should have been finally applied but weren’t.

The best way to can we minimize the excessive occurrence of cascade overrides is by reducing and/or eliminating duplicate styles as much as possible and encapsulating style within contexts — styles never leak out of their context only within (i.e. styles only influence other contexts nested within and never other contexts outside).

These are some of the things that methodologies like BEM don’t promote.

- Avoid bloat in CSS by minimizing needless duplication.

- Prioritize appending CSS styles to extending (and likely overriding) CSS styles across multiple selectors except where a pseudo-class is involved for same-context state-based overrides or a semantic attribute selector is involved.

- CSS styles that leak into other contexts inside them can be scoped better with attribute selectors and the new cascade layers(@layer syntax).

I strongly believe that in 2023 going forward, you shouldn’t be using BEM (Block-Element-Modifier) or OOCSS (Object-oriented CSS) because of how flawed they are. This is not to say that BEM and OOCSS have zero value. BEM and OOCSS are the reason we have had Bootstrap CSS and Tailwind CSS in the first place because of the groundwork that was done with them.

Also, BEM and OOCSS do have very fantastic premise about not letting a given (dependent) selector depend on the defined styles of another selector while separating styles into very reusable and modular pieces each having their own function for structure and looks (or skin). In other words atomic CSS

This is all well and nice but premature (as stated earlier).

Methodologies like BEM create bloat in CSS code and create a situation where a HTML element is excessively bound to its context. (i.e. where a styled HTML element appears on a HTML document).

For example, lets’ consider the markup below (which is BEM themed):

<article class="article"> <!-- BLOCK -->
  <h3 class="article__header"><!-- BLOCK__ELEMENT -->
    An article title
  </h3>
  <div class="article__content"><!-- BLOCK__ELEMENT -->
    <p class="article__content--paragraph">
      Lorem ipsum ...
    </p>
    <blockquote class="article__content--quote" cite="https://quotes.com/one-liners">
      A quote from long time ago...
    </blockquote>
    <p class="article__content--highlighted-paragraph"><!-- BLOCK__ELEMENT--MODIFIER -->
      More lorem ipsum ...
    </p>
  </div>
</article>
The issue with the class name selectors here is that they facilitate a lot of coupling and bloat.

Like so:

.article__header {
  font-weight: bolder;
  color: lightgray;
  white-space: pre-wrap;
  word-break: break-word;
}

/* Luckily, this style ruleset makes use of applying margins indirectly */
.article .article__header {
  margin-bottom: 15px;
}

/* Firstly, this class name implicitly couples this rule-set to only one context */
/* This context is the "article" context or in this case, the "BLOCK" */
.article__content {
  letter-spacing: 0.4px;
  padding-top: 10px;
  padding-bottom: 20px;
  cursor: text;
  color: black;
}

/* Luckily, this style ruleset makes use of applying margins indirectly */
.article .article__content {
  margin-left: 15px;
}

.article .article__content--paragraph {
  margin-bottom: 30px;
}

.article__content--paragraph {
  font-weight: 300;
  line-height: 1.2;
}

.article__content--quote {
  font-weight: 100;
  font-style: italic;
  /* overriding "color" property inherited from `.article__content` */
  color: cyan;
}

/* Here comes the duplication case; enough of it andd you'll have bloat */
.article__content--highlighted-paragraph {
  /* I have to copy everything in `.article__content--paragraph` over here */
  font-weight: 300;
  line-height: 1.2;
  /* Then, append additional styles */
  text-shadow: 0 0 2px 4px rgba(0, 0, 0, 0.35);
  border: 2px solid cyan;
  outline: 1px solid rgba(0, 255, 255, 0.42);
  outline-offset: -2px;
  padding: 5px;
  color: cyan;
}
So, as you can see, the problem here with BEM (or even OOCSS) is that they both either do not emphasise the importance of context enough or over-emphasize it thereby tightly coupling a semantic class name to its’ context. They both treat context as either an unimportant aspect of styling web pages or of greater importance than it really is in the moment.

Context-aware styling applied to the markup above:

<article class="article"> <!-- OUTERMOST CONTEXT -->
  <h3 class="header"><!-- A `header` not an `articles' header` -->
    An article title
  </h3>
  <div class="content"><!-- Another NESTED CONTEXT -->
    <p class="paragraph"><!-- Not a NESTED CONTEXT because it has text -->
      Lorem ipsum ...
    </p>
    <blockquote class="quote" cite="https://quotes.com/one-liners">
      A quote from long time ago...
    </blockquote>
    <p class="paragraph" data-variant="highlighted">
      More lorem ipsum ...
    </p>
  </div>
</article>

<aside class="page-control" role="complementary" data-position="bottom">
  <h3 class="header"><!-- A `header` not an `page-controls' header` -->
    An article title
  </h3>
  <nav class="navigation"><!-- Another NESTED CONTEXT -->
    <menu>
      <li>Home</li>
    </menu>
  </nav>
</aside>
.page-control .header { /* `.header` class in one context (no overrides) */
  color: brown;
  margin-bottom: 10px;
}

.article .header { /* `.header` class in another context (no overrides) */
  font-weight: bolder;
  color: lightgray;
  margin-bottom: 15px;
}

/* The `.article` is the outermost context */
/* There should be a maximum of only 2 selectors (one parent and one child) */
.article .content {
  letter-spacing: 0.4px;
  padding-top: 10px;
  padding-bottom: 20px;
  cursor: text;
  color: black;
  margin-left: 15px;
}

/*. The `.content` is a nested context nested within `.article` */
/* There should be a maximum of only 2 selectors (one parent and one child) */
.content .paragraph {
  margin-bottom: 30px;
  font-weight: 300;
  line-height: 1.2;
}

.content .quote {
  font-weight: 100;
  font-style: italic;
  /* overriding "color" property inherited from `.content` */
  color: cyan;
}

/* No duplication and no bloat! */
.content .paragraph[data-variant="highlighted"] {
  /* Just, append additional styles */
  text-shadow: 0 0 2px 4px rgba(0, 0, 0, 0.35);
  border: 2px solid cyan;
  outline: 1px solid rgba(0, 255, 255, 0.42);
  outline-offset: -2px;
  padding: 5px;
  /* overriding "color" property inherited from `.content` */
  color: cyan;
}
Also, they both don’t properly emphasize separating form from function which is the most important line of separation that matters most other than separating structure from look (or skin). The “Block” in BEM is the form but its’ function is not highlighted properly in context. Neither the “Element” nor the “Modifier” emphasize function nor functional distinction as well.

Therefore, instead of doing this:

<button class="button">
 Normal button
</button>

<button class="button button--state-success">
 Success button
</button>

<button class="button button--state-danger">
 Danger button
</button>

<button class="button" type="submit">
 Send
</button>
.button {
  display: inline-block;
  border-radius: 3px;
  padding: 7px 12px;
  border: 1px solid #D5D5D5;
  background-image: linear-gradient(#EEEEEE, #DDDDDD);
  font: 700 13px/18px Helvetica, arial;
}

/* @HINT: Lack of specificity score hierarchy */
.button--state-success {
  color: #FFFFFF;
  background: #569E3D linear-gradient(#79D858, #569E3D) repeat-x;
  border-color: #4A993E;
}

/* @HINT: Lack of specificity score hierarchy */
.button--state-danger {
  color: #990000;
}
It’s better and more beneficial to do this: 👇🏾👇🏾

<button class="button">
 Normal button
</button>

<button class="button" data-variant="success">
 Success button
</button>

<button class="button" data-variant="danger">
 Danger button
</button>

<button class="button" type="submit">
 Send
</button>
.button {
  display: inline-block;
  border-radius: 3px;
  padding: 7px 12px;
  border: 1px solid #D5D5D5;
  background-image: linear-gradient(#EEEEEE, #DDDDDD);
  font: 700 13px/18px Helvetica, arial;
}

/* @HINT: Presence of specificity score hierarchy: atrribute + class selectors */
.button[data-variant="success"] {
  color: #FFFFFF;
  background: #569E3D linear-gradient(#79D858, #569E3D) repeat-x;
  border-color: #4A993E;
}

/* @HINT: Presence of specificity score hierarchy: atrribute + class selectors */
.button[data-variant="danger"] {
  color: #990000;
}

.call-to-action-context [type="submit"]:only-child,
.call-to-action-context a[href]:first-child:last-child,
[role="form"] [type="submit"]:only-child {
  display: block;
  width: 100%;
  box-sizing: border-box;
}
Take a design system for example; what is the most obvious thing about how it can be implemented wrongly ? Well, the lack of context (i.e no atoms, molecules, organisms e.t.c). You see; the lack of context makes design systems hard to realize in the most unreliable manner. If you do not know where each component in a design system goes on a fully-detailed web page and how it fits there, styling the design system component on its’ own will only lead to problems later on.

When using context-aware styling in building your design systems, atoms and molecules should have minimal styling based on structure but have no styling at all based on look (e.g. color, borders, outlines box-shadow, border-radius). In other words, they should be “headless components”.

This is why context matters greatly when writing CSS. As i said earlier that context is the function (separated from the form).

There’s often a very potent concern that limiting the scope of a selector to a particular DOM subtree doesn’t guarantee that it won’t unintentionally affect elements within the same subtree. This issue is well addressed by Context-aware styling.

See below:

h1 {
  outline: none;
}

/* Utility classes are used in context-aware styling for things that hardly changes */
/* Things like the display, margin, position will hardly change */

.display-flex {
  display: box;
  display: -ms-flex;
  display: -moz-flex;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
}

.flex-fixed\:\[\=190px\] {
  flex-basis: 11.875rem;
}

.flex-fluid {
  flex-basis: auto; /* Override back to default */
  flex: 1;
  -webkit-flex: 1;
  -moz-flex: 1;
  -ms-flex: 1;
}

/* For context-aware styling: there should be a maximum of 2 selectors */
/* There should be a maximum of only 2 selectors (one parent and one child) */

/* When there are multiple selectors, they are arranged in order of decreasing specificity */

[class*="framed-widget"][data-style-priority][role="presentation"] h1,
[class*="framed-widget"][data-style-priority] h1,
.framed-widget[role="presentation"] h1,
[role="presentation"][data-style-priority] h1,
[role="presentation"] h1,
.framed-widget[data-style-priority] h1,
.framed-widget h1 {
  color: brown;
  text-align: right;
  border-bottom: 1px solid #000000;
}

[class*="sidebar"][role="complementary"][data-style-priority] h1,
[class*="sidebar"][data-style-priority] h1,
.sidebar[role="complementary"] h1,
[role="complementary"][data-style-priority] h1,
[role="complementary"] h1,
.sidebar[data-style-priority] h1,
.sidebar h1 {
  color: white;
  text-align: center;
  border: none;
}
Notice the bottom border doesn’t show up in the inner h1 :

<h1>One CSS Example</h1>

<aside class="display-flex flex-fluid" role="complementary">
  <h1>Another CSS Example</h1>
  <div class="display-flex flex-fixed:[=190px]" role="presentation" data-style-priority>
   <h1>The Last CSS Inner Example</h1>
  </div>
</aside>
As you see above, the only trade-off is the stack of selectors arranged in order of decreasing specificity.

Also as above, Context-aware styling makes it very easier for us to also manage specificity within the same nested context (or same DOM subtree) without letting things get out of hand. We have defined a data-style-priortity attribute that controls what styles are prioritized within the same nested contexts.

This data-style-priortity attribute works irrespective of CSS source order.

By arranging the selectors attached to each rule-set in order of decreasing specificity, we have ensured that CSS source order will have no effect on when and how styles get prioritized.

Only now does the bottom border show up in both h1 within the <aside> tag. See below:

<h1>One CSS Example</h1>

<aside class="display-flex flex-fluid" role="complementary" data-style-priority>
  <h1>Another CSS Example</h1>
  <div class="display-flex flex-fixed:[=190px]" role="presentation">
   <h1>The Last CSS Inner Example</h1>
  </div>
</aside>
Context-aware styling works well with only one existing methodology: ITCSS. This allows CSS to be written in order of increasing specificity from top to bottom but with some interleaving allowed. So, with the likes of Cascade layers (@layer), Container (@container) queries and now context-aware styling, ITCSS will now become a lot more nuanced which is a good thing.

By focusing too much on context and specificity flatness (the way BEM does), one can introduce problems like low reusability of styles, and too much structure based on the naming of selectors. It’s also possible to easily create lengthy cascade override chains that are not state-based as explained earlier.

Context-aware styling solves all these by only emphasizing context in a reasonably balanced way.

Beware the issue of margin-collapse 😟
Margin collapse is an occurrence that makes it difficult to have 2 block-level elements with margins sit side-by-side in such a way that the distance from each other is exactly the sum of the margins set on each of them. For more info on this see this article. It’s also highlighted in the CSS 2.1 specifications too.

Conclusion
If you look closely, you’d notice that each of the points i gave above are very related to one another. These points will help you write better CSS with much less headaches.

Remember that context-aware styles emphasizes encapsulation of style definitions per selector (in context) and reduces the occurrence of override chains.

I hope that you learnt a whole lot from these tips.

Finally, i am not in any way saying don’t use CSS frameworks and component libraries. I encourage you to use them but keep these tips in mind as you do so.

Have fun writing great and scalable CSS! 👍🏾



