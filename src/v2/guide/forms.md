---
title: Lucru cu Formularele
type: guide
order: 10
---

## Utilizare de Bază

Puteți utiliza directiva `v-model` pentru a crea legături de date bidirecționale pe elementele de intrare și textarea formularului. Se alege automat modul corect de actualizare a elementului pe baza tipului de intrare. Deși un pic neobișnuit, `v-model` este în esență sintaxa "zahăr" pentru actualizarea datelor privind evenimentele de intrare ale utilizatorului, plus o îngrijire specială pentru unele cazuri de margine.

<p class="tip">`v-model` va ignora atributele inițiale `value`, `checked` sau `selected` găsite pe orice element ale formularului. Acesta va trata întotdeauna datele instanței Vue ca sursa adevarului. Ar trebui să declarați valoarea inițială pe partea JavaScript, în interiorul opțiunii `data` a componentei dvs.</p>

<p class="tip" id="vmodel-ime-tip">Pentru limbile care necesită o [IME](https://en.wikipedia.org/wiki/Input_method) (chineză, japoneză, coreeană etc.), veți observa că `v-model` nu se actualizează în timpul compoziţiei IME. Dacă doriți să satisfaceți și aceste actualizări, utilizați, un eveniment `input`.</p>

### Text

``` html
<input v-model="message" placeholder="editează-mă">
<p>Mesajul este: {{ message }}</p>
```

{% raw %}
<div id="example-1" class="demo">
  <input v-model="message" placeholder="editează-mă">
  <p>Mesajul este: {{ message }}</p>
</div>
<script>
new Vue({
  el: '#example-1',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

### Text Multiline

``` html
<span>Mesajul multiline este:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="Adaugă linii multiple"></textarea>
```

{% raw %}
<div id="example-textarea" class="demo">
  <span>Mesajul multiline este:</span>
  <p style="white-space: pre-line;">{{ message }}</p>
  <br>
  <textarea v-model="message" placeholder="Adaugă linii multiple"></textarea>
</div>
<script>
new Vue({
  el: '#example-textarea',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

{% raw %}
<p class="tip">Interpolarea pe textarea (<code>&lt;textarea&gt;{{text}}&lt;/textarea&gt;</code>) nu va funcționa. Utilizați <code>v-model</code>.</p>
{% endraw %}

### Casetă de selectare(Checkbox)

Casetă de selectare(Checkbox) unică, valoare booleană:

``` html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```
{% raw %}
<div id="example-2" class="demo">
  <input type="checkbox" id="checkbox" v-model="checked">
  <label for="checkbox">{{ checked }}</label>
</div>
<script>
new Vue({
  el: '#example-2',
  data: {
    checked: false
  }
})
</script>
{% endraw %}

Casete de selectare multiple, legate la aceeași matrice(array):

``` html
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Nume bifate: {{ checkedNames }}</span>
</div>
```

``` js
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
```

{% raw %}
<div id="example-3" class="demo">
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Nume bifate: {{ checkedNames }}</span>
</div>
<script>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
</script>
{% endraw %}

### Radio

``` html
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">Unu</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Doi</label>
<br>
<span>Selecţionat: {{ picked }}</span>
```
{% raw %}
<div id="example-4" class="demo">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">Unu</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Doi</label>
  <br>
  <span>Selecţionat: {{ picked }}</span>
</div>
<script>
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
</script>
{% endraw %}

### Selectare

Selectare unică:

``` html
<select v-model="selected">
  <option disabled value="">Vă rugăm selectați o obțiune</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>Selectat: {{ selected }}</span>
```
``` js
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```
{% raw %}
<div id="example-5" class="demo">
  <select v-model="selected">
    <option disabled value="">Vă rugăm selectați o obțiune</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selectat: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-5',
  data: {
    selected: ''
  }
})
</script>
{% endraw %}

<p class="tip">Dacă valoarea inițială a expresiei `v-model` nu se potrivește cu niciuna dintre opțiuni, elementul <select> va fi afișat într-o stare "neselectată". În iOS, acest lucru va face ca utilizatorul să nu poată selecta primul element, deoarece iOS nu declanșează un eveniment de schimbare în acest caz. Prin urmare, este recomandat să furnizați o opțiune dezactivată cu o valoare goală, așa cum este demonstrat în exemplul de mai sus.</p>

Selectare multiplă (legat de Array):

``` html
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<br>
<span>Selectat: {{ selected }}</span>
```
{% raw %}
<div id="example-6" class="demo">
  <select v-model="selected" multiple style="width: 50px;">
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selectat: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
})
</script>
{% endraw %}

Opțiuni dinamice rendate cu `v-for`:

``` html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selectat: {{ selected }}</span>
```
``` js
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'Unu', value: 'A' },
      { text: 'Doi', value: 'B' },
      { text: 'Trei', value: 'C' }
    ]
  }
})
```
{% raw %}
<div id="example-7" class="demo">
  <select v-model="selected">
    <option v-for="option in options" v-bind:value="option.value">
      {{ option.text }}
    </option>
  </select>
  <span>Selectat: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-7',
  data: {
    selected: 'A',
    options: [
      { text: 'Unu', value: 'A' },
      { text: 'Doi', value: 'B' },
      { text: 'Trei', value: 'C' }
    ]
  }
})
</script>
{% endraw %}

## Legarea Valorilor

Pentru radio, caseta de selectare și opțiunile de selectare, valorile obligatorii `v-model` sunt, de obicei, șiruri statice (sau boolean pentru caseta de selectare):

``` html
<!-- `picked` este un șir "a" când este bifat -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` este fie adevărat, fie fals -->
<input type="checkbox" v-model="toggle">

<!-- `selected` este un șir "abc" -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

Dar uneori am putea dori să legăm valoarea la o proprietate dinamică din instanța Vue. Putem folosi `v-bind` pentru a realiza acest lucru. În plus, `v-bind` ne permite să legăm valoarea de intrare la valori non-string.

### Checkbox

``` html
<input
  type="checkbox"
  v-model="toggle"
  v-bind:true-value="a"
  v-bind:false-value="b"
>
```

``` js
// când e bifat:
vm.toggle === vm.a
// când nu e bifat:
vm.toggle === vm.b
```

### Radio

``` html
<input type="radio" v-model="pick" v-bind:value="a">
```

``` js
// când e bifat:
vm.pick === vm.a
```

### Opțiuni de Selectare

``` html
<select v-model="selected">
  <!-- obiect literal în linie -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```

``` js
// când e selectat:
typeof vm.selected // => 'obiect'
vm.selected.number // => 123
```

## Modificatorii

### `.lazy`

În mod implicit, `v-model` sincronizează intrarea cu datele după fiecare eveniment de intrare (cu excepția compoziției IME ca [de mai sus](#vmodel-ime-tip)). Puteți adăuga modificatorul `lazy` în loc să se sincronizeze după evenimentele `change`:

``` html
<!-- sincronizat după "change" în loc de "input" -->
<input v-model.lazy="msg" >
```

### `.number`

Dacă doriți ca intrarea utilizatorului să fie difuzată automat ca număr, puteți adăuga modificatorul `number` la intrările gestionate de `v-model`:

``` html
<input v-model.number="age" type="number">
```

Acest lucru este adesea util, deoarece chiar și cu `type="number"`, valoarea elementelor de intrare HTML întoarce întotdeauna un șir.

### `.trim`

Dacă doriți ca intrarea utilizatorului să fie tăiată în mod automat, puteți adăuga modificatorul `trim` la intrările gestionate de `v-model`:

```html
<input v-model.trim="msg">
```

## `v-model` cu Componente

> Dacă nu sunteți încă familiarizat cu componentele Vue, puteți trece peste acest lucru acum.

Tipurile de intrări integrate ale HTML-ului nu vor răspunde întotdeauna nevoilor dvs. Din fericire, componentele Vue vă permit să construiți intrări reutilizabile cu un comportament complet personalizat. Aceste intrări chiar funcționează cu `v-model`! Pentru a afla mai multe, citiți despre [intrările personalizate](components.html#Form-Input-Components-using-Custom-Events) din ghidul de Componente.
