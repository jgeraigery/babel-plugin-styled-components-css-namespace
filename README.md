# @quickbase/babel-plugin-styled-components-css-namespace

## Getting Started

1.  Add the plugin with `yarn add @quickbaseoss/babel-plugin-styled-components-css-namespace` or `npm install @quickbaseoss/babel-plugin-styled-components-css-namespace`
1.  Include the plugin in your babel configuration.

```json
"babel": {
  "plugins": [
    "@quickbaseoss/babel-plugin-styled-components-css-namespace"
  ]
}
```

If you are also using [babel-plugin-styled-components](https://github.com/styled-components/babel-plugin-styled-components), you must place `styled-components-css-namespace` **before** `styled-components`.

```json
"babel": {
  "plugins": [
    "@quickbaseoss/babel-plugin-styled-components-css-namespace",
    "styled-components"
  ]
}
```

## Options

### Default

Without adding options, this plugin will duplicate the class name generated by `styled-components` as suggested in [this issue](https://github.com/styled-components/styled-components/issues/613).

```css
/* output */
.c0.c0 {
  background-color: blue;
}
```

### Increasing Specificity

A common scenario when integrating styled-components into existing projects is fighting against extremely specific legacy CSS selectors such as `#someId .grossly .nested section input {/* styles */}`.

To increase the specificity that this plugin adds, you can leverage the [recommended approach from the styled-components docs](https://www.styled-components.com/docs/faqs#how-can-i-override-styles-with-higher-specificity). Add the appropriate number of `&` selectors equal to the desired selector duplication as the `cssNamespace` option (the default behavior is x2 `{"cssNamespace": "&&"}`).

```json
{
  "plugins": [
    [
      "@quickbaseoss/babel-plugin-styled-components-css-namespace",
      {"cssNamespace": "&&&"}
    ],
    "babel-plugin-styled-components"
  ]
}
```

```css
/* output */
.c0.c0.c0 {
  background-color: blue;
}
```

### Custom Namespace

You can provide a `cssNamespace` to use instead of duplicating the class name. Remember to include a DOM element with that class that wraps the styled-component. The `cssNamespace` takes the form of the selector you want to use to wrap all of your styles with.

```json
"babel": {
  "plugins": [
    ["@quickbaseoss/babel-plugin-styled-components-css-namespace", {"cssNamespace": ".specific .moreSpecific .reallySpecific"}],
    "styled-components"
  ]
}
```

```css
/* output */
.specific .moreSpecific .reallySpecific .c0 {
  background-color: blue;
}
```

_where .c0 is the class added by styled-components to the element_

## Upgrade to version 1.0.0

Note that `rawCssNamespace` was dropped in favor of the single `cssNamespace` option. Additionally, support for an array of selectors was dropped as well. Update any references to `rawCssNamespace` with `cssNamespace`.

If you were already using `cssNamespace`, update your configuration to use a css selector rather than an array of classes. E.g., `cssNamespace: 'moreSpecific'` should be `cssNamespace: '.moreSpecific'` and `cssNamespace: ['specific', 'verySpecific']` should be `cssNamespace: '.specific .verySpecific'`.

## The Problem

[styled-components](https://github.com/QuickBase/styled-components) is an awesome library for css-in-js and feels like a natural combination of React and CSS. It is easy to use and produces css instead of inline styles.

However, if you are trying to gradually introduce [styled-components](https://github.com/QuickBase/styled-components) into a legacy website that might not have the best CSS, the legacy styles may bleed into your styled-components because they have more specificity than the single class styled-components.

## The Solution

This plugin will automatically add additional css namespaces or duplicated classes to the selectors produced by styled components effectively creating a wall between the legacy css code and your new shiny styled components.

![monty-python-castle](https://media.giphy.com/media/12TIvbgMTrGhhu/giphy.gif)

## Styling frameworks

This plugin was built for [Styled Components](https://www.styled-components.com/); however, since initially creating it, we at Quick Base have switched to [Emotion](https://emotion.sh/). It works as an alternative to the [stylis extra scope plugin](https://github.com/Andarist/stylis-plugin-extra-scope) which requires creating your own instance of stylis.

## Developing

1.  Clone the repo with `git clone https://github.com/QuickBase/babel-plugin-styled-components-css-namespace.git`
1.  `yarn install` (prefer `yarn` although `npm` should work as well)
1.  `yarn test` to run the tests

## Publishing

When we are ready to release a new version, one of the admins needs to run the following commands to publish the new version to npm.
We probably need to invest in a better deploy and semver management system. Interested? See [this issue](https://github.com/QuickBase/babel-plugin-styled-components-css-namespace/issues/9).

- If needed, open a new PR to update the version in the [package.json](https://github.com/QuickBase/babel-plugin-styled-components-css-namespace/blob/master/package.json)
- Copy the commit hash from the [commit log](https://github.com/QuickBase/babel-plugin-styled-components-css-namespace/commits/master)
- Run `git tag -a {version} {commit_hash}`. For example: `git tag -a 0.0.9 abf3123`
- In the editor, add a message about the changes in this version and save
- Push the tag to GitHub with `git push --follow-tags`
- Travis CI will build and publish the new version to npm

## Acknowledgements

Open source software is a group effort. This version of the plugin was heavily inspired by [a fork](https://github.com/TrevorBurnham/babel-plugin-namespace-styled-components) of the original plugin from @TrevorBurnham.

We also would like to thank some of our contributors who helped solve some tough issues with the previous iteration of this plugin:

- @daniloster
- @dan-kez
- @prevostc
- @danielhusar
