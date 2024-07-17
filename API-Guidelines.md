# Upute za dizajn API-ja

- [Upute za dizajn API-ja](#upute-za-dizajn-api-ja)
  - [1. Opći principi](#1-opći-principi)
    - [1.1. Imenovanje](#11-imenovanje)
    - [1.2. Bool vs. Enum vs. String](#12-bool-vs-enum-vs-string)
    - [1.3. ID-jevi](#13-id-jevi)
    - [1.4. Definicija vremena](#14-definicija-vremena)
  - [2. Struktura API-ja](#2-struktura-api-ja)
  - [3. Akcije nad resursima](#3-akcije-nad-resursima)
    - [3.1. Dohvat resursa](#31-dohvat-resursa)
      - [3.1.1. Filtriranje](#311-filtriranje)
      - [3.1.2. Sortiranje](#312-sortiranje)
      - [3.1.3. Paginacija](#313-paginacija)
  - [4. HTTP metode](#4-http-metode)
    - [4.1. GET](#41-get)
    - [4.2. POST](#42-post)
    - [4.3. PATCH](#43-patch)
    - [4.4. PUT](#44-put)
    - [4.5. DELETE](#45-delete)
    - [4.6. OPTIONS](#46-options)
    - [4.7. HEAD](#47-head)
  - [5. Autentikacija](#5-autentikacija)
    - [3.1. Primjer autentikacije](#31-primjer-autentikacije)
  - [6. Autorizacija](#6-autorizacija)
    - [6.1. Primjer autorizacije](#61-primjer-autorizacije)
  - [7. Logiranje](#7-logiranje)
  - [8. Greške](#8-greške)
    - [8.1. Greške u zahtjevu](#81-greške-u-zahtjevu)
    - [8.2. Poruke grešaka](#82-poruke-grešaka)
  - [9. Caching](#9-caching)
  - [10. Statusni kodovi](#10-statusni-kodovi)
  - [11. Dokumentacija](#11-dokumentacija)
  - [12. Pojmovnik](#12-pojmovnik)
  - [13. Verzioniranje](#13-verzioniranje)
  - [14. Long-running operations](#14-long-running-operations)
  - [15. Deprecation](#15-deprecation)
  - [TODO](#todo)

## 1. Opći principi

API mora biti dizajniran tako da bude:

- jednostavan za uporabu
- jasan i razumljiv
- konzistentan
- siguran
- nadogradiv

### 1.1. Imenovanje

---

Kod imenovanja resursa, atributa i parametara treba se pridržavati sljedećih pravila:

| 📜 Pravilo | ✅ Primjer | ⛔ Izbjegavati |
|---------|---------|-------------|
| **Nazivi** resursa, atributa i parametara moraju biti na (američkom) **engleskom** jeziku. | `/users` </br> `/colors` | `/korisnici` </br> `/colours` |
| **Nazivi** resursa, atributa i parametara moraju biti **kratki, jasni i ne preopćeniti**. | `/product` | `/item` |
| **Nazivi resursa** moraju biti **imenice**. | `/users` | `/get-users` |
| **Kolekcije** moraju biti dostupne na URL-ovima s imenicom u **množini**. | `/users` | `/user` |
| Kada resurs **nije kolekcija**, tada se upotrebljava imenica u **jednini**. | `/account` | `/accounts` |
| Imena **resursa** moraju biti u `kebab-case` formatu. | `/academic-year` </br> `/messaging-group` | `/academic_year` </br> `/academicYear` |
| Imena **atributa i parametara** moraju biti u `camelCase` formatu. | `firstName` </br> `lastName` | `first_name` </br> `FirstName` |
| **Izbjegavati redundanciju** u imenima resursa, atributa i parametara. | `/users/{id}/groups` | `/users/{id}/user-groups` |
| **Kratice** se pišu **malim slovima**, poput riječi. | `id` </br> `userId` | `ID` </br> `URL` |
| Za riječ koja se piše sa **spojnicom (-)** vrijede pravila kao da se radi o više riječi. | `/real-time` </br> `realTime` | `/realtime` </br>  `realtime` |
| **Boolean** varijable moraju imati prefiks `is`, `has`, `can`, `should`, `allow` ili `show`. | `isActive` </br> `hasChildren` | `active` </br> `children` |

**Oznake resursa** su jedinstveni identifikator određenog resursa na API-ju i njihov naziv se formira spajanjem naziva kolekcija u URL-u, ali primjenom imenica u jednini i u formatu `camelCase`. U slučaju da je samo dio URL-a dovoljan za stvaranje jedinstvene oznake i ta oznaka jasno predstavlja resurs, tada nije potrebno navoditi sve nazive kolekcija iz URL-a u oznaci resursa. Na primjer:

- za URL `/users/{id}` oznaka resursa je `user`
- za URL `/users/{id}/groups/{id}` oznaka resursa je `userGroup`
- za URL `/academic-years/{id}` oznaka resursa je `academicYear`
- za URL `/students/{id}/enrolled-courses/{id}` oznaka resursa je `enrolledCourse` (jer je jasno da se radi o podacima o upisu kolegija)

Za **oznake skupine resursa** upotrebljava se **oznaka resursa u množini**. Na primjer:

- za URL `/users` oznaka skupine resursa je `users`
- za URL `/users/{id}/groups` oznaka skupine resursa je `userGroups`
- za URL `/students/{id}/enrolled-courses` oznaka skupine resursa je `enrolledCourses`
- za URL `/academic-years` oznaka skupine resursa je `academicYears`

Oznake resursa su korisne za jednostavno referenciranje resursa u dokumentaciji i logiranju, te omogućuju da svakome tko promatra odgovor odmah bude jasno o kojim podacima se radi.

Primjeri primjene oznaka resursa:

```json
{
  "data": {
    "user": {
      "id": "1234",
      "name": "John Doe",
      "email": ""
    }
  }
}
```

```json
{
  "data": {
    "users": [
      {
        "id": "1234",
        "name": "John Doe",
        "email": ""
      },
      {
        "id": "5678",
        "name": "Jane Doe",
        "email": ""
      }
    ]
  }
}
```

Sljedeće nazive potrebno je **izbjegavati** jer su preopćeniti:

- elements
- entries
- instances
- items
- objects
- resources
- types
- values

Oni se mogu u nekim slučajevima nadopuniti kako bi bili preciznije određeni, primjerice `rowValues`.

Sljedeći nazivi se trebaju upotrebljavati kada se pojavljuju isti koncepti. Time se osigurava konzistentnost i jasnoća kroz različite dijelove API-ja.

| Naziv             | Tip                      | Opis |
|-------------------|--------------------------|------|
| `name`            | `string`                 | Polje name treba sadržavati relativno ime resursa. |
| `parent`          | `string`                 | Za definicije resursa i zahtjeve za dohvaćanje/kreiranje, polje parent treba sadržavati relativno ime roditeljskog resursa. |
| `createTime`      | `Timestamp`              | Vrijeme kreiranja entiteta. |
| `updateTime`      | `Timestamp`              | Vrijeme posljednjeg ažuriranja entiteta. Napomena: update_time se ažurira kada se izvrši operacija kreiranja/ažuriranja/brisanja. |
| `deleteTime`      | `Timestamp`              | Vrijeme brisanja entiteta, samo ako se entitet označava kao obrisan. |
| `expireTime`      | `Timestamp`              | Vrijeme isteka entiteta ako isti istekne. |
| `startTime`       | `Timestamp`              | Vrijeme početka određenog vremenskog razdoblja. |
| `endTime`         | `Timestamp`              | Vrijeme završetka određenog vremenskog razdoblja ili operacije (bez obzira na njen uspjeh). |
| `readTime`        | `Timestamp`              | Vrijeme u kojem se entitet treba pročitati (ako se koristi u zahtjevu) ili je pročitan (ako se koristi u odgovoru). |
| `timeZone`        | `string`                 | Naziv vremenske zone. Trebao bi biti IANA TZ naziv, kao što je "America/Los_Angeles". Za više informacija, pogledajte <https://en.wikipedia.org/wiki/List_of_tz_database_time_zones>. |
| `regionCode`      | `string`                 | Unicode kod zemlje/regije (CLDR) lokacije, kao što su "US" i "419". Za više informacija, pogledajte <http://www.unicode.org/reports/tr35/#unicode_region_subtag>. |
| `languageCode`    | `string`                 | BCP-47 jezični kod, kao što su "en-US" ili "sr-Latn". Za više informacija, pogledajte <http://www.unicode.org/reports/tr35/#Unicode_locale_identifier>. |
| `mimeType`        | `string`                 | Objavljena MIME vrsta od strane IANA (također poznata kao media type). Za više informacija, pogledajte <https://www.iana.org/assignments/media-types/media-types.xhtml>. |
| `displayName`     | `string`                 | Prikazano ime entiteta. |
| `title`           | `string`                 | Službeno ime entiteta, kao što je naziv tvrtke. Treba se tretirati kao formalna verzija prikazanog imena. |
| `description`     | `string`                 | Jedan ili više paragrafa teksta s opisom entiteta. |
| `filter`          | `string`                 | Standardni parametar filtra za metode popisa. Pogledajte AIP-160. |
| `query`           | `string`                 | Isto kao i filter ako se primjenjuje na metodu pretraživanja (npr. :search) |
| `pageToken`       | `string`                 | Token za paginaciju u zahtjevu za popis. |
| `page`            | `int32`                  | Stranica u paginaciji (query parametar). |
| `currentPage`     | `int32`                  | Trenutna stranica u paginaciji (response body). |
| `pageSize`        | `int32`                  | Veličina paginacije u zahtjevu za popis. |
| `totalPages`      | `int32`                  | Ukupan broj stranica liste bez obzira na paginaciju. |
| `totalSize`       | `int32`                  | Ukupan broj stavki u listi bez obzira na paginaciju. |
| `nextPageToken`   | `string`                 | Sljedeći token za paginaciju u odgovoru na popis. Trebao bi se koristiti kao pageToken za sljedeći zahtjev. Prazna vrijednost znači da više nema rezultata. |
| `orderBy`         | `string`                 | Navodi redoslijed rezultata za zahtjeve dohvata nad kolekcijom. |
| `progressPercent` | `int32`                  | Navodi napredak akcije u postocima (0-100). Vrijednost -1 znači da je napredak nepoznat. |
| `requestId`       | `string`                 | Jedinstveni ID string za otkrivanje dupliciranih zahtjeva. |
| `labels`          | `map<string, string>`    | Predstavlja korisničke oznake resursa. |
| `showDeleted`     | `bool`                   | Ako resurs dopušta povratak izbrisanih entiteta, metoda dohvata kolekcije mora imati parametar show_deleted kako bi klijent mogao otkriti obrisane resurse. |
| `validateOnly`    | `bool`                   | Ako je true, označava da se zahtjev samo treba validirati, a ne izvršiti. |

### 1.2. Bool vs. Enum vs. String

---

Parametri i atributi API-ja koji služe kao izbornici opcija mogu biti definirani kao `bool`, `enum` ili `string`.

Smjernice za odabir tipa:

- Vrijednosti `bool` su prikladne za binarne opcije za koje smo sigurni da se neće proširivati, npr. za parametre poput `showDeleted`. Može poprimiti vrijednosti `true` ili `false`.
- Vrijednosti `enum` su prikladne za opcije koje bi se moglo proširivati, npr. `priority` ili `status`.
- Vrijednosti `string` su prikladne za opcije koje će se gotovo sigurno proširivati i to relativno često, npr. `type` ili `role`, ili za opcije koje su definirane po nekom vanjskom standardu, npr. `mimeType` ili `languageCode`.

Vrijednosti `enum` trebaju biti definirane u dokumentaciji API-ja, i moraju imati `TYPE_UNKNOWN` kao prvu opciju (npr. `STATUS_UNKNOWN`). Time se omogućava dodavanje novih vrijednosti bez narušavanja kompatibilnosti s klijentima koji ih još nisu implementirali.

### 1.3. ID-jevi

---

Svi **ID-jevi moraju biti definirani kao stringovi** zbog uniformnosti, fleksibilnosti i backwards compatibilityja. Time se nastoji izbjeći da zbog promjene tipa ID-ja dođe do problema s klijentima koji se koriste starim tipom ID-ja.

### 1.4. Definicija vremena

---

Datumi i vrijeme moraju pratiti [**ISO 8601**](https://en.wikipedia.org/wiki/ISO_8601) standard:

- Vrijeme se uvijek definira kao **UTC timestamp** u formatu `YYYY-MM-DDTHH:MM:SSZ`. Time se osigurava da se svi klijenti koriste istim vremenom i da se izbjegnu problemi s vremenskim zonama.
  - Primjer: `2024-01-01T00:00:00Z`.
  - Ako je potrebno, mogu se dodati i milisekunde: `2024-01-01T00:00:00.000Z`.
- Datumi se uvijek definiraju u formatu `YYYY-MM-DD`.
  - Primjer: `2024-01-01`.

## 2. Struktura API-ja

API komunicira putem **HTTP protokola**. Svi zahtjevi i odgovori moraju biti u **formatu JSON**.

Struktura JSON odgovora mora pratiti sljedeću shemu:

```json
{
  "data": {
    "collectionName": {
      "attribute1": "value1",
      "attribute2": "value2"
    }
  }
}
```

ili za kolekciju...

```json
{
  "data": {
    "collectionName": [
      {
        "attribute1": "value1",
        "attribute2": "value2"
      },
      {
        "attribute1": "value1",
        "attribute2": "value2"
      }
    ]
  }
}
```

ili za resurs s ugniježđenim resursima...

```json
{
  "data": {
    "collectionName": {
      "attribute1": "value1",
      "attribute2": "value2",
      "nestedCollection": [
        {
          "attribute1": "value1",
          "attribute2": "value2"
        },
        {
          "attribute1": "value1",
          "attribute2": "value2"
        }
      ]
    }
  }
}
```

API bi trebao imati **osnovni URL** koji se sastoji od verzije API-ja, npr. `https://example.com/api/v1`.

API bi trebao imati **statusni endpoint** (`/status`) koji vraća status i detalje API-ja:

- opći status (`status`) API-ja i resursa o kojima ovisi (poput baza podataka, drugih API-ja...): `Healthy`/`Unhealthy`
- trenutnu verziju (`version`) API-ja: `v1`/`v2`
- okruženje (`environment`): `Production`/`Staging`
- vrijeme na serveru (`timestamp`): `2024-01-01T00:00:00Z`

Primjer odgovora statusnog endpointa:

```json
{
  "data": {
    "status": "Healthy",
    "version": "v1",
    "environment": "Production",
    "timestamp": "2024-01-01T00:00:00Z",
    "dependencies": {
      "database": "Healthy",
      "otherApi": "Healthy"
    }
  }
}
```

URL **ne smije** završavati s `/`:

- Ispravno: `/users`
- Neispravno: `/users/`

**Jedinstveni resurs** je definiran na sljedeći način: `/collection/{id}`.

Resursi mogu biti **ugniježđeni** (`/collection/{id}/collection`) samo **ako je to prirodno** i ako podresurs sigurno **ne može pripadati nijednom drugom resursu**.

- Primjer: `/students/1234/enrolled-courses`.
- Ako se želi, primjerice, pod kolegijem studenta dohvatiti predavač, tada bi odgovor trebao sadržavati hyperlink na predavača:

```json
{
  "data": {
    "enrolledCourses": [
      {
        "id": "1234",
        "name": "Calculus",
        "lecturer": {
          "id": "5678",
          "firstName": "John",
          "lastName": "Doe",
          "url": "/lecturers/5678"
        }
      }
    ]
  }
}
```

⛔ Ovo se **ne smije** raditi jer profesori (`lecturers`) mogu biti definirani i samostalno, tj. ne pripadaju samo i jedino upisanim kolegijima studenta:

```http
GET /students/1234/enrolled-courses/1234/lecturers/5678
```

Upotrijebi `/collection/-/collection` za **sve** ugniježđene resurse **bez obzira na nadređene resurse**, ako je ta funkcionalnost potrebna.

- Rezultat je iste strukture kao i za `/collection/{id}/collection`.
- Primjer: `GET /students/-/enrolled-courses` dohvaća sve upise kolegije svih studenata.

Upotrijebi `/collection/-/collection/{id}` za **određeni** ugniježđeni resurs **bez obzira na nadređeni resurs**, ako je ta funkcionalnost potrebna.

- Rezultat je iste strukture kao i za `/collection/{id}/collection/{id}`.
- Primjer: `GET /students/-/enrolled-courses/1234` dohvaća upis kolegija s ID-jem 1234 bez obzira na studenta.

## 3. Akcije nad resursima

- **Izbjegavati glagole u URL-ovima**. Upotrijebiti HTTP metode za akcije nad resursima.
  - Primjer: `POST /users` umjesto `/users/create-user`
  - Primjer: `GET /users/{id}` umjesto `/users/get-user`
  - Primjer: `PATCH /users/{id}` umjesto `/users/update-user`
  - Primjer: `DELETE /users/{id}` umjesto `/users/delete-user`
- Za **akcije koje nisu CRUD** operacije tj. one koje su kompleksnije, upotrijebiti **HTTP metodu koja najbolje odgovara** željenoj akciji (najčešće `POST`), imenovati akciju **glagolom** i **dodati ju pod `actions`** u URL-u resursa (`/collection/{id}/actions/do-something`). Takve akcije treba **izbjegavati ako je moguće**.
  - Primjer: Za slanje emaila korisniku, upotrijebiti `POST /users/{id}/actions/send-email`.
  - Primjer: Za promjenu statusa studenta u alumni, upotrijebiti `POST /students/{id}/actions/graduate`.
  - Primjer: Za dohvat statusa studenta, upotrijebiti `GET /students/{id}` umjesto `GET /students/{id}/actions/get-status` jer podaci studenta sigurno sadrže i njegov status.
- **Akcije koje nisu CRUD** operacije se definiraju **isključivo glagolima**, a ako je neki glagol ujedno i imenica tada je potrebno upotrijebiti drugačiji izraz kako bi bilo jasno da se radi o akciji.
  - Primjer: `POST /users/{id}/actions/send-email` umjesto `POST /users/{id}/actions/email`.
  - Primjer: `POST /stocks/{id}/actions/trade-stock` umjesto `POST /stocks/{id}/actions/trade`.
- **Ako se akcija ne smije ponoviti više puta**, odnosno kada ista akcija ne rezultira uvijek istim rezultatom, tada je uz akciju potrebno **poslati** i `requestId` koji je **jedinstveni string** za otkrivanje dupliciranih zahtjeva. API bi trebao provjeriti je li akcija s istim `requestId`-jem već poslana i ako je, vratiti isti rezultat. Time se izbjegava dvostruka obrada iste akcije, i osigurava da će klijent dobiti rezultat ako ga je ranije propustio.

### 3.1. Dohvat resursa

---

| Akcija | Metoda | Request URL | Primjer |
|--------|--------|----------|---------|
| Dohvat jednog resursa po ID-ju | GET | `/collection/{id}` | `GET /users/1234` umjesto `GET /users?id=1234` |
| Dohvat više resursa s različitim ID-jevima | POST | `/collection/actions/batch-fetch` | `POST /users/actions/batch-fetch` s request bodyjem `{"ids": ["1", "2", "3"]}` |
| Dohvat svih resursa | GET | `/collection` | `GET /users` |
| Filtriranje | GET | `/collection?attribute=value` | `GET /users?name=John&age=20` |
| Filtriranje (poseban filter) | GET | `/collection/{filter}` | `GET /academic-year/current` za dohvat trenutne akademske godine |
| Sortiranje | GET | `/collection?orderBy=field:direction,field:direction` | `GET /users?orderBy=name:asc,age:desc` |
| Paginacija (offset) | GET | `/collection?pageSize=10&page=2` | `GET /users?pageSize=10&page=2` |
| Paginacija (cursor) | GET | `/collection?pageSize=10&pageToken=token` | `GET /users?pageSize=10&pageToken=token` |
| Pretraga | GET | `/collection/search?query=value` | `GET /users/search?query=John` |
| Rad s resursom trenutnog korisnika (opcionalno) | GET | `/collection/me` | `GET /students/me/enrolled-courses` |

Primjer uporabe kombinacije akcija:

```http
GET /users?name=John&age=20&orderBy=name:asc,age:desc&pageSize=10&page=2
```

Ako primijenjeni filtri ili parametri paginacije **ne daju rezultate**, API tada **mora vratiti praznu listu**. Na primjer.:

```json
{
  "data": {
    "users": []
  }
  "pagination": {
    "currentPage": 1,
    "pageSize": 0,
    "totalPages": 0,
    "totalSize": 0
  }
}
```

#### 3.1.1. Filtriranje

Filtriranje se može upotrijebiti za dohvaćanje resursa koji zadovoljavaju određene kriterije:

- Kriteriji za filtriranje se šalju kao query parametri u formatu `field=value`. Na primjer, `/users?name=John`.
- Ako je potrebno filtrirati po rasponima, upotrebljavaju se `min` i `max` prefiksi. Na primjer, `/users?minAge=18&maxAge=30`.
- Za uključivanje više vrijednosti, upotrebljava se zarez. Na primjer, `/users?role=admin,user`.
- Za filtriranje pomoću boolean vrijednosti, upotrebljava se `true` i `false`. Na primjer, `/users?isActive=true`.

#### 3.1.2. Sortiranje

Sortiranje se može upotrebljavati za dohvaćanje resursa sortiranih prema određenom kriteriju:

- Za sortiranje se uvijek upotrebljava query parametar `orderBy`, a podaci za sortiranje se šalju u formatu `field:direction`, gdje je `field` naziv atributa po kojem se sortira, a `direction` smjer sortiranja (`asc` za uzlazno i `desc` za silazno).
- Sintaksa za sortiranje je sljedeća: `/collection?orderBy=field1:direction,field2:direction`. Na primjer, `/users?orderBy=name:asc,age:desc` za sortiranje po imenu uzlazno i dobi silazno.

Sortiranje uvijek treba biti **stabilno**. To znači da ako dva resursa imaju istu vrijednost po kojoj se sortira, mora biti jednoznačno određeno kojim redoslijedom se pojavljuju. To se postiže tako da se dodaju sekundarni kriteriji sortiranja. Na primjer, ako se sortira po imenu, a dva resursa imaju isto ime, tada se sortira i po ID-ju.

#### 3.1.3. Paginacija

**U svakoj dinamičnoj ili većoj kolekciji resursa mora biti omogućena paginacija.** To isključuje kolekcije koje se gotovo nikad ne mijenjaju, poput statusa, tipova i vrsta. Razlog za to je da sve veće kolekcije moraju u jednom trenutku imati paginaciju kako bi se smanjila količina podataka koja se prenosi i kako bi se smanjilo opterećenje servera. A budući da je dodavanje paginacije breaking change, ona se mora dodati od početka.

Paginacija je breaking change zato što bez parametra količine rezultata klijent dobiva onoliko rezultata koliko server defaultno vraća, zbog čega klijent misli da je dobio sve rezultate kad zapravo nije.

Na API-ju **mora biti postavljen defaultni broj rezultata po stranici**, a klijent može postaviti željeni broj rezultata po stranici koji ne smije biti veći od **maksimalnog broja rezultata definiranog na API-ju**.

Kod paginacije se treba se primijeniti princip **stabilnosti sortiranja** kako bi se osigurao uvijek jednak redoslijed rezultata. Također je potrebno uz za svaku sljedeću stranicu **slati primijenjene filtre i sortiranje**.

Paginacija može biti ostvarena na dva osnovna načina:

| Metoda | Opis | Primjer | Rezultat | Prednosti | Nedostaci |
|--------|------|---------|----------|-----------|-----------|
| **Offset-based paginacija** | Klijent šalje broj rezultata po stranici i broj stranice koju želi dohvatiti. | `GET /users?pageSize=10&page=2` | - rezultati </br> - trenutna stranica (`currentPage`) </br> - broj rezultata na trenutnoj stranici (`pageSize`) </br> - ukupan broj stranica (`totalPages`) </br> - ukupan broj rezultata (`totalSize`) | - jednostavno za implementaciju | - neefikasno za velike kolekcije </br> - moguće preskakanje/dupliciranje rezultata ako dođe do promjene u podacima |
| **Cursor-based paginacija** | Klijent šalje broj rezultata po stranici i token koji označava trenutnu poziciju u kolekciji (na prvom dohvatu podataka nema tokena). | `GET /users?pageSize=10&pageToken=token` | - rezultati </br> - sljedeći token (`nextPageToken`) ako ima još rezultata, inače `null` kao vrijednost tokena </br> - broj rezultata na trenutnoj "stranici" (`pageSize`) | - efikasno za velike kolekcije </br> - nema preskakanja rezultata | - složenija implementacija </br> - prethodna stranica se pohranjuje na klijentu |

**Page token** je identifikator koji označava trenutnu poziciju u kolekciji. On može biti, primjerice, ID posljednjeg rezultata na trenutnoj stranici. Page token mora biti **kriptiran** i kodiran u `base64` formatu kako bi se omogućila promjena implementacije paginacije bez utjecaja na klijente. Page token se šalje kao query parametar u zahtjevu za dohvat sljedeće stranice, a server vraća novi page token ako ima još rezultata.

Postoje još neki pristupi paginaciji poput bilježenja vremena inicijalnog dohvata te zatim dohvata snapshota podataka koji su stvoreni ili zadnji put mijenjani prije tog vremena, čime se može osigurati konzistentnost podataka. Međutim, takav pristup je složeniji i manje efikasan te nema mogućnost prikaza najnovijih podataka.

U svakom slučaju uz rezultate paginacije moraju se vratiti i metapodaci o paginaciji:

```json
{
  "data": {
    "users": [
      {
        "id": "1",
        "name": "John Doe"
      },
      ...
    ]
  },
  "pagination": {
    "currentPage": 1,
    "pageSize": 10,
    "totalPages": 5,
    "totalSize": 50
  }
}
```

ili...

```json
{
  "data": {
    "users": [
      {
        "id": "1",
        "name": "John Doe"
      },
      ...
    ]
  },
  "pagination": {
    "nextPageToken": "token",
    "pageSize": 10
  }
}
```

## 4. HTTP metode

### 4.1. GET

---

> Za dohvat resursa (READ).

Mogući statusni kodovi i sadržaj odgovora:

| Statusni kod | Opis | Response body | Response headers |
|--------------|------|---------------|------------------|
| `200 OK` | Dohvat je uspješno izvršen. | Resurs | - |
| `202 Accepted` | Zahtjev je prihvaćen, ali nije završen. | - | - |
| `304 Not Modified` | Resurs nije promijenjen od zadnjeg upita (za uštedu bandwidtha). | - | - |
| `400 Bad Request` | Zahtjev nije ispravan. | Greška | - |
| `401 Unauthorized` | Korisnik nije autentificiran. | Greška | `WWW-Authenticate` s popisom načina autentikacije |
| `403 Forbidden` | Korisnik nema dozvolu za pristup resursu. | Greška | - |
| `404 Not Found` | Resurs nije pronađen (ili korisnik nema dozvolu). | Greška | - |
| `405 Method Not Allowed` | Metoda nije dozvoljena za traženi resurs. | Greška | `Allow` s popisom dozvoljenih metoda |
| `406 Not Acceptable` | Traženi format odgovora nije podržan (npr. zatražen XML umjesto JSON-a). | Greška | - |
| `429 Too Many Requests` | Premašen limit zahtjeva. | Greška | - |
| `500 Internal Server Error` | Greška na serveru. | Greška | - |
| `503 Service Unavailable` | Servis nije dostupan. | Greška | - |
| `504 Gateway Timeout` | Vrijeme odgovora je isteklo. | Greška | - |

**NAPOMENE:**

- Kada se dohvaća resurs koji zahtijeva autorizaciju, a korisnik nije autoriziran, mora se vratiti statusni kod `403 Forbidden`, osim u slučaju da bi samo odavanje informacije postoji li resurs moglo predstavljati kršenje privatnosti ili narušavanje sigurnosti.

### 4.2. POST

---

> Za kreiranje resursa (CREATE).

Mogući statusni kodovi i sadržaj odgovora:

| Statusni kod | Opis | Response body | Response headers |
|--------------|------|---------------|------------------|
| `200 OK` | Akcija je uspješno izvršena. | Resurs / Rezultat akcije | - |
| `201 Created` | Resurs je uspješno kreiran. | Resurs | `Location` s lokacijom novokreiranog resursa |
| `202 Accepted` | Zahtjev je prihvaćen, ali nije završen. | - | - |
| `204 No Content` | Akcija je uspješno izvršena (rezultat nije potrebno vratiti klijentu). | - | - |
| `400 Bad Request` | Zahtjev nije ispravan. | Greška | - |
| `401 Unauthorized` | Korisnik nije autentificiran. | Greška | `WWW-Authenticate` s popisom načina autentikacije |
| `403 Forbidden` | Korisnik nema dozvolu za pristup resursu. | Greška | - |
| `404 Not Found` | Resurs nije pronađen (ili korisnik nema dozvolu). | Greška | - |
| `405 Method Not Allowed` | Metoda nije dozvoljena za traženi resurs. | Greška | `Allow` s popisom dozvoljenih metoda |
| `406 Not Acceptable` | Traženi format odgovora nije podržan (npr. zatražen XML umjesto JSON-a). | Greška | - |
| `409 Conflict` | Resurs već postoji. | Greška | - |
| `415 Unsupported Media Type` | Tip medija nije podržan (primjerice klijent šalje XML umjesto JSON-a). | Greška | - |
| `429 Too Many Requests` | Premašen limit zahtjeva. | Greška | - |
| `500 Internal Server Error` | Greška na serveru. | Greška | - |
| `503 Service Unavailable` | Servis nije dostupan. | Greška | - |
| `504 Gateway Timeout` | Vrijeme odgovora je isteklo. | Greška | - |
| `507 Insufficient Storage` | Nedovoljno prostora za pohranu. | Greška | - |

**NAPOMENE:**

- Pri kreiranju resursa potrebno je poslati podatke u formatu JSON. Za podatke koje kreira server, kao što je ID resursa, u dokumentaciji treba jasno naznačiti da ih kreira server (npr. Output only).

### 4.3. PATCH

---

> Za ažuriranje resursa (UPDATE).

Mogući statusni kodovi i sadržaj odgovora:

| Statusni kod | Opis | Response body | Response headers |
|--------------|------|---------------|------------------|
| `200 OK` | Ažuriranje je uspješno izvršeno. | Resurs | - |
| `202 Accepted` | Zahtjev je prihvaćen, ali nije završen. | - | - |
| `400 Bad Request` | Zahtjev nije ispravan. | Greška | - |
| `401 Unauthorized` | Korisnik nije autentificiran. | Greška | `WWW-Authenticate` s popisom načina autentikacije |
| `403 Forbidden` | Korisnik nema dozvolu za pristup resursu. | Greška | - |
| `404 Not Found` | Resurs nije pronađen (ili korisnik nema dozvolu). | Greška | - |
| `405 Method Not Allowed` | Metoda nije dozvoljena za traženi resurs. | Greška | `Allow` s popisom dozvoljenih metoda |
| `406 Not Acceptable` | Traženi format odgovora nije podržan (npr. zatražen XML umjesto JSON-a). | Greška | - |
| `409 Conflict` | Resurs nije moguće ažurirati zbog konflikta. | Greška | - |
| `415 Unsupported Media Type` | Tip medija nije podržan (primjerice klijent šalje XML umjesto JSON-a). | Greška | - |
| `429 Too Many Requests` | Premašen limit zahtjeva. | Greška | - |
| `500 Internal Server Error` | Greška na serveru. | Greška | - |
| `503 Service Unavailable` | Servis nije dostupan. | Greška | - |
| `504 Gateway Timeout` | Vrijeme odgovora je isteklo. | Greška | - |
| `507 Insufficient Storage` | Nedovoljno prostora za pohranu. | Greška | - |

**NAPOMENE:**

- Metoda `PATCH` se upotrebljava za parcijalno ažuriranje resursa. Podaci za ažuriranje moraju biti poslani u formatu JSON u request bodyju.
- Svi podaci koji su navedeni se ažuriraju, a svi ostali ostaju nepromijenjeni. Ako se neki atribut želi postaviti na `null`, tada se mora poslati `null` kao vrijednost.
- Ako se uz ažuriranje želi napraviti i nešto drugo, to je moguće napraviti u istom requestu kroz query parametre. Na primjer, `PATCH /students/1234?notify=true`.
- Ako se želi ažurirati resurs na način da se dogode još neke promjene u sustavu, tada se treba napraviti posebna akcija pod `actions` u URL-u umjesto da se radi običan update. Na primjer, `POST /students/{id}/actions/graduate`.

### 4.4. PUT

---

> Za zamjenu resursa (REPLACE).

**VAŽNO:** Metodu `PUT` treba koristiti samo kada je potrebno zamijeniti cijeli resurs. Ako je potrebno ažurirati samo dio resursa, tada se treba koristiti `PATCH`.

Mogući statusni kodovi i sadržaj odgovora:

| Statusni kod | Opis | Response body | Response headers |
|--------------|------|---------------|------------------|
| `200 OK` | Ažuriranje je uspješno izvršeno. | Resurs | - |
| `202 Accepted` | Zahtjev je prihvaćen, ali nije završen. | - | - |
| `400 Bad Request` | Zahtjev nije ispravan. | Greška | - |
| `401 Unauthorized` | Korisnik nije autentificiran. | Greška | `WWW-Authenticate` s popisom načina autentikacije |
| `403 Forbidden` | Korisnik nema dozvolu za pristup resursu. | Greška | - |
| `404 Not Found` | Resurs nije pronađen (ili korisnik nema dozvolu). | Greška | - |
| `405 Method Not Allowed` | Metoda nije dozvoljena za traženi resurs. | Greška | `Allow` s popisom dozvoljenih metoda |
| `406 Not Acceptable` | Traženi format odgovora nije podržan (npr. zatražen XML umjesto JSON-a). | Greška | - |
| `409 Conflict` | Resurs nije moguće ažurirati zbog konflikta. | Greška | - |
| `415 Unsupported Media Type` | Tip medija nije podržan (primjerice klijent šalje XML umjesto JSON-a). | Greška | - |
| `429 Too Many Requests` | Premašen limit zahtjeva. | Greška | - |
| `500 Internal Server Error` | Greška na serveru. | Greška | - |
| `503 Service Unavailable` | Servis nije dostupan. | Greška | - |
| `504 Gateway Timeout` | Vrijeme odgovora je isteklo. | Greška | - |
| `507 Insufficient Storage` | Nedovoljno prostora za pohranu. | Greška | - |

**NAPOMENE:**

- Ovu metodu treba izbjegavati zbog backwards compatibilityja, jer ako se dodaju novi atributi u resurs, a klijent ne zna za njih, tada će ih zamijeniti s `null`.
- Metoda `PUT` se upotrebljava za zamjenu resursa. Podaci za zamjenu moraju biti poslani u formatu JSON u request bodyju.
- Ako se uz zamjenu resursa želi napraviti i nešto drugo, to je moguće napraviti u istom requestu kroz query parametre. Na primjer, `PUT /students/1234?notify=true`.
- Svi podaci koji su navedeni se zamjenjuju, a svi ostali se brišu. Ako se neki atribut želi postaviti na `null`, tada se mora poslati `null` kao vrijednost, ili ga se može izostaviti pa će biti prebrisan (postavljen na `null`).

### 4.5. DELETE

---

> Za brisanje resursa (DELETE).

Mogući statusni kodovi i sadržaj odgovora:

| Statusni kod | Opis | Response body | Response headers |
|--------------|------|---------------|------------------|
| `202 Accepted` | Zahtjev je prihvaćen, ali nije završen. | - | - |
| `204 No Content` | Resurs je uspješno obrisan. | - | - |
| `400 Bad Request` | Zahtjev nije ispravan. | Greška | - |
| `401 Unauthorized` | Korisnik nije autentificiran. | Greška | `WWW-Authenticate` s popisom načina autentikacije |
| `403 Forbidden` | Korisnik nema dozvolu za pristup resursu. | Greška | - |
| `404 Not Found` | Resurs nije pronađen (ili korisnik nema dozvolu). | Greška | - |
| `405 Method Not Allowed` | Metoda nije dozvoljena za traženi resurs. | Greška | `Allow` s popisom dozvoljenih metoda |
| `409 Conflict` | Resurs nije moguće izbrisati zbog konflikta. | Greška | - |
| `429 Too Many Requests` | Premašen limit zahtjeva. | Greška | - |
| `500 Internal Server Error` | Greška na serveru. | Greška | - |
| `503 Service Unavailable` | Servis nije dostupan. | Greška | - |
| `504 Gateway Timeout` | Vrijeme odgovora je isteklo. | Greška | - |

**NAPOMENE:**

- Metoda `DELETE` se upotrebljava za brisanje resursa.
- Metoda `DELETE` nema request body.
- Ako metoda `DELETE` odmah izbriše resurs, tada mora vratiti statusni kod `204 No Content` bez response bodyja.
- Ako je brisanje resursa duže od normalnog vremena odgovora, tada se vraća statusni kod `202 Accepted` zajedno s long-running operation.
- Ako brisanje resursa nije pravo brisanje nego promjena statusa ili zastavice, tada se vraća statusni kod `200 OK` i promijenjeni resurs.
- Kada se koristi HTTP metoda `DELETE` za brisanje resursa koji ne postoji, mora se vratiti statusni kod `404 Not Found`.

### 4.6. OPTIONS

---

> Za dohvat informacija o dozvoljenim metodama za resurs.

Mogući statusni kodovi i sadržaj odgovora:

| Statusni kod | Opis | Response body | Response headers |
|--------------|------|---------------|------------------|
| `200 OK` | Dohvat je uspješno izvršen. | - | `Allow` s popisom dozvoljenih metoda |
| `401 Unauthorized` | Korisnik nije autentificiran. | Greška | `WWW-Authenticate` s popisom načina autentikacije |
| `403 Forbidden` | Korisnik nema dozvolu za pristup resursu. | Greška | - |
| `404 Not Found` | Resurs nije pronađen. | Greška | - |
| `500 Internal Server Error` | Greška na serveru. | Greška | - |
| `503 Service Unavailable` | Servis nije dostupan. | Greška | - |
| `504 Gateway Timeout` | Vrijeme odgovora je isteklo. | Greška | - |

**NAPOMENE:**

- Metoda `OPTIONS` se upotrebljava za dohvat informacija o dozvoljenim metodama za resurs.
- U response headers se šalje `Allow` s popisom dozvoljenih metoda za traženi resurs.
- Ovu metodu nije potrebno implementirati, ali omogućava klijentima da saznaju koje metode su dozvoljene za traženi resurs.

### 4.7. HEAD

---

> Za dohvat informacija o resursu bez samog resursa.

Mogući statusni kodovi i sadržaj odgovora:

| Statusni kod | Opis | Response body | Response headers |
|--------------|------|---------------|------------------|
| `200 OK` | Dohvat je uspješno izvršen. | - | - |
| `401 Unauthorized` | Korisnik nije autentificiran. | Greška | `WWW-Authenticate` s popisom načina autentikacije |
| `403 Forbidden` | Korisnik nema dozvolu za pristup resursu. | Greška | - |
| `404 Not Found` | Resurs nije pronađen. | Greška | - |
| `500 Internal Server Error` | Greška na serveru. | Greška | - |
| `503 Service Unavailable` | Servis nije dostupan. | Greška | - |
| `504 Gateway Timeout` | Vrijeme odgovora je isteklo. | Greška | - |

**NAPOMENE:**

- Metoda `HEAD` se upotrebljava za dohvat informacija o resursu bez samog resursa.
- Ova metoda se koristi za dohvat informacija o resursu, kao što su veličina resursa, tip medija, datum zadnje promjene i sl.
- Ovu metodu nije potrebno implementirati, ali omogućava klijentima da saznaju informacije o resursu bez potrebe za dohvatom cijelog resursa.

## 5. Autentikacija

Za autentikaciju korisnika koristi se JWT (JSON Web Token).

Kada se korisnik autentificira, server mora vratiti JWT koji sadrži korisnikove podatke.

Ako korisnik nije autentificiran, server mora vratiti statusni kod `401 Unauthorized`.

Ako JWT nije ispravan, server mora vratiti statusni kod `401 Unauthorized`.

### 3.1. Primjer autentikacije

- Autentikacija predstavlja proces provjere identiteta korisnika.
- Kada se korisnik autentificira, server mora vratiti JWT.

Primjer odgovora:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxLCJ1c2VyX25hbWUiOiJKb2huIERvZSIsInVzZXJfZW1haWwiOiJqb2huQGVtYWlsLmNvbSJ9.3JVAJ9Jf
}
```

- Ako resurs zahtijeva provjeru identiteta korisnika, korisnik mora poslati JWT.
- Ako korisnik nije autentificiran, a resurs nije javno dostupan, server mora vratiti statusni kod `401 Unauthorized`.
- Ako je korisnik autentificiran, a JWT nije ispravan, server mora vratiti statusni kod `401 Unauthorized`.
- Ako je korisnik autentificiran, ali nema potrebnu ulogu za pristup resursu, server mora vratiti statusni kod `403 Forbidden`.

## 6. Autorizacija

- Autorizacija je proces određivanja prava pristupa resursima.
- Korisnik mora imati odgovarajuću ulogu kako bi pristupio određenom resursu.
- Ako korisnik nema odgovarajuću ulogu, server mora vratiti statusni kod `403 Forbidden`, osim u slučaju da bi samo odavanje informacije postoji li resurs moglo predstavljati kršenje privatnosti ili narušavanje sigurnosti. Tada se mora vratiti statusni kod `404 Not Found`.

### 6.1. Primjer autorizacije

Kada korisnik pokuša pristupiti resursu koji zahtijeva autorizaciju, mora poslati JWT.

Primjer zahtjeva:

```http
GET /users
Authorization
Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxLCJ1c2VyX25hbWUiOiJKb2huIERvZSIsInVzZXJfZW1haWwiOiJqb2huQGVtYWlsLmNvbSJ9.3JVAJ9Jf
```

## 7. Logiranje

Svi zahtjevi i statusni kodovi odgovora mogu biti logirani.

Logovi mogu sadržavati sljedeće informacije:

- vrijeme zahtjeva
- URL zahtjeva
- HTTP metoda zahtjeva
- query parametri zahtjeva
- vrijeme odgovora
- statusni kod odgovora
- identifikator korisnika koji je poslao zahtjev (ako je korisnik autentificiran)
- IP adresa klijenta koji je poslao zahtjev
- user-agent klijenta koji je poslao zahtjev (npr. browser ili aplikacija, verzija i sl.)
- poruka greške (ako je došlo do greške)
- vrijeme izvođenja zahtjeva na bazi podataka (ako je potrebno)

## 8. Greške

Server mora vraćati odgovarajuće statusne kodove i poruke grešaka.

Statusni kodovi i poruke grešaka moraju biti informativni i korisni.

### 8.1. Greške u zahtjevu

---

Može se dogoditi da klijent pošalje zahtjev koji nije sasvim neispravan, ali nije u potpunosti ispravan. Na primjer, kod paginacije klijent može poslati zahtjev za dohvat stranice koja ne postoji. Tada treba vratiti statusni kod `200 OK` uz prazan skup podataka (vidi upute za dohvat resursa). Ili kada klijent zatraži više rezultata po stranici nego što je dozvoljeno. Tada treba uzeti u obzir da klijent ne zna koliko rezultata po stranici je dozvoljeno, pa je potrebno izvršiti paginaciju s najvećim dozvoljenim brojem rezultata po stranici.

Ako se pak radi o djelomičnom ažuriranju resursa (metoda `PATCH`) i klijent pošalje podatke koji ne postoje, tada bi bilo dobro ažurirati samo one podatke koji postoje, a ostale ignorirati. Razlog tome je što se može dogoditi da su neki atributi uklonjeni, ali klijent ne zna za tu promjenu.

### 8.2. Poruke grešaka

---

Greške moraju sadržavati informacije o uzroku greške i o načinu rješavanja greške.

Kada pišeš poruku greške prati sljedeće smjernice:

- Nemoj pretpostavljati da je korisnik upoznat s API-jem. Korisnik može biti developer klijentske aplikacije, administrativni korisnik, IT podrška ili običan korisnik aplikacije.
- Nemoj pretpostavljati da korisnik zna išta o usluzi ili da je upoznat s terminologijom.
- Kada god je to moguće, poruke greške trebaju biti napisane na način da tehnički stručan korisnik može ispraviti problem.
- Poruka greške mora biti kratka.

**Obavezni** atributi greške su **`code` i `message`**, ostali atributi su opcionalni:

- `target` koji označava na koji dio zahtjeva se greška odnosi
- `details` koji sadrži dodatne informacije o grešci

Struktura greške je sljedeća:

```json
{
  "error": {
    "code": "badRequest",
    "message": "Multiple errors in ContactInfo data",
    "target": "contactInfo",
    "details": [
      {
        "code": "nullValue",
        "target": "phoneNumber",
        "message": "Phone number must not be null"
      },
      {
        "code": "nullValue",
        "target": "lastName",
        "message": "Last name must not be null"
      },
      {
        "code": "malformedValue",
        "target": "address",
        "message": "Address is not valid"
      }
    ]
  }
}
```

## 9. Caching

Caching se može upotrijebiti za pohranu odgovora na serveru ili na klijentu, u svrhu smanjenja opterećenja servera i ubrzanja dohvata podataka.

Server upravlja svojim cacheom i daje upute klijentu kako da upravlja svojim cacheom. To može činiti na sljedeće načine:

- Upotrebom HTTP zaglavlja `Cache-Control` za upravljanje cacheom na klijentu. Slijedi opis mogućih vrijednosti `Cache-Control` zaglavlja:
  - `public` - odgovor se može pohraniti u cache i dijeliti s drugim korisnicima
  - `private` - odgovor se može pohraniti u cache samo na klijentu
  - `no-cache` - odgovor se ne može pohraniti u cache
  - `no-store` - odgovor se ne smije pohraniti u cache
  - `max-age` - maksimalno vrijeme u sekundama na koje se odgovor može pohraniti u cache
  - `s-maxage` - maksimalno vrijeme u sekundama na koje se odgovor može pohraniti u cache na shared cacheu
  - `stale-while-revalidate` - vrijeme u sekundama na koje se odgovor može koristiti iz cachea nakon isteka `max-age` dok se odgovor revalidira
  - `stale-if-error` - vrijeme u sekundama na koje se odgovor može koristiti iz cachea ako je došlo do greške prilikom revalidacije
  - `must-revalidate` - klijent mora revalidirati odgovor s serverom prije dohvata iz cachea
  - `proxy-revalidate` - proxy mora revalidirati odgovor s serverom prije dohvata iz cachea
  - `immutable` - odgovor se neće mijenjati tijekom vremena
  - `no-transform` - proxy ne smije mijenjati odgovor
  - `only-if-cached` - klijent traži odgovor samo iz cachea
- Upotrebom HTTP zaglavlja `ETag` za provjeru promjena na resursu:
  1. Klijent šalje inicijalni zahtjev za resursom.
  2. Server vraća resurs s `ETag` zaglavljem.
  3. Klijent pohranjuje odgovor i njegov `ETag`.
  4. Klijent šalje novi zahtjev za resursom s `ETag` zaglavljem.
  5. Ako se resurs nije promijenio, server vraća statusni kod `304 Not Modified`.

## 10. Statusni kodovi

- `200 OK` - dohvat je uspješno izvršen
- `201 Created` - resurs je uspješno kreiran
- `202 Accepted` - zahtjev je prihvaćen, ali nije završen
- `204 No Content` - resurs je uspješno obrisan
- `304 Not Modified` - resurs nije promijenjen (za uštedu bandwidtha)
- `400 Bad Request` - zahtjev nije ispravan
- `401 Unauthorized` - korisnik nije autentificiran
- `403 Forbidden` - korisnik nema dozvolu za pristup resursu
- `404 Not Found` - resurs nije pronađen (ili korisnik nema dozvolu)
- `405 Method Not Allowed` - metoda nije podržana
- `406 Not Acceptable` - traženi format odgovora nije podržan (npr. zatražen XML umjesto JSON-a)
- `409 Conflict` - resurs već postoji
- `415 Unsupported Media Type` - tip medija nije podržan (primjerice XML umjesto JSON-a)
- `429 Too Many Requests` - premašen limit zahtjeva
- `500 Internal Server Error` - greška na serveru
- `503 Service Unavailable` - servis nije dostupan
- `504 Gateway Timeout` - vrijeme odgovora je isteklo
- `507 Insufficient Storage` - nedovoljno prostora za pohranu

## 11. Dokumentacija

Treba biti jasno naznačeno tko kreira ID resursa, klijentska aplikacija ili server. Na primjer, naziv datoteke kreira aplikacija, a ID kreira server.
Također, ako su neki atributi output only, to treba biti jasno naznačeno (kako?).

## 12. Pojmovnik

- **atribut** - svojstvo resursa
- **collection** - skup resursa
- **CRUD** - Create, Read, Update, Delete
- **HTTP metode** - metode koje se koriste za komunikaciju između klijenta i servera
- **JWT** - JSON Web Token (token za autentikaciju)
- **message field** - svaki podatak koji se šalje u requestu ili responseu kroz path, query parametre ili request i response body
- **parametar** - dodatni podatak koji se šalje u URL-u
- **resurs** - entitet koji se može dohvatiti, kreirati, ažurirati ili brisati
- **URL** - Uniform Resource Locator (adresa resursa)

## 13. Verzioniranje

API bi trebao imati glavni URL koji se sastoji od verzije API-ja. Na primjer, `https://example.com/api/v1`.

Ako bi promjena na API-ju mogla narušiti kompatibilnost s klijentima, tada se mora napraviti nova verzija API-ja. Inače je moguće samo nadograditi postojeći API.

Non-breaking promjene uključuju:

- dodavanje novih resursa (endpointova)
- dodavanje novih atributa u response bodyju
- dodavanje novih metoda nad resursima
- dodavanje novih akcija
- dodavanje novih opcionalnih request parametara koji ne mijenjaju logiku obrade zahtjeva ako nisu poslani
- dodavanje novih statusnih kodova
- dodavanje novih enum vrijednosti
- dodavanje novih poruka grešaka
- izmjena postojećih poruka grešaka
- izmjene koje ne mijenjaju logiku obrade zahtjeva

Ukratko, drži se sljedećih pravila:

- nemoj ništa uklanjati
- nemoj mijenjati nazive
- nemoj mijenjati logiku obrade zahtjeva
- nemoj mijenjati hijerarhijsku strukturu JSON-a odgovora
- opcionalni parametri ne smiju postati obavezni
- što god dodaješ mora biti opcionalno

## 14. Long-running operations

Ako je operacija koja se izvršava na serveru duža od normalnog vremena odgovora, tada se mora vratiti statusni kod `202 Accepted` zajedno s long-running operation.

Long-running operation je resurs koji sadrži informacije o statusu operacije, kao što su `status`, `progress`, `result` i sl.

Primjer long-running akcije:

```json
{
  "status": "running",
  "progress": 50,
  "result": null
}
```

## 15. Deprecation

Kada se API mijenja, a neke metode ili resursi postaju zastarjeli, tada se mora obavijestiti korisnike o depriciranim resursima i metodama.

To se može napraviti slanjem [`Deprecation`](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-deprecation-header) HTTP zaglavlja u odgovoru zajedno s UNIX timestampom od kada je resurs ili metoda zastarjela. Uz `Deprecation` zaglavlje može se poslati i `Sunset` zaglavlje koje sadrži ljudski čitljiv datum kada će resurs ili metoda biti uklonjeni, te `Link` zaglavlje s poveznicom na dokumentaciju o promjenama.

Primjer HTTP zaglavlja:

```http
HTTP/1.1 200 OK
Content-Type: application/json
Deprecation: @1688169599
Sunset: Fri, 31 Dec 2030 23:59:59 GMT
Link: <https://example.com/docs/v2>; rel="deprecation" type:"text/html"
```

## TODO

- linkovi na druge resurse: <https://web.archive.org/web/20171202064047id_/http://www.si-journal.org/index.php/JSI/article/viewFile/290/298>
- long-running operations:
  - <https://github.com/microsoft/api-guidelines/blob/vNext/graph/patterns/long-running-operations.md>
  - <https://cloud.google.com/document-ai/docs/long-running-operations>
  - <https://google.aip.dev/151>
  - <https://cloud.ibm.com/docs/api-handbook?topic=api-handbook-long-running-operations>
- greške u zahtjevima
