# CSS media-queries.md - responsive first.
Below: This will give you a responsive two-column layout in which mobile styles are displayed when the 
device width hits 480px or smaller. The problem, however, is that this is not a mobile-first 
approach to coding. If you’d rather build an optimal design for smartphones, and then have your 
emails adapt and expand for larger screens, the min-width media query is a better option.
##
<pre>
&lt;style&gt;
  :root {
    color-scheme: light dark;
    supported-color-schemes: light dark;
    font-size: 16px;
    font-color: #222;
  }

  h2 {
    margin: 0;
  }

  .column {
    width: 50%;
    display: table-cell;
    padding: .5em;
  }

@media screen and (max-width:480px) {
  .column {
    display: block !important;
    width: 100% !important;
  }

  .column:last-child {
    margin-top: 2em !important;
  }
}
&lt;/style&gt;
</pre>
##
So, if an email recipient views your campaign on a smartphone with a display less than 600 pixels 
wide, it will not show the defined styles. In this situation, you code for the smaller screens 
first and used the media query to define desktop styles.

Below: Here’s some sample email code showing how you’d use a min-width media query:
##
<pre>
&lt;style&gt;
  :root {
    color-scheme: light dark;
    supported-color-schemes: light dark;
    font-size: 16px;
    font-color: #222;
  }

  h2 {
    margin: 0;
  }

  .column:last-child {
    margin-top: 2em;
  }

@media screen and (min-width:480px) {
  .column {
    width: 50%;
    display: table-cell;
    padding: .5em;
  }
  .column:last-child {
    margin-top: 0;
  }
}
&lt;/style&gt;
## Combining min-width and max-width media queries
##
@media only screen and (max-width: 600px) and (min-width: 400px)  {...}
##
@media only screen and (max-width: 600px) {...}
##
<pre>
@media screen and (orientation: landscape) {
  body::after {
    content: "Landscape";
  }
}

@media screen and (orientation: portrait) {
  body::after {
    content: "Portrait";
  }
}

body {
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100vw;
  font-size: 10vmax;
}
</pre>
##
<pre>
@media all and (orientation: landscape) {
   /* Styles for landscape mode. */
}

@media all and (orientation: portrait) {
   /* Styles for portrait mode. */
}
</pre>
##
<pre>
@media only screen and ( min-width: 50em ) {
  .open-book {
    margin: 1em;
    position: relative;
  }
  .open-book:before {
    background-color: #8b4513;  /* dark orange */
    border-radius: 0.25em;
    bottom: -1em;
    content: '';
    left: -1em;
    position: absolute;
    right: -1em;
    top: -1em;
    z-index: -1;
  }
  .open-book:after {
    background: linear-gradient(to right, transparent 0%,rgba(0,0,0,0.2) 46%,rgba(0,0,0,0.5) 49%,rgba(0,0,0,0.6) 50%,rgba(0,0,0,0.5) 51%,rgba(0,0,0,0.2) 52%,transparent 100%);
    bottom: -1em;
    content: '';
    left: 50%;
    position: absolute;
    top: -1em;
    transform: translate(-50%,0);
    width: 4em;
    z-index: 1;
  }
  .open-book > * {
    column-count: 2;
    column-gap: 6em;
    position: relative;
    z-index: 1;
  }

  /* open-book Header/Footer */
  .open-book header:before,
  .open-book header:after,
  .open-book footer:before,
  .open-book footer:after {
    background: #fff;
    border-radius: 25%;
    content: '';
    height: 2em;
    position: absolute;
    z-index: -1;
    width: calc(50% + 2em);
  }
  .open-book header:before,
  .open-book footer:before,
  .open-book footer:after {
    border-top-left-radius: 0;
  }
  .open-book header:after,
  .open-book footer:before,
  .open-book footer:after {
      border-top-right-radius: 0;
  }
  .open-book header:before,
  .open-book header:after,
  .open-book footer:after {
    border-bottom-right-radius: 0;
  }
  .open-book header:before,
  .open-book header:after,
  .open-book footer:before {
    border-bottom-left-radius: 0;
  }
  .open-book header:before,
  .open-book header:after {
    top: -2.65em;
  }
  .open-book header:before,
  .open-book footer:before {
    right: 50%;
  }
  .open-book header:before {
    transform: rotate(-2deg);
  }
  .open-book header:after,
  .open-book footer:after {
    left: 50%;
  }
  .open-book header:after {
    transform: rotate(2deg);
  }
  .open-book footer:before,
  .open-book footer:after {
    bottom: -2.65em;
  }
  .open-book footer:before {
    transform: rotate(2deg);
  }
  .open-book footer:after {
    transform: rotate(-2deg);
  }
  .open-book header > *:last-child,
  .open-book footer > *:last-child {
    text-align: right;
  }
  .open-book footer #page-numbers {
    display: block;
  }
  
  /* open-book Chapter Title */
  .open-book .chapter-title {
    font-size: 2em;  /* 3em; */
  }
  .open-book .chapter-title:before,
  .open-book .chapter-title:after {
    height: 0.125em;
  }
  
  /* open-book Body Copy */
  .open-book article p {
    text-indent: 1.25em;  /* 2em;, 3em; */
  }
  .open-book article > ul,
  .open-book article > ol {
    padding-left: 3em;  /* 4em; */
  }
}  /* end of @media only screen and ( min-width: 50em ) */
</pre>

