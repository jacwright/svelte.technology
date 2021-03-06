---
title: Scoped styles
---

One of Svelte's key tenets is that components should be self-contained and reusable in different contexts. Because of that, it has a mechanism for *scoping* your CSS, so that you don't accidentally clobber other selectors on the page.

### Adding styles

Your component template can have a `<style>` tag, like so:

```html
<!--{ title: 'Scoped styles' }-->
<div class='foo'>
	Big red Comic Sans
</div>

<style>
	.foo {
		color: red;
		font-size: 2em;
		font-family: 'Comic Sans MS';
	}
</style>
```


### How it works

Open the example above in the REPL and inspect the element to see what has happened – Svelte has added a `svelte-[uniqueid]` class to the element, and transformed the CSS selector accordingly. Since no other element on the page can share that selector, anything else on the page with `class="foo"` will be unaffected by our styles.

This is vastly simpler than achieving the same effect via [Shadow DOM](http://caniuse.com/#search=shadow%20dom) and works everywhere without polyfills.

> Svelte will add a `<style>` tag to the page containing your scoped styles. Dynamically adding styles may be impossible if your site has a [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP). If that's the case, you can use scoped styles by [server-rendering your CSS](guide#rendering-css) and using the `css: false` compiler option (or `--no-css` with the CLI).


### Cascading rules

By default, the usual cascading mechanism still applies – any global `.foo` styles would still be applied, and if our template had [nested components](guide#nested-components) with `class="foo"` elements, they would inherit our styles.

If the `cascade: false` option is passed to the compiler, styles will *only* apply to the current component, unless you opt in to cascading with the `:global(...)` modifier:

<!-- TODO `cascade: false` in the REPL -->

```html
<!-- { repl: false } -->
<div>
	<Widget/>
</div>

<style>
	p {
		/* this block will be disregarded, since
		   there are no <p> elements here */
		color: red;
	}

	div :global(p) {
		/* this block will be applied to any <p> elements
		   inside the <div>, i.e. in <Widget> */
		font-weight: bold;
	}
</style>

<script>
	import Widget from './Widget.html';

	export default {
		components: { Widget }
	};
</script>
```

The `cascade: false` behaviour is recommended, and will be switched on by default in future versions of Svelte.

> Scoped styles are *not* dynamic – they are shared between all instances of a component. In other words you can't use `{{tags}}` inside your CSS.


### Unused style removal

If you're using the recommended `cascade: false` option, Svelte will identify and remove any styles that it can guarantee are not being used in your app. It will also emit a warning so that you can remove them from the source.


### Special selectors

If you have a [ref](guide#refs) on an element, you can use this as a CSS selector. An element with `ref:foo` can be styled with `ref:foo { color: whatever; }`. The `ref:*` selector has the same specificity as a class or attribute selector.


```html
<!--{ title: 'Styling with refs' }-->
<div ref:foo>
	yeah!
</div>

<style>
	ref:foo {
		color: magenta;
		font-size: 5em;
	}
</style>
```
