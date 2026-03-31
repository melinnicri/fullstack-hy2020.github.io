---
mainImage: ../../../images/part-7.svg
part: 7
letter: a
lang: fi
---

<div class="content">

Kurssin seitsemännen osan tehtävät poikkeavat jossain määrin muiden osien tehtävistä. Tässä ja seuraavassa luvussa on normaaliin tapaan [luvun teoriaan liittyviä tehtäviä](/osa7/react_router/#tehtavat-7-1-7-3).

Tämän ja seuraavan luvun tehtävien lisäksi seitsemäs osa sisältää kertaavan ja soveltavan [tehtäväsarjan](/osa7/tehtavia_blogilistan_laajennus), jossa laajennetaan osissa 4 ja 5 tehtyä Bloglist-sovellusta.

### React Router

Tämän luvun materiaali siirtynyt [osaan 5](/osa5/react_router_tyylikirjastot). Voit kuitenkin edelleen tehdä aiheeseen liittyvät tehtävät.

</div>

<div class="tasks">

### Tehtävät 7.1.-7.3.

Jatketaan anekdoottien parissa. Ota seuraaviin tehtäviin pohjaksi repositoriossa <https://github.com/fullstack-hy2020/routed-anecdotes> oleva Reduxiton anekdoottisovellus.

Jos kloonaat projektin olemassaolevan Git-repositorion sisälle, <i>poista kloonatun sovelluksen Git-konfiguraatio</i>:

```bash
cd routed-anecdotes   // eli mene ensin kloonatun repositorion hakemistoon
rm -rf .git
```

Sovellus käynnistyy normaaliin tapaan, mutta joudut ensin asentamaan sovelluksen riippuvuudet:

```bash
npm install
npm run dev
```

#### 7.1: routed anecdotes, step1

Lisää sovellukseen React Router siten, että <i>Menu</i>-komponentissa olevia linkkejä klikkailemalla saadaan säädeltyä näytettävää näkymää.

Sovelluksen juuressa eli polulla _/_ näytetään anekdoottien lista:

![](../../assets/teht/40.png)

Pohjalla oleva <i>Footer</i>-komponentti tulee näyttää aina.

Uuden anekdootin luominen tapahtuu esim. polulla <i>create</i>:

![](../../assets/teht/41.png)

#### 7.2: routed anecdotes, step2

Toteuta sovellukseen yksittäisen anekdootin tiedot näyttävä näkymä:

![](../../assets/teht/42.png)

Yksittäisen anekdootin sivulle navigoidaan klikkaamalla anekdootin nimeä:

![](../../assets/teht/43.png)

#### 7.3: routed anecdotes, step3

Luomislomakkeen oletusarvoinen toiminnallisuus on melko hämmentävä, sillä kun lomakkeen avulla luodaan uusi muistiinpano, mitään ei näytä tapahtuvan.

Paranna toiminnallisuutta siten, että luomisen jälkeen siirrytään automaattisesti kaikkien anekdoottien näkymään <i>ja</i> käyttäjälle näytetään viiden sekunnin ajan onnistuneesta lisäyksestä kertova notifikaatio:

![](../../assets/teht/44.png)

</div>
