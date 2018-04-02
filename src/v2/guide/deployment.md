---
title: Implementarea Producției
type: guide
order: 401
---

## Activați Modul de Producție

În timpul dezvoltării, Vue oferă multe avertismente pentru a vă ajuta cu erorile și capcanele comune. Cu toate acestea, aceste șiruri de avertizare devin inutile în producție și umflă dimensiunea greutăților încărcate de aplicația dvs. În plus, unele dintre aceste verificări de avertizare au costuri mici de executare care pot fi evitate în modul de producție.

### Fără Instrumente de Construcție

Dacă utilizați construirea completă, adică direct incluzând Vue printr-o etichetă de script fără un instrument de construire, asigurați-vă că utilizați versiunea minificată (`vue.min.js`) pentru producție. Ambele versiuni pot fi găsite în [Installation guide](installation.html#Direct-lt-script-gt-Include).

### cu Instrumente de Construcție

Când se utilizează un instrument de construcție cum ar fi Webpack sau Browserify, modul de producție va fi determinat de `process.env.NODE_ENV` în codul sursă Vue și va fi în mod implicit în modul de dezvoltare. Ambele instrumente de construire oferă modalități de suprascriere a acestei variabile pentru a permite modul de producție al Vue, iar avertismentele vor fi eliminate de minifiere în timpul construcției. Toate șabloanele `vue-cli` au aceste preconfigurate pentru dvs., dar ar fi benefic să știți cum se face:

#### Webpack

Utilizați [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) de la Webpack pentru a indica un mediu de producție, astfel încât blocurile de avertizare să poată fi abandonate automat de UglifyJS în timpul procesului de minificare. Exemplu config:

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: '"production"'
      }
    })
  ]
}
```

#### Browserify

- Rulați comanda de îmbinare cu variabila actuală de mediu "NODE_ENV" setată la `"producție"`. Acest lucru spune `vueify` pentru a evita includerea reîncărcării-hot și a codului de dezvoltare.

- Aplicați o transformare globală [envify](https://github.com/hughsk/envify) în pachetul dvs. Aceasta permite minifierului să elimine toate avertismentele din codul sursă al Vue înfășurat în blocuri condiționale variabile env. De exemplu:

  ``` bash
  NODE_ENV=production browserify -g envify -e main.js | uglifyjs -c -m > build.js
  ```

- Or, using [envify](https://github.com/hughsk/envify) with Gulp:

  ``` js
  // Utilizați modulul personalizat pentru a specifica variabilele de mediu
  var envify = require('envify/custom')

  browserify(browserifyOptions)
    .transform(vueify),
    .transform(
      // Obligatoriu pentru procesarea fișierelor node_modules
      { global: true },
      envify({ NODE_ENV: 'production' })
    )
    .bundle()
  ```

#### Rollup

Utilizează [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace):

``` js
const replace = require('rollup-plugin-replace')

rollup({
  // ...
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify( 'production' )
    })
  ]
}).then(...)
```

## Șabloane de Precompilare

Atunci când se utilizează șabloane in-DOM sau șiruri de șabloane în JavaScript, compilația funcțiilor șablon-redare se efectuează în zbor. De obicei, acest lucru este destul de rapid în cele mai multe cazuri, dar este cel mai bine evitat dacă aplicația dvs. este sensibilă la performanță.

Cea mai simplă metodă de precompilare a șabloanelor este utilizarea [Componentelor Single-File](single-file-components.html) - setările asociate de construire realizează automat o precompilare pentru dvs., astfel încât codul construit conține funcțiile de redare deja compilate, în loc de șabloane de șiruri neprelucrate.

Dacă utilizați Webpack și preferați să separați JavaScript și fișierele de șablon, puteți utiliza [vue-template-loader](https://github.com/ktsn/vue-template-loader), care transformă, de asemenea, fișierele șablon în funcții de randare JavaScript în timpul etapei de construire.


## Extragerea Сomponentei CSS

Când se utilizează Componentele cu un singur fișier, componentele CSS din interiorul acestora sunt injectate dinamic ca etichete `<style>` prin JavaScript. Acest lucru are un cost redus de execuție și, dacă utilizați randarea de pe server, acesta va provoca o "blițare a conținutului neuniform". Extragerea CSS în toate componentele în același fișier va evita aceste probleme și va duce, de asemenea, la îmbunătățirea miniaturilor și cache-urilor CSS.

Consultați documentațiile respectivului instrument de construcție pentru a vedea cum se face:

- [Webpack + vue-loader](https://vue-loader.vuejs.org/en/configurations/extract-css.html) (șablonul webpack `vue-cli` are acestă preconfigurare)
- [Browserify + vueify](https://github.com/vuejs/vueify#css-extraction)
- [Rollup + rollup-plugin-vue](https://vuejs.github.io/rollup-plugin-vue/#/en/2.3/?id=custom-handler)

## Tracking Runtime Errors

If a runtime error occurs during a component's render, it will be passed to the global `Vue.config.errorHandler` config function if it has been set. It might be a good idea to leverage this hook together with an error-tracking service like [Sentry](https://sentry.io), which provides [an official integration](https://sentry.io/for/vue/) for Vue.
