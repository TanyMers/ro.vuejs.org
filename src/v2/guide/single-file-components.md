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

### Cum rămâne cu separarea responsabilităților?

Un lucru important este de remarcat faptul că **separarea responsabilităților nu este egală cu separarea tipurilor de fișiere.** În dezvoltarea UI modernă, am constatat că, în loc să împartă codul în trei straturi uriașe care se interpun între ele, mai mult sens pentru a le împărți în componente cu cuplaj liber și pentru a le compune. În interiorul unei componente, șablonul, logica și stilurile sale sunt în mod inerent cuplate, iar colocarea acestora face de fapt componenta mai coerentă și mai ușor de întreținut.

Chiar dacă nu vă place ideea Componentelor cu un singur fișier, puteți totuși să utilizați funcțiile de reîncărcare-hot și de precompilare prin separarea JavaScript și CSS în fișiere separate:


``` html
<!-- my-component.vue -->
<template>
  <div>Acest lucru va fi precompilat</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```

## Noțiuni de Bază

### Exemplu Sandbox

Dacă doriți să vă aruncați înainte și să începeți să jucați cu componente cu un singur fișier, verificați [această aplicație simplă 0todo](https://codesandbox.io/s/o29j95wx9) pe CodeSandbox.

### Pentru Utilizatorii Noi în Modulele Sistemelor în Construirea JavaScript

Cu componentele `.vue`, intrăm în domeniul aplicațiilor JavaScript avansate. Asta înseamnă că trebuie să învățați să folosiți câteva instrumente suplimentare, dacă nu ați făcut-o deja:

- **Node Package Manager (NPM)**: Citiți ghidul [Getting Started](https://docs.npmjs.com/getting-started/what-is-npm) prin secțiunea _10: Dezinstalarea pachetelor globale_.

- **JavaScript-ul modern cu ES2015/16**: Citiți prin [Ghidul de instruire ES2015](https://babeljs.io/docs/learn-es2015/) al lui Babel. Nu trebuie să memorați fiecare caracteristică acum, dar păstrați această pagină ca referință la care vă puteți întoarce.

După ce ați luat o zi să vă aruncați cu capul în aceste resurse, vă recomandăm să verificați șablonul [webpack](https://vuejs-templates.github.io/webpack). Urmați instrucțiunile și ar trebui să aveți un proiect Vue cu componente `.vue`, ES2015 și reîncărcare în cel mai scurt timp!

Pentru a afla mai multe despre Webpack-ul în sine, consultați [documentele oficiale](https://webpack.js.org/configuration/) și [Webpack Academy](https://webpack.academy/p/the-core-concepts). În Webpack, fiecare fișier poate fi transformat de un "încărcător" înainte de a fi inclus în pachet, iar Vue oferă pluginul [vue-loader](https://vue-loader.vuejs.org) pentru a traduce un singur fișier (`.vue`).

### Pentru Utilizatorii Avansați

Indiferent dacă preferați Webpack sau Browserify, am documentat șabloane pentru proiecte simple și mai complexe. Vă recomandăm să răsfoiți [github.com/vuejs-templates](https://github.com/vuejs-templates), alegeți un șablon potrivit pentru dvs., apoi urmați instrucțiunile din README pentru a genera un nou proiect cu [vue- cli](https://github.com/vuejs/vue-cli).
