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

### `.sync` Modifier

> 2.3.0+

In some cases we may need "two-way binding" for a prop - in fact, in Vue 1.x this is exactly what the `.sync` modifier provided. When a child component mutates a prop that has `.sync`, the value change will be reflected in the parent. This is convenient, however it leads to maintenance issues in the long run because it breaks the one-way data flow assumption: the code that mutates child props are implicitly affecting parent state.

This is why we removed the `.sync` modifier when 2.0 was released. However, we've found that there are indeed cases where it could be useful, especially when shipping reusable components. What we need to change is **making the code in the child that affects parent state more consistent and explicit.**

In 2.3.0+ we re-introduced the `.sync` modifier for props, but this time it is only syntax sugar that automatically expands into an additional `v-on` listener:

The following

``` html
<comp :foo.sync="bar"></comp>
```

is expanded into:

``` html
<comp :foo="bar" @update:foo="val => bar = val"></comp>
```

For the child component to update `foo`'s value, it needs to explicitly emit an event instead of mutating the prop:

``` js
this.$emit('update:foo', newValue)
```

### Form Input Components using Custom Events

Custom events can also be used to create custom inputs that work with `v-model`. Remember:

``` html
<input v-model="something">
```

is syntactic sugar for:

``` html
<input
  v-bind:value="something"
  v-on:input="something = $event.target.value">
```

When used with a component, it instead simplifies to:

``` html
<custom-input
  :value="something"
  @input="value => { something = value }">
</custom-input>
```

So for a component to work with `v-model`, it should (these can be configured in 2.2.0+):

- accept a `value` prop
- emit an `input` event with the new value

Let's see it in action with a simple currency input:

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
    // Instead of updating the value directly, this
    // method is used to format and place constraints
    // on the input's value
    updateValue: function (value) {
      var formattedValue = value
        // Remove whitespace on either side
        .trim()
        // Shorten to 2 decimal places
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      // If the value was not already normalized,
      // manually override it to conform
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      // Emit the number value through the input event
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

The implementation above is pretty naive though. For example, users are allowed to enter multiple periods and even letters sometimes - yuck! So for those that want to see a non-trivial example, here's a more robust currency filter:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/result,html,js" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### Customizing Component `v-model`

> New in 2.2.0+

By default, `v-model` on a component uses `value` as the prop and `input` as the event, but some input types such as checkboxes and radio buttons may want to use the `value` prop for a different purpose. Using the `model` option can avoid the conflict in such cases:

``` js
Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean,
    // this allows using the `value` prop for a different purpose
    value: String
  },
  // ...
})
```

``` html
<my-checkbox v-model="foo" value="some value"></my-checkbox>
```

The above will be equivalent to:

``` html
<my-checkbox
  :checked="foo"
  @change="val => { foo = val }"
  value="some value">
</my-checkbox>
```

<p class="tip">Note that you still have to declare the `checked` prop explicitly.</p>

### Non Parent-Child Communication

Sometimes two components may need to communicate with one-another but they are not parent/child to each other. In simple scenarios, you can use an empty Vue instance as a central event bus:

``` js
var bus = new Vue()
```
``` js
// in component A's method
bus.$emit('id-selected', 1)
```
``` js
// in component B's created hook
bus.$on('id-selected', function (id) {
  // ...
})
```

In more complex cases, you should consider employing a dedicated [state-management pattern](state-management.html).

## Content Distribution with Slots

When using components, it is often desired to compose them like this:

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

There are two things to note here:

1. The `<app>` component does not know what content it will receive. It is decided by the component using `<app>`.

2. The `<app>` component very likely has its own template.

To make the composition work, we need a way to interweave the parent "content" and the component's own template. This is a process called **content distribution** (or "transclusion" if you are familiar with Angular). Vue.js implements a content distribution API that is modeled after the current [Web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), using the special `<slot>` element to serve as distribution outlets for the original content.

### Compilation Scope

Before we dig into the API, let's first clarify which scope the contents are compiled in. Imagine a template like this:

``` html
<child-component>
  {{ message }}
</child-component>
```

Should the `message` be bound to the parent's data or the child data? The answer is the parent. A simple rule of thumb for component scope is:

> Everything in the parent template is compiled in parent scope; everything in the child template is compiled in child scope.

A common mistake is trying to bind a directive to a child property/method in the parent template:

``` html
<!-- does NOT work -->
<child-component v-show="someChildProperty"></child-component>
```

Assuming `someChildProperty` is a property on the child component, the example above would not work. The parent's template is not aware of the state of a child component.

If you need to bind child-scope directives on a component root node, you should do so in the child component's own template:

``` js
Vue.component('child-component', {
  // this does work, because we are in the right scope
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

Similarly, distributed content will be compiled in the parent scope.

### Single Slot

Parent content will be **discarded** unless the child component template contains at least one `<slot>` outlet. When there is only one slot with no attributes, the entire content fragment will be inserted at its position in the DOM, replacing the slot itself.

Anything originally inside the `<slot>` tags is considered **fallback content**. Fallback content is compiled in the child scope and will only be displayed if the hosting element is empty and has no content to be inserted.

Suppose we have a component called `my-component` with the following template:

``` html
<div>
  <h2>I'm the child title</h2>
  <slot>
    This will only be displayed if there is no content
    to be distributed.
  </slot>
</div>
```

And a parent that uses the component:

``` html
<div>
  <h1>I'm the parent title</h1>
  <my-component>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </my-component>
</div>
```

The rendered result will be:

``` html
<div>
  <h1>I'm the parent title</h1>
  <div>
    <h2>I'm the child title</h2>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </div>
</div>
```

### Named Slots

`<slot>` elements have a special attribute, `name`, which can be used to further customize how content should be distributed. You can have multiple slots with different names. A named slot will match any element that has a corresponding `slot` attribute in the content fragment.

There can still be one unnamed slot, which is the **default slot** that serves as a catch-all outlet for any unmatched content. If there is no default slot, unmatched content will be discarded.

For example, suppose we have an `app-layout` component with the following template:

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

Parent markup:

``` html
<app-layout>
  <h1 slot="header">Here might be a page title</h1>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <p slot="footer">Here's some contact info</p>
</app-layout>
```

The rendered result will be:

``` html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

The content distribution API is a very useful mechanism when designing components that are meant to be composed together.

### Scoped Slots

> New in 2.1.0+

A scoped slot is a special type of slot that functions as a reusable template (that can be passed data to) instead of already-rendered-elements.

In a child component, pass data into a slot as if you are passing props to a component:

``` html
<div class="child">
  <slot text="hello from child"></slot>
</div>
```

In the parent, a `<template>` element with a special attribute `slot-scope` must exist, indicating that it is a template for a scoped slot. The value of `slot-scope` will be used as the name of a temporary variable that holds the props object passed from the child:

``` html
<div class="parent">
  <child>
    <template slot-scope="props">
      <span>hello from parent</span>
      <span>{{ props.text }}</span>
    </template>
  </child>
</div>
```

If we render the above, the output will be:

``` html
<div class="parent">
  <div class="child">
    <span>hello from parent</span>
    <span>hello from child</span>
  </div>
</div>
```

> In 2.5.0+, `slot-scope` is no longer limited to `<template>` and can be used on any element or component.

A more typical use case for scoped slots would be a list component that allows the component consumer to customize how each item in the list should be rendered:

``` html
<my-awesome-list :items="items">
  <!-- scoped slot can be named too -->
  <li
    slot="item"
    slot-scope="props"
    class="my-fancy-item">
    {{ props.text }}
  </li>
</my-awesome-list>
```

And the template for the list component:

``` html
<ul>
  <slot name="item"
    v-for="item in items"
    :text="item.text">
    <!-- fallback content here -->
  </slot>
</ul>
```

#### Destructuring

`scope-slot`'s value is in fact a valid JavaScript expression that can appear in the argument position of a function signature. This means in supported environments (in single-file components or in modern browsers) you can also use ES2015 destructuring in the expression:

``` html
<child>
  <span slot-scope="{ text }">{{ text }}</span>
</child>
```

## Dynamic Components

You can use the same mount point and dynamically switch between multiple components using the reserved `<component>` element and dynamically bind to its `is` attribute:

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
  <!-- component changes when vm.currentView changes! -->
</component>
```

If you prefer, you can also bind directly to component objects:

``` js
var Home = {
  template: '<p>Welcome home!</p>'
}

var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```

### `keep-alive`

If you want to keep the switched-out components in memory so that you can preserve their state or avoid re-rendering, you can wrap a dynamic component in a `<keep-alive>` element:

``` html
<keep-alive>
  <component :is="currentView">
    <!-- inactive components will be cached! -->
  </component>
</keep-alive>
```

Check out more details on `<keep-alive>` in the [API reference](../api/#keep-alive).

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
