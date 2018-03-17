---
title: Componentele
type: guide
order: 11
---

## Ce sunt Componentele?

Componentele sunt una dintre cele mai puternice caracteristici ale Vue. Ele vă ajută să extindeți elementele de bază HTML pentru a încapsula codul reutilizabil. La un nivel înalt, componentele sunt elemente personalizate pe care compilatorul Vue le atașează comportamentului. În unele cazuri, ele pot apărea, de asemenea, ca un element HTML nativ extins cu atributul special `is`.

## Utilizarea Componentelor

### Înregistrare Globală

În secțiunile anterioare am învățat că putem crea o nouă instanță Vue cu:

``` js
new Vue({
  el: '#some-element',
  // opțiuni
})
```

Pentru a înregistra o componentă globală, puteți folosi `Vue.component(tagName, options)`. De exemplu:

``` js
Vue.component('my-component', {
  // opțiuni
})
```

<p class="tip">Rețineți că Vue nu aplică [regulile W3C](https://www.w3.org/TR/custom-elements/#concepts) pentru numele de etichete personalizate (cu litere mici, trebuie să conțină o cratimă), deși urmarea acestor convenții sunt considerate practici bune.</p>

Odată înregistrat, o componentă poate fi utilizată într-un șablon al unui exemplu ca element personalizat, `<my-component></ my-component>`. Asigurați-vă că componenta este înregistrată **înainte de** rădăcina instanței Vue. Mai jos găsiți exemplul complet:

``` html
<div id="example">
  <my-component></my-component>
</div>
```

``` js
// înregistrează
Vue.component('my-component', {
  template: '<div>O componentă personalizată!</div>'
})

// creați o instanță rădăcină
new Vue({
  el: '#example'
})
```

Ceea ce va face:

``` html
<div id="example">
  <div>O componentă personalizată!</div>
</div>
```

{% raw %}
<div id="example" class="demo">
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<div>O componentă personalizată!</div>'
})
new Vue({ el: '#example' })
</script>
{% endraw %}

### Înregistrare Locală

Nu trebuie să înregistrați fiecare componentă la nivel global. Puteți face o componentă disponibilă numai în scopul unei alte instanțe/componente, înregistrând-o cu opțiunea instanțelor `components`:

``` js
var Child = {
  template: '<div>O componentă personalizată!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> va fi disponibilă numai în șablonul părintelui
    'my-component': Child
  }
})
```

Aceeași încapsulare se aplică și altor caracteristici Vue registerabile, cum ar fi directivele.

### Caracteristicile Parsării Șablonului DOM

Când utilizați DOM-ul ca șablon (de exemplu, folosiți opțiunea `el` pentru a monta un element cu conținut existent), veți fi supuși anumitor restricții inerente modului în care HTML funcționează, deoarece Vue poate prelua numai conținutul șablonului **după ce** browser-ul a analizat și a normalizat-o. În special, unele elemente precum `<ul>`, `<ol>`, `<table>` și `<select>` au restricții asupra elementelor care pot apărea în ele și unele elemente precum `<option>` pot apărea numai în anumite elemente.

Acest lucru va duce la probleme atunci când se utilizează componente personalizate cu elemente care au astfel de restricții, de exemplu:

``` html
<table>
  <my-row>...</my-row>
</table>
```

Componenta personalizată `<my-row>` va fi ridicată ca conținut nevalid, provocând astfel erori în eventuala ieșire randată. O soluție este folosirea atributului special `is`:

``` html
<table>
  <tr is="my-row"></tr>
</table>
```

**Trebuie de remarcat faptul că aceste limitări nu se aplică dacă utilizați șabloane de șir dintr-una din următoarele surse**:

- `<script type="text/x-template">`
- JavaScript inline template strings
- `.vue` components

Prin urmare, preferați să utilizați șabloane de șir ori de câte ori este posibil.

### `data` Trebuie să fie o funcție

Majoritatea opțiunilor care pot fi transmise în constructorul Vue pot fi utilizate într-o componentă, cu un caz special: `data` trebuie să fie o funcție. De fapt, dacă încercați acest lucru:

``` js
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'Salut'
  }
})
```

Apoi Vue va opri și va emite avertismente în consola, spunându-vă că `data` trebuie să fie o funcție pentru instanțele componente. Este bine să înțelegem de ce există normele, deci hai să înțelegem.

``` html
<div id="example-2">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
```

``` js
var data = { counter: 0 }

Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
   // datele sunt din punct de vedere tehnic o funcție, deci Vue nu se
   // va plânge, dar va returna același obiect
   // referință pentru fiecare instanță componentă

  data: function () {
    return data
  }
})

new Vue({
  el: '#example-2'
})
```

{% raw %}
<div id="example-2" class="demo">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
<script>
var data = { counter: 0 }
Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return data
  }
})
new Vue({
  el: '#example-2'
})
</script>
{% endraw %}

Întrucât toate cele trei instanțe componente împart același obiect `data`, incrementarea unui contor, incrementează pe toate! Aoleu! Să rezolvăm acest lucru prin returnarea unui obiect de date nou:

``` js
data: function () {
  return {
    counter: 0
  }
}
```

Acum, fiecare dintre contoarele noastre au fiecare propria lor stare internă:

{% raw %}
<div id="example-2-5" class="demo">
  <my-component></my-component>
  <my-component></my-component>
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  }
})
new Vue({
  el: '#example-2-5'
})
</script>
{% endraw %}

### Componența componentelor

Componentele sunt destinate a fi utilizate împreună, cel mai frecvent în relațiile părinte-copil: componenta A poate folosi componenta B în propriul șablon. În mod inevitabil, este necesar să se comunice unii cu alții: părintele poate avea nevoie să transmită date copilului, iar copilul poate avea nevoie să informeze părintele despre ceva care sa întâmplat în copil. Cu toate acestea, este foarte important să păstrați părintele și copilul cât mai detașați pe o interfață clar definită. Acest lucru asigură faptul că fiecare componentă poate fi scrisă și argumentată în mod izolat, făcându-i astfel mai ușor de întreținut și, eventual, mai ușor de reutilizat.

În Vue, relația părinte-copil componentă poate fi rezumată ca **parametri de intrare-în jos, evenimente-în sus(props down, events up)**. Parintele transmite datele copilului prin **parametri de intrare-props**, iar copilul trimite mesaje părintelui prin **evenimente-events**. Să vedem cum funcționează în continuare.

<p style="text-align: center;">
  <img style="width: 300px;" src="/images/props-events.png" alt="props down, events up">
</p>

## Parametri de Intrare

### Transmiterea datelor cu Parametrii de Intrare

Fiecare instanță a componentei are propriul ei **domeniu de aplicare izolat**. Aceasta înseamnă că nu puteți (și nu ar trebui) să trimiteți direct datele părinților într-un șablon al componentei copilului. Datele pot fi transmise către componentele copilului folosind **parametrii de intrare**.

Parametrul de intrare(Prop) este un atribut personalizat pentru transmiterea informațiilor din componentele părinte. O componentă copil trebuie să declare explicit parametrii de intrare pe care se așteaptă să le primească folosind opțiunea [`parametrii de intrare`](../api/#props):

``` js
Vue.component('child', {
  // declararea parametrilor de intrare
  props: ['message'],
  // cum ar fi data, parametrul de intrare poate fi folosit în șablonuri și
   // este, de asemenea, disponibil în vm ca this.message
  template: '<span>{{ message }}</span>'
})
```

Putem transmite un șir simplu în componentă, de exemplu:

``` html
<child message="salut!"></child>
```

Rezultat:

{% raw %}
<div id="prop-example-1" class="demo">
  <child message="salut!"></child>
</div>
<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['message'],
      template: '<span>{{ message }}</span>'
    }
  }
})
</script>
{% endraw %}

### camelCase vs. kebab-case

Atributele HTML nu sunt insensibile la litere mari, deci atunci când folosesc șabloane non-string, denumirile de propoziții de tip camelCase trebuie să folosească echivalentele lor cu kebab-case (delimitat cu liniuță):

``` js
Vue.component('child', {
  // camelCase în JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

``` html
<!-- kebab-case în HTML -->
<child my-message="salut!"></child>
```

Din nou, dacă utilizați șabloane de șir, această limitare nu se aplică.

### Parametri de Intrare Dinamici

Similar cu legarea unui atribut normal unei expresii, putem folosi, de asemenea, `v-bind` pentru parametrul de intrare dinamic, legat de datele din părinte. Ori de câte ori datele sunt actualizate în părinte, acestea vor apărea și în copil:

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```

De asemenea, puteți utiliza sintaxa prescurtată pentru `v-bind`:

``` html
<child :my-message="parentMsg"></child>
```

Rezultat:

{% raw %}
<div id="demo-2" class="demo">
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Mesaj de la părinte'
  },
  components: {
    child: {
      props: ['myMessage'],
      template: '<span>{{myMessage}}</span>'
    }
  }
})
</script>
{% endraw %}

Dacă doriți să treceți toate proprietățile dintr-un obiect ca elemente de parametru de intrare, puteți utiliza `v-bind` fără argument (`v-bind` în loc de `v-bind:prop-name`). De exemplu, având în vedere un obiect `todo`:

``` js
todo: {
  text: 'Learn Vue',
  isComplete: false
}
```

Apoi:

``` html
<todo-item v-bind="todo"></todo-item>
```

Va fi echivalent cu:

``` html
<todo-item
  v-bind:text="todo.text"
  v-bind:is-complete="todo.isComplete"
></todo-item>
```

### Literalii vs. Parametrii Dinamici

O greșeală obișnuită pe care începătorii o fac când încearcă să treacă un număr folosind sintaxa literală:

``` html
<!-- acest lucru trece printr-un șir simplu "1" -->
<comp some-prop="1"></comp>
```

Cu toate acestea, deoarece aceasta este o propoziție literală, valoarea sa este transmisă ca un șir simplu "1" în loc de un număr real. Dacă vrem să transmitem un număr JavaScript real, trebuie să folosim `v-bind` astfel încât valoarea sa să fie evaluată ca expresie JavaScript:

``` html
<!-- acest lucru duce la un număr real -->
<comp v-bind:some-prop="1"></comp>
```

### Flux de Date Unidirecționale

Toți parametrii de intrare formează o legătură **unidirecțională-în jos** între proprietatea copilului și cea parentală: atunci când proprietatea părinte actualizează, acesta va curge până la copil, dar nu invers. Acest lucru împiedică componentele copilului să modifice accidental starea părintelui, ceea ce poate face ca fluxul de date al aplicației să fie mai greu de înțeles.

În plus, de fiecare dată când componenta parentală este actualizată, toate elementele parametrului de intrare din componenta copil vor fi actualizate cu ultima valoare. Acest lucru înseamnă că **nu ar trebui** să încerci să muți un element în interiorul unei componente copil. Dacă o faci, Vue te va avertiza în consola.

Există de obicei două cazuri în care ești tentant să muți un parametru de intrare:

1. Parametrul de intrare este folosit pentru a trece într-o valoare inițială; componenta copil vrea să-l folosească ulterior ca proprietate de date locală.

2. Parametrul de intrare este transmis ca valoare neprelucrată care trebuie transformată.

Răspunsul corespunzător la aceste cazuri de utilizare este:

1. Definiți o proprietate de date locală care utilizează valoarea inițială a parametrului de intrare ca valoare inițială:

  ``` js
  props: ['initialCounter'],
  data: function () {
    return { counter: this.initialCounter }
  }
  ```
2. Definiți o proprietate computed care este calculată din valoarea parametrului de intrare:

  ``` js
  props: ['size'],
  computed: {
    normalizedSize: function () {
      return this.size.trim().toLowerCase()
    }
  }
  ```

<p class="tip">Rețineți că obiectele și matricele(arrays) din JavaScript sunt transmise prin referință, deci dacă parametrul de intrare este o matrice sau un obiect, mutarea obiectului sau a matricei în interiorul copilului **va afecta** starea parentală.</p>

### Validarea Parametrului de intrare

Este posibil ca o componentă să specifice cerințele pentru parametrii de intrare pe care le primește. Dacă o cerință nu este îndeplinită, Vue va emite avertismente. Acest lucru este util în special atunci când creați o componentă care este destinată utilizării de către alții.

În loc de a defini parametrii de intrare ca o serie de șiruri de caractere, puteți utiliza un obiect cu cerințe de validare:

``` js
Vue.component('example', {
  props: {
    // verificarea tipului de bază ("null" înseamnă acceptarea oricărui tip)
    propA: Number,
    // mai multe tipuri posibile
    propB: [String, Number],
    // un șir necesar
    propC: {
      type: String,
      required: true
    },
    // un număr cu valoarea implicită
    propD: {
      type: Number,
      default: 100
    },
    // valorile implicite ale unui obiect/array ar trebui să fie returnate 
    // printr-o funcție
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // funcția validator personalizată
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```
`type` poate fi unul dintre următorii constructori nativi:

- String
- Number
- Boolean
- Function
- Object
- Array
- Symbol

În plus, `type` poate fi, de asemenea, o funcție constructor personalizată, iar afirmația va fi făcută cu o verificare `instanceof`.

Când validarea parametrului de intrare nu reușește, Vue va produce o avertizare în consolă (dacă se utilizează construirea de dezvoltare). Rețineți că parametrii de intrare sunt validați __înainte de__ crearea unei instanțe a componentei, deci în cadrul funcțiilor `default` sau `validator`, proprietățile instanțelor cum ar fi `data`, `computed` sau `methods` nu vor fi disponibile.

## Atributele Non-Prop

Un atribut non-prop este un atribut care este transmis unei componente, dar nu are definit un parametru de intrare corespunzător.

Deși parametrii de intrare definiți explicit sunt preferați pentru transmiterea informațiilor către o componentă copil, autorii bibliotecilor de componente nu pot întotdeauna să prevadă contextele în care pot fi folosite componentele acestora. De aceea, componentele pot accepta atribute arbitrare, care sunt adăugate la elementul rădăcină al componentei.

De exemplu, imaginați-vă că folosim o componentă `bs-date-input` a unui terț cu un plugin Bootstrap care necesită un atribut `data-3d-date-picker` pe intrare. Putem adăuga acest atribut instanței noastre componente:

``` html
<bs-date-input data-3d-date-picker="true"></bs-date-input>
```

Și atributul `data-3d-date-picker="true"` va fi adăugat automat la elementul rădăcină din `bs-date-input`.

### Înlocuirea/îmbinarea cu atributele existente

Imaginați-vă că acesta este șablonul pentru `bs-date-input`:

``` html
<input type="date" class="form-control">
```

Pentru a specifica o temă pentru pluginul de selectare a datei, este posibil să fie necesar să adăugăm o anumită clasă, după cum urmează:

``` html
<bs-date-input
  data-3d-date-picker="true"
  class="date-picker-theme-dark"
></bs-date-input>
```

În acest caz, două valori diferite pentru `class` sunt definite:

- `form-control`, care este setat de componentă în șablonul său
- `date-picker-theme-dark`, care este transmisă componentei de către părintele său

Pentru majoritatea atributelor, valoarea furnizată componentei va înlocui valoarea stabilită de componentă. Deci, de exemplu, trecerea `type = 'large'` va înlocui `type = 'date'` și probabil va întrerupe tot! Din fericire, atributele `class` și `style` sunt puțin mai inteligente, astfel ambele valori sunt îmbinate, făcând valoarea finală: `form-control date-picker-theme-dark`.

## Evenimente Personalizate

Am aflat că părintele poate să transmită datele copiilor folosind parametri de intrare, dar cum să comunicăm părintelui atunci când se întâmplă ceva? Aici vine sistemul de evenimente personalizate Vue.

### Utilizarea `v-on` cu Evenimente Personalizate

Fiecare instanță Vue implementează o [interfață de evenimente](../api/#Instance-Methods-Events), ceea ce înseamnă că puteți:

- Asculta un eveniment folosind `$on(eventName)`
- Activa un eveniment folosind `$emit(eventName)`

<p class="tip">Rețineți că sistemul de evenimente Vue este diferit de aplicația [EventTarget API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget) a browserului. Deși lucrează în mod similar, `$on` și `$emit` __nu__ sunt aliasuri pentru `addEventListener` și `dispatchEvent`.</p>

În plus, o componentă părinte poate asculta evenimentele emise de o componentă copil folosind `v-on` direct în șablonul în care este utilizată componenta copil.

<p class="tip">Nu puteți folosi `$on` pentru a asculta evenimentele emise de copii. Trebuie să utilizați `v-on` direct în șablon, ca în exemplul de mai jos.</p>

Aici este un exemplu:

``` html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```

``` js
Vue.component('button-counter', {
  template: '<button v-on:click="incrementCounter">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})

new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
```

{% raw %}
<div id="counter-event-example" class="demo">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
<script>
Vue.component('button-counter', {
  template: '<button v-on:click="incrementCounter">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  }
})
new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
</script>
{% endraw %}

În acest exemplu, este important să rețineți că componenta copil este încă complet decuplată de ceea ce se întâmplă în afara acesteia. Tot ce ea poate face este: să raporteze informații despre propria activitate, doar în cazul în care componentei părinte ar putea să-i pese.

### Legarea Evenimentelor Native de Componente

Pot exista momente când doriți să ascultați un eveniment nativ de pe elementul rădăcină al unei componente. În aceste cazuri, puteți să utilizați modificatorul `.native` pentru `v-on`. De exemplu:

``` html
<my-component v-on:click.native="doTheThing"></my-component>
```

### Modificatorul `.sync`

> 2.3.0+

În unele cazuri, este posibil să avem nevoie de o "legătură bidirecțională" pentru o propunere - de fapt, în Vue 1.x acesta este exact ceea ce a furnizat modificatorul `.sync`. Când o componentă a copilului mutează un element pe care îl are `.sync`, schimbarea valorii va fi reflectată în părinte. Acest lucru este convenabil, cu toate acestea, duce la probleme de întreținere pe termen lung, deoarece încalcă ipoteza unidirecțională a fluxului de date: codul care mută parametrul de intrare a derivatei afectează implicit statul părinte.

Din acest motiv, am eliminat modificatorul `.sync` când 2.0 a fost lansat. Cu toate acestea, am constatat că există într-adevăr cazuri în care ar putea fi util, mai ales atunci când expediați componente reutilizabile. Ceea ce trebuie să schimbăm este **să facem codul copilului care afectează statul părinte mai consistent și explicit.**

În 2.3.0+ am reintrodus modificatorul `.sync` pentru parametrul de intrare, dar de această dată este vorba doar de sintaxa zahăr care se extinde automat într-un ascultător suplimentar `v-on`:

Următoarea

``` html
<comp :foo.sync="bar"></comp>
```

este extinsă în:

``` html
<comp :foo="bar" @update:foo="val => bar = val"></comp>
```

Pentru ca componenta copilului să actualizeze valoarea `foo`, trebuie să emită în mod explicit un eveniment în loc să muteze parametrul de intrare:

``` js
this.$emit('update:foo', newValue)
```

### Componentele de intrare a Formularului utilizând Evenimente Personalizate

Evenimente personalizate pot fi, de asemenea, folosite pentru a crea intrări personalizate care funcționează cu `v-model`. Ține minte că:

``` html
<input v-model="something">
```

este zahăr sintetic pentru:

``` html
<input
  v-bind:value="something"
  v-on:input="something = $event.target.value">
```

Atunci când este utilizat cu o componentă, se simplifică în loc de:

``` html
<custom-input
  :value="something"
  @input="value => { something = value }">
</custom-input>
```

Deci, pentru ca o componentă să lucreze cu `v-model`, ar trebui să (acestea pot fi configurate în 2.2.0+):

- accepte un parametru de intrare `value`
- emită un eveniment `input` cu o valoare noua

Să o vedem în acțiune cu o simplă intrare în valută:

``` html
<currency-input v-model="price"></currency-input>
```

``` js
Vue.component('currency-input', {
  template: '\
    <span>\
      $\
      <input\
        ref="input"\
        v-bind:value="value"\
        v-on:input="updateValue($event.target.value)">\
    </span>\
  ',
  props: ['value'],
  methods: {
     // În loc să actualizeze valoarea direct, aceasta
     // folosește metoda de formare și de plasare a constrângerilor
     // din valoarea intrării
    updateValue: function (value) {
      var formattedValue = value
        // Ștergeți spațiile albe din ambele părți
        .trim()
        // Reduceți până la două litere după virgulă
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      // Dacă valoarea nu este normalizată,
      // o normalizăm manual
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      // Emiteți valoarea numerică prin evenimentul de actualizare
      this.$emit('input', Number(formattedValue))
    }
  }
})
```

{% raw %}
<div id="currency-input-example" class="demo">
  <currency-input v-model="price"></currency-input>
</div>
<script>
Vue.component('currency-input', {
  template: '\
    <span>\
      $\
      <input\
        ref="input"\
        v-bind:value="value"\
        v-on:input="updateValue($event.target.value)"\
      >\
    </span>\
  ',
  props: ['value'],
  methods: {
    updateValue: function (value) {
      var formattedValue = value
        .trim()
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      this.$emit('input', Number(formattedValue))
    }
  }
})
new Vue({
  el: '#currency-input-example',
  data: { price: '' }
})
</script>
{% endraw %}

Implementarea de mai sus este destul de naivă. De exemplu, utilizatorii au permisiunea de a introduce mai multe perioade și chiar și litere uneori! Astfel, pentru cei care doresc să vadă un exemplu netrivial, iată un filtru valutar mai robust:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/result,html,js" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### Personalizarea Componetei `v-model`

> Nou în 2.2.0+

În mod implicit, `v-model` pe o componentă folosește `value` drept parametru de intrare și `input` ca eveniment, dar unele tipuri de intrări, cum ar fi boxele de selectare și butoanele radio, pot să utilizeze parametru de intrare `value` pentru alt scop. Utilizarea opțiunii `model` poate evita conflictul în astfel de cazuri:

``` js
Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean,
    // acest lucru permite folosirea parametrului de intrare `value` pentru un alt scop
    
    value: String
  },
  // ...
})
```

``` html
<my-checkbox v-model="foo" value="some value"></my-checkbox>
```

Cele de mai sus vor fi echivalente cu:

``` html
<my-checkbox
  :checked="foo"
  @change="val => { foo = val }"
  value="some value">
</my-checkbox>
```

<p class="tip">Rețineți că trebuie să declarați în mod explicit parametrul de intrare `checked`.</p>

### Comunicarea Non Părinte-Copil

Uneori, este posibil ca două componente să fie nevoite să comunice una cu cealaltă, dar nu sunt părinte/copil reciproc. În scenariile simple, puteți utiliza o instanță Vue goală ca o magistrală a evenimentului central:

``` js
var bus = new Vue()
```
``` js
// în metoda din componenta A
bus.$emit('id-selected', 1)
```
``` js
// în hook-ul creat de componenta B
bus.$on('id-selected', function (id) {
  // ...
})
```

În cazuri mai complexe, trebuie să luați în considerare angajarea unui model dedicat [managementului de stat](state-management.html).

## Distribuirea Conținutului prin Sloturi

Când folosiți componente, ar fi bine adesea să le compuneți astfel:

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

Există două lucruri de reținut aici:

1. Componenta `<app>` nu știe ce conținut va primi. Aceasta decide componenta ce folosește `<app>`.

2. Componenta `<app>` are probabil propriul șablon.

Pentru a face compoziția să funcționeze, avem nevoie de o modalitate de a interconecta conținutul părintelui și șablonul propriu al componentei. Acesta este un proces denumit **distribuție de conținut** (sau "transluție" dacă sunteți familiarizat cu Angular). Vue.js implementează un API de distribuire a conținutului, care este modelat după versiunea actuală [Web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), utilizând elementul `<slot>` special pentru a servi drept puncte de distribuție pentru conținutul original.

### Domeniul de Compilare

Înainte de a intra în API, să clarificăm mai întâi ce domeniu de conținut este compilat. Imaginați-vă un șablon de genul:

``` html
<child-component>
  {{ message }}
</child-component>
```

Ar trebui ca mesajul `message` să fie legat de datele părintelui sau de datele copilului? Răspunsul este părintele. O regulă simplă de aplicare a componentei este:

> Totul din șablonul părintelui este compilat în domeniul părintelui; totul din șablonul copilului este compilat în domeniul copilului.

O greșeală comună este încercarea de a lega o directivă de o proprietate/metodă copil în șablonul părinte:

``` html
<!-- NU funcționează -->
<child-component v-show="someChildProperty"></child-component>
```

Presupunând că `someChildProperty` este o proprietate asupra componentei copil, exemplul de mai sus nu ar funcționa. Șablonul părintelui nu cunoaște starea unei componente copil.

Dacă trebuie să legați directivele privind domeniul de aplicare pentru copii pe un nod rădăcină al componentei, ar trebui să faceți acest lucru în propriul șablon al componentei copilului:

``` js
Vue.component('child-component', {
  // acest lucru funcționează, deoarece suntem în domeniul potrivit
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

În mod similar, conținutul distribuit va fi compilat în domeniul de aplicare parental.

### Slot Unic

Conținutul parental va fi **eliminat** cu excepția cazului în care șablonul componentei copil conține cel puțin o priză `<slot>`. Când există un singur slot fără atribute, întregul fragment de conținut va fi inserat în poziția sa din DOM, înlocuind slotul propriu-zis.

Orice element inițial din interiorul tagurilor `<slot>` este considerat **conținut de rezervă**. Conținutul de fond este compilat în domeniul copilului și va fi afișat numai dacă elementul gazdă este gol și nu are conținut inserat.

Să presupunem că avem o componentă denumită `my-component` cu următorul șablon:

``` html
<div>
  <h2>Sunt titlul copilului</h2>
  <slot>
    Aceasta va fi afișată numai dacă nu există conținut
    pentru a fi distribuite.
  </slot>
</div>
```

Și un părinte care utilizează componenta:

``` html
<div>
  <h1>Sunt titlul părintelui</h1>
  <my-component>
    <p>Un conținut original</p>
    <p>Un alt conținut original</p>
  </my-component>
</div>
```

Rezultatul obținut va fi:

``` html
<div>
  <h1>Sunt titlul părintelui</h1>
  <div>
    <h2>Sunt titlul copilului</h2>
    <p>Un conținut original</p>
    <p>Un alt conținut original</p>
  </div>
</div>
```

### Sloturile Denumite

Elementele `<slot>` au un atribut special, `name`, care poate fi folosit pentru a personaliza mai mult modul în care trebuie distribuit conținutul. Puteți avea mai multe sloturi cu nume diferite. Un slot denumit se va potrivi cu orice element care are un atribut `slot` corespunzător în fragmentul de conținut.

Poate fi încă un slot fără nume, care este **slotul implicit** ce servește ca o priză de captură pentru orice conținut de neegalat. Dacă nu există niciun slot implicit, conținutul de neegalat va fi eliminat.

De exemplu, să presupunem că avem o componentă `app-layout` cu următorul șablon:

``` html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Marcaj parental:

``` html
<app-layout>
  <h1 slot="header">Aici ar putea fi un titlu de pagină</h1>

  <p>Un paragraf pentru conținutul principal.</p>
  <p>Și înca unul.</p>

  <p slot="footer">Aici plasăm datele de contact</p>
</app-layout>
```

Rezultatul obținut va fi:

``` html
<div class="container">
  <header>
    <h1>Aici ar putea fi un titlu de pagină</h1>
  </header>
  <main>
    <p>Un paragraf pentru conținutul principal.</p>
    <p>Și înca unul.</p>
  </main>
  <footer>
    <p>Aici plasăm datele de contact</p>
  </footer>
</div>
```

API-ul de distribuire a conținutului este un mecanism foarte util atunci când se proiectează componentele care trebuie să fie compuse împreună.

### Sloturi cu Domeniu de Vizibilitate Limitat

> Nou în 2.1.0+

Un slot cu domeniu de vizibilitate limitat este un tip special de slot care funcționează ca un șablon reutilizabil (care poate fi transferat de date) în loc de elemente deja redate.

Într-o componentă copil, transmiteți datele într-un slot ca și cum ați trece elemente de recuzită la o componentă:

``` html
<div class="child">
  <slot text="mesajul derivatei"></slot>
</div>
```

În părinte, trebuie să existe un element `<template>` cu un atribut special `slot-scope`, indicând că acesta este un șablon pentru un slot cu domeniu de vizibilitate limitat. Valoarea `slot-scope` va fi folosită ca nume a unei variabile temporare care deține obiectul de recuzită trecut de la copil:


``` html
<div class="parent">
  <child>
    <template slot-scope="props">
      <span>mesajul părintelui</span>
      <span>{{ props.text }}</span>
    </template>
  </child>
</div>
```

Dacă vom face cele de mai sus, rezultatul va fi:

``` html
<div class="parent">
  <div class="child">
    <span>mesajul părintelui</span>
    <span>mesajul copilului</span>
  </div>
</div>
```

> În 2.5.0+, `slot-scope` nu mai este limitat la `<template>` și poate fi utilizat pe orice element sau component.

Un caz de utilizare mai tipic pentru sloturile cu domeniu de vizibilitate limitat ar fi o componentă de listă care permite consumatorului componentei să personalizeze modul în care fiecare element din listă ar trebui să fie redat:

``` html
<my-awesome-list :items="items">
  <!-- slotul cu domeniu de vizibilitate limitat de asemenea poate fi numit -->
  <li
    slot="item"
    slot-scope="props"
    class="my-fancy-item">
    {{ props.text }}
  </li>
</my-awesome-list>
```

Și șablonul pentru componenta listei:

``` html
<ul>
  <slot name="item"
    v-for="item in items"
    :text="item.text">
    <!-- aici e conținutul rezervă -->
  </slot>
</ul>
```

#### Destructurarea

Valoarea `scope-slot` este, de fapt, o expresie JavaScript validă care poate apărea în poziția argumentului unei semnături de funcție. Acest lucru înseamnă că în medii acceptate (în componente cu un singur fișier sau în browsere moderne) puteți utiliza, de asemenea, distrugerea ES2015 în expresia:

``` html
<child>
  <span slot-scope="{ text }">{{ text }}</span>
</child>
```

## Componente Dinamice

Puteți utiliza același punct de montare și comuta dinamic între mai multe componente folosind elementul `<component>` rezervat și legați dinamic de atributul `is`:

``` js
var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```

``` html
<component v-bind:is="currentView">
  <!-- modificările componentelor atunci când vm.currentView se modifică!-->
</component>
```

Dacă preferați, puteți lega direct și obiecte componente:

``` js
var Home = {
  template: '<p>Bine ai venit acasă!</p>'
}

var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```

### `keep-alive`

Dacă doriți să păstrați componentele dezactivate în memorie, astfel încât să puteți păstra starea lor sau să evitați redarea, puteți împacheta o componentă dinamică într-un element `<keep-alive>`:

``` html
<keep-alive>
  <component :is="currentView">
    <!-- componentele inactive vor fi stocate în memoria cache! -->
  </component>
</keep-alive>
```

Verificați mai multe detalii despre `<keep-alive>` în [API reference](../api/#keep-alive).

## Misc

### Authoring Reusable Components

When authoring components, it's good to keep in mind whether you intend to reuse it somewhere else later. It's OK for one-off components to be tightly coupled, but reusable components should define a clean public interface and make no assumptions about the context it's used in.

The API for a Vue component comes in three parts - props, events, and slots:

- **Props** allow the external environment to pass data into the component

- **Events** allow the component to trigger side effects in the external environment

- **Slots** allow the external environment to compose the component with extra content.

With the dedicated shorthand syntaxes for `v-bind` and `v-on`, the intents can be clearly and succinctly conveyed in the template:

``` html
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat"
>
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```

### Child Component Refs

Despite the existence of props and events, sometimes you might still need to directly access a child component in JavaScript. To achieve this you have to assign a reference ID to the child component using `ref`. For example:

``` html
<div id="parent">
  <user-profile ref="profile"></user-profile>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// access child component instance
var child = parent.$refs.profile
```

When `ref` is used together with `v-for`, the ref you get will be an array containing the child components mirroring the data source.

<p class="tip">`$refs` are only populated after the component has been rendered, and it is not reactive. It is only meant as an escape hatch for direct child manipulation - you should avoid using `$refs` in templates or computed properties.</p>

### Async Components

In large applications, we may need to divide the app into smaller chunks and only load a component from the server when it's actually needed. To make that easier, Vue allows you to define your component as a factory function that asynchronously resolves your component definition. Vue will only trigger the factory function when the component actually needs to be rendered and will cache the result for future re-renders. For example:

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Pass the component definition to the resolve callback
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

The factory function receives a `resolve` callback, which should be called when you have retrieved your component definition from the server. You can also call `reject(reason)` to indicate the load has failed. The `setTimeout` here is for demonstration; how to retrieve the component is up to you. One recommended approach is to use async components together with [Webpack's code-splitting feature](https://webpack.js.org/guides/code-splitting/):

``` js
Vue.component('async-webpack-example', function (resolve) {
  // This special require syntax will instruct Webpack to
  // automatically split your built code into bundles which
  // are loaded over Ajax requests.
  require(['./my-async-component'], resolve)
})
```

You can also return a `Promise` in the factory function, so with Webpack 2 + ES2015 syntax you can do:

``` js
Vue.component(
  'async-webpack-example',
  // The `import` function returns a `Promise`.
  () => import('./my-async-component')
)
```

When using [local registration](components.html#Local-Registration), you can also directly provide a function that returns a `Promise`:

``` js
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

<p class="tip">If you're a <strong>Browserify</strong> user that would like to use async components, its creator has unfortunately [made it clear](https://github.com/substack/node-browserify/issues/58#issuecomment-21978224) that async loading "is not something that Browserify will ever support." Officially, at least. The Browserify community has found [some workarounds](https://github.com/vuejs/vuejs.org/issues/620), which may be helpful for existing and complex applications. For all other scenarios, we recommend using Webpack for built-in, first-class async support.</p>

### Advanced Async Components

> New in 2.3.0+

Starting in 2.3.0+ the async component factory can also return an object of the following format:

``` js
const AsyncComp = () => ({
  // The component to load. Should be a Promise
  component: import('./MyComp.vue'),
  // A component to use while the async component is loading
  loading: LoadingComp,
  // A component to use if the load fails
  error: ErrorComp,
  // Delay before showing the loading component. Default: 200ms.
  delay: 200,
  // The error component will be displayed if a timeout is
  // provided and exceeded. Default: Infinity.
  timeout: 3000
})
```

Note that when used as a route component in `vue-router`, these properties will be ignored because async components are resolved upfront before the route navigation happens. You also need to use `vue-router` 2.4.0+ if you wish to use the above syntax for route components.

### Component Naming Conventions

When registering components (or props), you can use kebab-case, camelCase, or PascalCase.

``` js
// in a component definition
components: {
  // register using kebab-case
  'kebab-cased-component': { /* ... */ },
  // register using camelCase
  'camelCasedComponent': { /* ... */ },
  // register using PascalCase
  'PascalCasedComponent': { /* ... */ }
}
```

Within HTML templates though, you have to use the kebab-case equivalents:

``` html
<!-- always use kebab-case in HTML templates -->
<kebab-cased-component></kebab-cased-component>
<camel-cased-component></camel-cased-component>
<pascal-cased-component></pascal-cased-component>
```

When using _string_ templates however, we're not bound by HTML's case-insensitive restrictions. That means even in the template, you can reference your components using:

- kebab-case
- camelCase or kebab-case if the component has been defined using camelCase
- kebab-case, camelCase or PascalCase if the component has been defined using PascalCase

``` js
components: {
  'kebab-cased-component': { /* ... */ },
  camelCasedComponent: { /* ... */ },
  PascalCasedComponent: { /* ... */ }
}
```

``` html
<kebab-cased-component></kebab-cased-component>

<camel-cased-component></camel-cased-component>
<camelCasedComponent></camelCasedComponent>

<pascal-cased-component></pascal-cased-component>
<pascalCasedComponent></pascalCasedComponent>
<PascalCasedComponent></PascalCasedComponent>
```

This means that the PascalCase is the most universal _declaration convention_ and kebab-case is the most universal _usage convention_.

If your component isn't passed content via `slot` elements, you can even make it self-closing with a `/` after the name:

``` html
<my-component/>
```

Again, this _only_ works within string templates, as self-closing custom elements are not valid HTML and your browser's native parser will not understand them.

### Recursive Components

Components can recursively invoke themselves in their own template. However, they can only do so with the `name` option:

``` js
name: 'unique-name-of-my-component'
```

When you register a component globally using `Vue.component`, the global ID is automatically set as the component's `name` option.

``` js
Vue.component('unique-name-of-my-component', {
  // ...
})
```

If you're not careful, recursive components can also lead to infinite loops:

``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

A component like the above will result in a "max stack size exceeded" error, so make sure recursive invocation is conditional (i.e. uses a `v-if` that will eventually be `false`).

### Circular References Between Components

Let's say you're building a file directory tree, like in Finder or File Explorer. You might have a `tree-folder` component with this template:

``` html
<p>
  <span>{{ folder.name }}</span>
  <tree-folder-contents :children="folder.children"/>
</p>
```

Then a `tree-folder-contents` component with this template:

``` html
<ul>
  <li v-for="child in children">
    <tree-folder v-if="child.children" :folder="child"/>
    <span v-else>{{ child.name }}</span>
  </li>
</ul>
```

When you look closely, you'll see that these components will actually be each other's descendent _and_ ancestor in the render tree - a paradox! When registering components globally with `Vue.component`, this paradox is resolved for you automatically. If that's you, you can stop reading here.

However, if you're requiring/importing components using a __module system__, e.g. via Webpack or Browserify, you'll get an error:

```
Failed to mount component: template or render function not defined.
```

To explain what's happening, let's call our components A and B. The module system sees that it needs A, but first A needs B, but B needs A, but A needs B, etc, etc. It's stuck in a loop, not knowing how to fully resolve either component without first resolving the other. To fix this, we need to give the module system a point at which it can say, "A needs B _eventually_, but there's no need to resolve B first."

In our case, let's make that point the `tree-folder` component. We know the child that creates the paradox is the `tree-folder-contents` component, so we'll wait until the `beforeCreate` lifecycle hook to register it:

``` js
beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue')
}
```

Problem solved!

### Inline Templates

When the `inline-template` special attribute is present on a child component, the component will use its inner content as its template, rather than treating it as distributed content. This allows more flexible template-authoring.

``` html
<my-component inline-template>
  <div>
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```

However, `inline-template` makes the scope of your templates harder to reason about. As a best practice, prefer defining templates inside the component using the `template` option or in a `template` element in a `.vue` file.

### X-Templates

Another way to define templates is inside of a script element with the type `text/x-template`, then referencing the template by an id. For example:

``` html
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```

``` js
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```

These can be useful for demos with large templates or in extremely small applications, but should otherwise be avoided, because they separate templates from the rest of the component definition.

### Cheap Static Components with `v-once`

Rendering plain HTML elements is very fast in Vue, but sometimes you might have a component that contains **a lot** of static content. In these cases, you can ensure that it's only evaluated once and then cached by adding the `v-once` directive to the root element, like this:

``` js
Vue.component('terms-of-service', {
  template: '\
    <div v-once>\
      <h1>Terms of Service</h1>\
      ... a lot of static content ...\
    </div>\
  '
})
```
