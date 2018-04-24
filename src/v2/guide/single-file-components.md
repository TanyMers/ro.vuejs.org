---
title: Componentele unui Singur Fișier
type: guide
order: 402
---

## Introducere

În multe proiecte Vue, componentele globale vor fi definite folosind `Vue.component`, urmat de `new Vue({ el: '#container' })` pentru a viza un element container în corpul fiecărei pagini.

Acest lucru poate funcționa foarte bine pentru proiectele de dimensiuni mici și mijlocii, în care JavaScript este utilizat numai pentru a îmbunătăți anumite viziuni. Cu toate acestea, în proiecte mai complexe sau când interfața dvs. este condusă în întregime de JavaScript, aceste dezavantaje devin vizibile:

- **Definiții globale** forțați nume unice pentru fiecare componentă
- **Șabloanele de șir** nu au o evidențiere a sintaxei și necesită tăieturi(slashes) urâte pentru HTML multiline
- **Nu are suport CSS** înseamnă că, în timp ce HTML și JavaScript sunt modulate în componente, CSS este evitat în mod vizibil
- **Nici un pas de construcție** nu ne limitează la HTML și ES5 JavaScript, mai degrabă decât preprocesoare ca Pug (fostul Jade) și Babel

Toate acestea sunt rezolvate de către **componentele cu un singur fișier** cu o extensie `.vue`, făcută posibilă cu instrumente de construire cum ar fi Webpack sau Browserify.

Iată un exemplu de fișier pe care îl vom numi `Hello.vue`:

<img src="/images/vue-component.png" style="display: block; margin: 30px auto;">

Acum primim:

- [Sintaxă completă evidențiată](https://github.com/vuejs/awesome-vue#source-code-editing)
- [Modulele CommonJS](https://webpack.js.org/concepts/modules/#what-is-a-webpack-module)
- [Componenta-scoped CSS](https://vue-loader.vuejs.org/en/features/scoped-css.html)

Așa cum am promis, putem folosi și preprocesoare cum ar fi Pug, Babel(cu modulele ES2015) și Stylus pentru componente mai curate și mai bogate în caracteristici.

<img src="/images/vue-component-with-preprocessors.png" style="display: block; margin: 30px auto;">

Aceste limbi specifice sunt doar exemple. Ai putea folosi cu ușurință și Bublé, TypeScript, SCSS, PostCSS - sau orice alte preprocesoare care te ajută să fii productiv. Dacă folosiți Webpack cu `vue-loader`, acesta are și suport de primă clasă pentru modulele CSS.


### What About Separation of Concerns?

One important thing to note is that **separation of concerns is not equal to separation of file types.** In modern UI development, we have found that instead of dividing the codebase into three huge layers that interweaves with one another, it makes much more sense to divide them into loosely-coupled components and compose them. Inside a component, its template, logic and styles are inherently coupled, and collocating them actually makes the component more cohesive and maintainable.

Even if you don't like the idea of Single-File Components, you can still leverage its hot-reloading and pre-compilation features by separating your JavaScript and CSS into separate files:

``` html
<!-- my-component.vue -->
<template>
  <div>This will be pre-compiled</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```

## Getting Started

### Example Sandbox

If you want to dive right in and start playing with single-file components, check out [this simple todo app](https://codesandbox.io/s/o29j95wx9) on CodeSandbox.

### For Users New to Module Build Systems in JavaScript

With `.vue` components, we're entering the realm of advanced JavaScript applications. That means learning to use a few additional tools if you haven't already:

- **Node Package Manager (NPM)**: Read the [Getting Started guide](https://docs.npmjs.com/getting-started/what-is-npm) through section _10: Uninstalling global packages_.

- **Modern JavaScript with ES2015/16**: Read through Babel's [Learn ES2015 guide](https://babeljs.io/docs/learn-es2015/). You don't have to memorize every feature right now, but keep this page as a reference you can come back to.

After you've taken a day to dive into these resources, we recommend checking out the [webpack](https://vuejs-templates.github.io/webpack) template. Follow the instructions and you should have a Vue project with `.vue` components, ES2015, and hot-reloading in no time!

To learn more about Webpack itself, check out [their official docs](https://webpack.js.org/configuration/) and [Webpack Academy](https://webpack.academy/p/the-core-concepts). In Webpack, each file can be transformed by a "loader" before being included in the bundle, and Vue offers the [vue-loader](https://vue-loader.vuejs.org) plugin to translate single-file (`.vue`) components.

### For Advanced Users

Whether you prefer Webpack or Browserify, we have documented templates for both simple and more complex projects. We recommend browsing [github.com/vuejs-templates](https://github.com/vuejs-templates), picking a template that's right for you, then following the instructions in the README to generate a new project with [vue-cli](https://github.com/vuejs/vue-cli).
