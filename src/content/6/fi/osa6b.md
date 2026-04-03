---
mainImage: ../../../images/part-6.svg
part: 6
letter: b
lang: fi
---

<div class="content">

Jatketaan muistiinpanosovelluksen Zustand-version laajentamista.

Sovelluskehitystä helpottaaksemme muutetaan alkutilaa siten, että siellä on jo muutama muistiinpano:

```js
// highlight-start
const initialNotes = [
    {
      id: 1,
      content: 'Zustand is less complex than Redux',
      important: true,
    }, {
      id: 2,
      content: 'React app benefits from custom hooks',
      important: false,
    }, {
      id: 3,
      content: 'Remember to sleep well',
      important: true,
    }
  ]


//highlight-end

const useNoteStore = create((set) => ({
  notes: initialNotes,
  // ...
}
```

### Monimutkaisempi tila

Toteutetaan sovellukseen näytettävien muistiinpanojen filtteröinti, jonka avulla näytettäviä muistiinpanoja voidaan rajata. Filtterin toteutus tapahtuu [radiopainikkeiden](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/radio) avulla:

![Sivun alussa lomake muistiinpanon lisäämiseen (syötekenttä ja nappi add). Tämän jälkeen radiopainikevalinta mitkä muistiinpanot näytetään, vaihtoehdot all, important ja noimportant. Näiden alle renderöidän kaikki muistiinpanot ja niiden yhteyteen teksti important jos muistiinpano merkattu tärkeäksi. ](../../images/6/u1.png)

Herää kysymys, miten filtterin tilanhallinta kannattaisi hoitaa. Vaihtoehtoja on käytännössä kaksi: tehdään filterille erillinen store Zustandilla, tai lisätään se samaan tilaan. Kumpikin näistä ratkaisuista on perusteltavissa. Internetistä löytyvät [parhaat käytänteet](https://tkdodo.eu/blog/working-with-zustand#keep-the-scope-of-your-store-small) kehottavat pitämään toisistaan täysin erilliset asiat erillisissä storeissa. Muistiinpanojen lista ja filtteröinti ovat kuitenkin sen verran sidoksissa toisiinsa, että päädymme sijoittamaan molemmat samaan storeen:

```js
const useNoteStore = create((set) => ({
  notes: initialNotes,
  filter: 'all',
  actions: {
    add: note => set(
      state => ({ notes: state.notes.concat(note) })
    ),
    toggleImportance: id => set(
      state => ({
        notes: state.notes.map(note =>
          note.id === id ? { ...note, important: !note.important } : note
        )
      })
    ),
    setFilter: value => set(() => ({ filter: value })) // highlight-line
  }
}))

export const useNotes = () => useNoteStore((state) => state.notes)
export const useFilter = () => useNoteStore((state) => state.filter) // highlight-line
export const useNoteActions = () => useNoteStore((state) => state.actions)
```

Filtterille arvon asettava komponentti:

```js
import { useNoteActions } from './store'

const VisibilityFilter = () => {
  const { setFilter } = useNoteActions()

  return (
    <div>
      <input
        type="radio"
        name="filter"
        onChange={() => setFilter('all')}
        defaultChecked
      />
      all
      <input
        type="radio"
        name="filter"
        onChange={() => setFilter('important')}
      />
      important
      <input
        type="radio"
        name="filter"
        onChange={() => setFilter('nonimportant')}
      />
      not important
    </div>
  )
}

export default VisibilityFilter
```

Komponentti <i>App</i> renderöi filtterin:

```js
const App = () => (
  <div>
    <NoteForm />
    <VisibilityFilter />
    <NoteList />
  </div>
)
```

Näytettävien muistiinpanojen filtteröinti voitaisiin hoitaa komponentissa <i>NoteList</i> esim. seuraavassa:

```js
import { useNotes, useFilter } from './store'
import Note from './Note'

const NoteList = () => {
  const notes = useNotes()
  const filter = useFilter() // highhlight-line

  // highhlight-start
  const notesToShow = notes.filter(note => {
    if (filter === 'important') return note.important
    if (filter === 'nonimportant') return !note.important
    return true
  })
  // highhlight-end

  return (
    <ul>
      {notesToShow.map(note => ( // highhlight-line
        <Note key={note.id} note={note} />
      ))}
    </ul>
  )
}
```

Parempi ratkaisuun päädytään jos filtteröintilogiikka sisällytetään suoraan storen funktiossa  <i>useNotes</i>:
js
```
import { create } from 'zustand'

const useNoteStore = create((set) => ({
  // ...
}))

// highlight-start
export const useNotes = () => useNoteStore(({ notes, filter }) => {
  if (filter === 'important') return notes.filter(n => n.important)
  if (filter === 'nonimportant') return notes.filter(n => !n.important)
  return notes
})
// highlight-end
```

Eli funktio <i>useNotes</i> palauttaa aina halutulla tavalla filtteröidyn muistiinpanojen listan. Funktion käyttäjän, eli komponentin <i>NoteList</i> ei tarvitse edes olla tietoinen filtterin olemassaolosta:

```js
import { useNotes } from './store'
import Note from './Note'

const NoteList = () => {
  // component gets always the properly filtered set of notes
  const notes = useNotes()

  return (
    <ul>
      {notes.map(note => (
        <Note key={note.id} note={note} />
      ))}
    </ul>
  )
}
```

Ratkaisu on elegantti!

Sovelluksen tämänhetkinen koodi on kokonaisuudessaan [GitHubissa](https://github.com/fullstack-hy2020/redux-notes/tree/part6-3), branchissa <i>part6-3</i>.

</div>

<div class="tasks">

### Tehtävä 6.6

Jatketaan tehtävässä 6.3 aloitetun Reduxia käyttävän anekdoottisovelluksen parissa.

#### 6.6 anekdootit, step5

Toteuta sovellukseen näytettävien anekdoottien filtteröiminen:

![Yläosaan lisätään tekstikenttä, johon kirjoittamalla voidaan rajoittaa näytettävät anekdootit niihin joihin sisältyy "filtterikenttään" kirjoitettu merkkijono](../../images/6/9ea.png)

Säilytä filtterin tila Redux-storessa. Käytännössä kannattaa tehdä uusi reducer ja action creatorit, ja luoda storea varten yhdistetty reduceri funktion <i>combineReducers</i> avulla.

Tee filtterin ruudulla näyttämistä varten komponentti <i>Filter</i>. Voit ottaa sen pohjaksi seuraavan koodin:

```js
const Filter = () => {
  const handleChange = (event) => {
    // input-kentän arvo muuttujassa event.target.value
  }
  const style = {
    marginBottom: 10
  }

  return (
    <div style={style}>
      filter <input onChange={handleChange} />
    </div>
  )
}

export default Filter
```

</div>

<div class="content">

### Data palvelimelle

Laajennetaan sovellusta siten, että muistiinpanot talletetaan backendiin. Käytetään osasta 2 tuttua [JSON Serveriä](/osa2/palvelimella_olevan_datan_hakeminen).

Tallennetaan projektin juureen tiedostoon <i>db.json</i> tietokannan alkutila:

```json
{
  "notes": [
    {
      "id": 1,
      "content": "Zustand is less complex than Redux",
      "important": true
    },
    {
      "id": 2,
      "content": "React app benefits from custom hooks",
      "important": false
    },
    {
      "id": 3,
      "content": "Remember to sleep well",
      "important": true
    }
  ]
}
```

Asennetaan projektiin JSON Server

```bash
npm install json-server --save-dev
```

ja lisätään tiedoston <i>package.json</i> osaan <i>scripts</i> rivi

```js
"scripts": {
  "server": "json-server -p 3001 db.json",
  // ...
}
```

Käynnistetään JSON Server komennolla _npm run server_.

### Fetch API

Ohjelmistokehityksessä joudutaan usein pohtimaan, kannattaako jokin toiminnallisuus toteuttaa käyttämällä ulkoista kirjastoa vai onko parempi hyödyntää ympäristön tarjoamia natiiveja ratkaisuja. Molemmilla lähestymistavoilla on omat etunsa ja haasteensa. 

Olemme käyttäneet HTTP-pyyntöjen tekemiseen kurssin aiemmissa osissa [Axios](https://axios-http.com/docs/intro)-kirjastoa. Tutustutaan nyt vaihtoehtoiseen tapaan tehdä HTTP-pyyntöjä natiivia [Fetch APIa](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) hyödyntäen.

On tyypillistä, että ulkoinen kirjasto kuten <i>Axios</i> on toteutettu hyödyntäen muita ulkoisia kirjastoja. Esimerkiksi jos Axioksen asentaa projektiin komennolla _npm install axios_, konsoliin tulostuu: 

```bash
$ npm install axios

added 23 packages, and audited 302 packages in 1s

71 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

Komento asentaisi projektiin siis Axios-kirjaston lisäksi yli 20 muuta npm-pakettia, jotka Axios tarvitsisi toimiakseen. 

<i>Fetch API</i> tarjoaa samankaltaisen tavan tehdä HTTP-pyyntöjä kuin Axios, mutta Fetch APIn käyttäminen ei vaadi ulkoisten kirjastojen asentamista. Sovelluksen ylläpito helpottuu, kun päivitettäviä kirjastoja on vähemmän, ja myös tietoturva paranee, koska sovelluksen mahdollinen hyökkäyspinta-ala pienenee. Sovellusten tietoturvaa ja ylläpitoa sivutaan kurssin [osassa 7](https://fullstackopen.com/osa7/luokkakomponentit_sekalaista#react-node-sovellusten-tietoturva).

Pyyntöjen tekeminen tapahtuu käytännössä käyttämällä _fetch()_-funktiota. Käytettävässä syntaksissa on jonkin verran eroja verrattuna Axiokseen. Huomaamme myös pian, että Axios on huolehtinut joistakin asioista puolestamme ja helpottanut elämäämme. Käytämme nyt kuitenkin Fetch APIa, koska se on laajasti käytetty natiiviratkaisu, joka jokaisen Full Stack -kehittäjän on syytä tuntea.

### Datan hakeminen palvelimelta

Tehdään backendistä dataa hakeva funktio tiedostoon <i>src/services/notes.js</i>:

```js
const baseUrl = 'http://localhost:3001/notes'

const getAll = async () => {
  const response = await fetch(baseUrl)

  if (!response.ok) {
    throw new Error('Failed to fetch notes')
  }

  const data = await response.json()
  return data
}

export default { getAll }
```

Tutkitaan _getAll_-metodin toteutusta tarkemmin. Muistiinpanot haetaan backendistä nyt kutsumalla _fetch()_-funktiota, jolle on annettu argumentiksi backendin URL-osoite. Pyynnön tyyppiä ei ole erikseen määritelty, joten _fetch_ toteuttaa oletusarvoisen toiminnon eli GET-pyynnön.

Kun vastaus on saapunut, tarkistetaan pyynnön onnistuminen vastauksen kentästä _response.ok_ ja heitetään tarvittaessa virhe:

```js
if (!response.ok) {
  throw new Error('Failed to fetch notes')
}
```

Attribuutti _response.ok_ saa arvon _true_, jos pyyntö on onnistunut eli jos vastauksen statuskoodi on välillä 200-299. Kaikilla muilla statuskoodeilla, esimerkiksi 404 tai 500, se saa arvon _false_. 

Huomaa, että _fetch_ ei automaattisesti heitä virhettä, vaikka vastauksen statuskoodi olisi esimerkiksi 404. Virheenkäsittely tulee toteuttaa manuaalisesti, kuten olemme nyt tehneet.

Jos pyyntö on onnistunut, vastauksen sisältämä data muunnetaan JSON-muotoon:

```js
const data = await response.json()
```

_fetch_ ei siis automaattisesti muunna vastauksen mukana mahdollisesti olevaa dataa JSON-muotoon, vaan muunnos tulee tehdä manuaalisesti. On myös hyvä huomata, että _response.json()_ on asynkroninen metodi, eli sen kanssa tulee käyttää <i>await</i>-avainsanaa.

Suoraviivaistetaan koodia vielä hieman palauttamalla suoraan metodin _response.json()_ palauttama data:

```js
const getAll = async () => {
  const response = await fetch(baseUrl)

  if (!response.ok) {
    throw new Error('Failed to fetch notes')
  }

  return await response.json() // highlight-line
}
```

Lisätään storeen funktio, jonka avulla tila voidaan alustaa palvelimelta haettavilla muistiinpanoilla:

```js
const useNoteStore = create((set) => ({
  notes: [], // highlight-line
  filter: '',
  actions: {
    // ...
    setFilter: value => set(() => ({ filter: value })),
    initialize: notes => set(() => ({ notes })) // highlight-line
  }
}))
```

Toteutetaan muistiinpanojen alustus <i>App</i>-komponentiin, eli kuten yleensä dataa palvelimelta haettaessa, käytetään <i>useEffect</i>-hookia:

```js
const App = () => {
  const { initialize } = useNoteActions()

  useEffect(() => {
    noteService.getAll().then(notes => initialize(notes))
  }, [initialize])

  return (
    <div>
      <NoteForm />
      <VisibilityFilter />
      <NoteList />
    </div>
  )
}
```


Muistiinpanot haetaan palvelimelta siis käyttäen määrittelemäämme _getAll()_-metodia ja tallennetaan sitten storen funktiolla <i>initialize</i> . Toiminnot tehdään <i>useEffect</i>-hookissa eli ne suoritetaan App-komponentin ensimmäisen renderoinnin yhteydessä.

Tutkitaan vielä tarkemmin erästä pientä yksityiskohtaa. Olemme lisänneet <i>initialize</i>-funktion <i>useEffect</i>-hookin riippuvuustaulukkoon. Jos yritämme käyttää tyhjää riippuvuustaulukkoa, ESLint antaa seuraavan varoituksen: <i>React Hook useEffect has a missing dependency: 'dispatch'</i>. Mistä on kyse?

Koodi toimisi loogisesti täysin samoin, vaikka käyttäisimme tyhjää riippuvuustaulukkoa, koska <i>initialize</i> viittaa samaan funktioon koko ohjelman suorituksen ajan. On kuitenkin hyvän ohjelmointikäytännön mukaista lisätä _useEffect_-hookin riippuvuuksiksi kaikki sen käyttämät muuttujat ja funktiot, jotka on määritelty kyseisen komponentin sisällä. Näin voidaan välttää yllättäviä bugeja.

### Datan lähettäminen palvelimelle

Toteutetaan seuraavaksi toiminnallisuus uuden muistiinpanon lähettämiseksi palvelimelle. Pääsemme samalla harjoittelemaan, miten POST-pyyntö tehdään _fetch()_-metodia käyttäen.

Laajennetaan tiedostossa <i>src/services/notes.js</i> olevaa palvelimen kanssa kommunikoivaa koodia seuraavasti:

```js
const baseUrl = 'http://localhost:3001/notes'

const getAll = async () => {
  const response = await fetch(baseUrl)

  if (!response.ok) {
    throw new Error('Failed to fetch notes')
  }

  return await response.json()
}

// highlight-start
const createNew = async (content) => {
  const response = await fetch(baseUrl, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ content, important: false }),
  })
  
  if (!response.ok) {
    throw new Error('Failed to create note')
  }
  
  return await response.json()
}
// highlight-end

export default { getAll, createNew } // highlight-line
```

Tutkitaan _createNew_-metodin toteutusta tarkemmin. _fetch()_-funktion ensimmäinen parametri määrittelee URL-osoitteen, johon pyyntö tehdään. Toinen parametri on olio, joka määrittelee muut pyynnön yksityiskohdat, kuten pyynnön tyypin, otsikot ja pyynnön mukana lähetettävän datan. Voimme selkeyttää koodia vielä hieman tallentamalla pyynnön yksityiskohdat määrittelevän olion erilliseen <i>options</i>-apumuuttujaan:

```js
const createNew = async (content) => {
  // highlight-start
  const options = {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ content, important: false }),
  }
  
  const response = await fetch(baseUrl, options)
  // highlight-end

  if (!response.ok) {
    throw new Error('Failed to create note')
  }
  
  return await response.json()
}
```

Tutkitaan <i>options</i>-oliota tarkemmin:

- <i>method</i> määrittelee pyynnön tyypin, joka tässä tapauksessa on <i>POST</i>
- <i>headers</i> määrittelee pyynnön otsikot. Liitämme pyyntöön otsikon _'Content-Type': 'application/json'_, jotta palvelin tietää, että pyynnön mukana oleva data on JSON-muotoista, ja osaa käsitellä pyynnön oikein
- <i>body</i> sisältää pyynnön mukana lähetettävän datan. Kentään ei voi suoraan sijoittaa JavaScript-oliota, vaan se tulee ensin muuntaa JSON-merkkijonoksi kutsumalla funktiota _JSON.stringify()_

Kuten GET-pyynnön kanssa, myös nyt vastauksen statuskoodi tutkitaan virheiden varalta:

```js
if (!response.ok) {
  throw new Error('Failed to create note')
}
```

Jos pyyntö onnistuu, <i>JSON Server</i> palauttaa juuri luodun muistiinpanon, jolle se on generoinut myös yksilöllisen <i>id</i>:n. Vastauksen sisältämä data tulee kuitenkin vielä muuntaa JSON-muotoon metodilla _response.json()_: 

```js
return await response.json()
```

Muutetaan sitten sovelluksemme <i>NoteForm</i>-komponenttia siten, että uusi muistiinpano lähetetään backendiin. Komponentin metodi _addNote_ muuttuu hiukan:

```js
import { useNoteActions } from './store'
import noteService from './services/notes'

const NoteForm = () => {
  const { add } = useNoteActions()

  const addNote = async (e) => {
    e.preventDefault()
    const content = e.target.note.value
    const newNote = await noteService.createNew(content) // highlight-line
    add(newNote)
    e.target.reset()
  }

  return (
    <form onSubmit={addNote}>
      <input name="note" />
      <button type="submit">add</button>
    </form>
  )
}

export default NoteForm
```

Kun uusi muistiinpano luodaan backendiin kutsumalla funktiota <i>createNew()<i>, saadaan paluuarvona muistiinpanoa kuvaava olio, jolle backend on generoinut <i>id</i>:n.

Sovelluksen tämänhetkinen koodi on kokonaisuudessaan [GitHubissa](https://github.com/fullstack-hy2020/redux-notes/tree/part6-4), branchissa <i>part6-4</i>.

### Asynkroniset actionit

Lähestymistapamme on melko hyvä, mutta siinä mielessä ikävä, että palvelimen kanssa kommunikointi tapahtuu komponentit määrittelevien funktioiden koodissa. Olisi parempi, jos kommunikointi voitaisiin abstrahoida komponenteilta siten, että niiden ei tarvitsisi kuin kutsua sopivaa storen tarjoamaa funktiota. 

Haluammekin, että <i>App</i> alustaa sovelluksen tilan seuraavasti:

```js
const App = () => {
  const { initialize } = useNoteActions()

  useEffect(() => {
    initialize()
  }, [initialize])

  return (
    <div>
      <NoteForm />
      <VisibilityFilter />
      <NoteList />
    </div>
  )
}
```


<i>NoteForm</i> puolestaan luo uuden muistiinpanon näin:

```js
const NoteForm = () => {
  const { add } = useNoteActions()

  const addNote = async (e) => {
    e.preventDefault()
    const content = e.target.note.value
    await add(content)
    e.target.reset()
  }

  return (
    <form onSubmit={addNote}>
      <input name="note" />
      <button type="submit">add</button>
    </form>
  )
}
```

Tiedostoon <i>store.js</i> tehtävä muutos on seuraava:

```js
import { create } from 'zustand'
import noteService from './services/notes' // highlight-line

const useNoteStore = create((set) => ({
  notes: [],
  filter: '',
  actions: {
    add: async (content) => {
      const newNote = await noteService.createNew(content)
      set(state => ({ notes: state.notes.concat(newNote) }))
    },
    initialize: async () => {
      const notes = await noteService.getAll()
      set(() => ({ notes }))
    },
    // ...
  }
}))
```

Funktiot <i>add</i> ja <i>initialize</i> on siis muutettu asynkronisiksi funktioiksi, jotka kutsuvat ensin sopivaa noteServicen funktiota, ja päivittävät tilan tämän jälkeen.

Ratkaisu on tyylikäs, tilan käsittely ja palvelimen kanssa kommunikointi on kokonaisuudessaan eriytetty React-komponenttien ulkopuolelle.

Viimeistellään vielä sovellus siten, että muistiinpanojen tärkeyden muutos synkronoidaan palvelimelle.

<i>noteService.js</i> laajenee seuraavasti:

```js
const update = async (id, note) => {
  const response = await fetch(`${baseUrl}/${id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(note),
  })

  if (!response.ok) {
    throw new Error('Failed to update note')
  }

  return await response.json()
}

export default { getAll, createNew, update } 
```

Storen funktion <i>toggleImportance</i> muutos ei ole sekään yllättävä

```js
const useNoteStore = create((set) => ({
  notes: [],
  filter: '',
  actions: {
    add: async (content) => {
      const newNote = await noteService.createNew(content)
      set(state => ({ notes: state.notes.concat(newNote) }))
    },
    // highlight-start
    toggleImportance: async (id) => {
      const note = useNoteStore.getState().notes.find(n => n.id === id)
      const updated = await noteService.update(id, { ...note, important: !note.important })
      set(state => ({
        notes: state.notes.map(n => n.id === id ? updated : n)
      }))
    },
    // highlight-end
    setFilter: value => set(() => ({ filter: value })),
    initialize: async () => {
      const notes = await noteService.getAll()
      set(() => ({ notes }))
    }
  }
}))
```

Sovelluksen lopullinen koodi on [GitHubissa](https://github.com/fullstack-hy2020/redux-notes/tree/part6-5) branchissa <i>part6-5</i>.

</div>

<div class="tasks">

### Tehtävät 6.7.-6.n.

#### 6.7 anekdootit, step6

Hae sovelluksen käynnistyessä anekdootit JSON Serverillä toteutetusta backendistä. Käytä HTTP-pyynnön tekemiseen Fetch APIa.

Backendin alustavan sisällön saat esim. [täältä](https://github.com/fullstack-hy2020/misc/blob/master/anecdotes.json).

#### 6.8 anekdootit, step7

Muuta uusien anekdoottien luomista siten, että anekdootit talletetaan backendiin. Hyödynnä toteutuksessasi jälleen Fetch APIa.

#### 6.9 anekdootit, step8

Äänestäminen ei vielä talleta muutoksia backendiin. Korjaa tilanne Redux Thunk ‑kirjastoa ja Fetch APIa hyödyntäen.

#### 6.10 anekdootit, step9

Sovelluksessa on valmiina komponentin <i>Notification</i> runko:

```js
const Notification = () => {
  const style = {
    border: 'solid',
    padding: 10,
    borderWidth: 1,
    marginBottom: 10
  }

  return (
    <div style={style}>
      render here notification...
    </div>
  )
}

export default Notification
```

Laajenna komponenttia siten, että se renderöi Zustand-storeen talletetun viestin. Tee toiminnallisuutta varten oma reduceri.

Tässä vaiheessa sovelluksen ei vielä tarvitse osata käyttää <i>Notification</i>-komponenttia järkevällä tavalla, vaan riittää että sovellus toimii ja näyttää <i>notificationReducerin</i> alkuarvoksi asettaman viestin.

#### 6.11 anekdootit, step11

Laajenna sovellusta siten, että se näyttää <i>Notification</i>-komponentin avulla viiden sekunnin ajan, kun sovelluksessa äänestetään tai luodaan uusia anekdootteja:

![Äänestyksen yhteydessä näytetään notifikaatio: you voted 'if it hurts, do it more often'](../../images/6/8eb.png)


#### 6.12 anekdootit step12

Notifikaatioiden tekeminen on nyt hieman ikävää, sillä se edellyttää kahden actionin tekemistä ja _setTimeout_-funktion käyttöä:

```js
dispatch(setNotification(`new anecdote '${content}'`))
setTimeout(() => {
  dispatch(clearNotification())
}, 5000)
```

Toteuta action creator, joka mahdollistaa notifikaation antamisen seuraavasti:

```js
dispatch(setNotification(`you voted '${anecdote.content}'`, 10))
```

Ensimmäisenä parametrina on renderöitävä teksti ja toisena notifikaation näyttöaika sekunneissa.

Ota paranneltu notifikaatiotapa käyttöön sovelluksessasi.

</div>

<div class="content">

Zustand-storejen testaaminen...


</div>
