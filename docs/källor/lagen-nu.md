# Lagen.nu

[Lagen.nu](https://lagen.nu) är en webbsida som drivs helt ideellt av Staffan Malmgren sedan 2004.

Webbplatsen samlar enorma mängder rättsinformation. Uppgifterna genomgår en rensningsprocess som gör att de ofta är bättre formaterade än i de officiella källorna. Dessutom sparar lagen.nu äldre versioner av lagarna, vilket gör det till den bästa källan för att se ändringar över tid utan att behöva extrahera dem manuellt från officiella PDF-dokument.

Systemet bygger på det öppna ramverket [Ferenda](https://ferenda.readthedocs.io/), som är utvecklat av samma upphovsman för att transformera juridisk information till strukturerad Linked Data (RDF).

## API och maskinläsbara format

Sajten erbjuder flera sätt att hämta data maskinellt. Eftersom den bygger på Linked Data-principer finns det flera format tillgängliga.

### Enskilda dokument i olika format

Man kan hämta enskilda dokument (författningar, domar, etc.) i olika strukturerade format genom att lägga till ett suffix eller en specifik sökväg till URL:en.

* **XHTML med RDFa:** Lägg till `.xhtml`. Detta ger dokumentets text med inbäddad metadata.
  * Exempel: `https://lagen.nu/2025:27.xhtml`
* **JSON:** Lägg till `.json`. Ger metadata och ofta en förenklad representation av dokumentet.
  * Exempel: `https://lagen.nu/2025:27.json`
* **RDF/XML:** Lägg till `.rdf` (eller `/data.rdf`). Detta ger den rena semantiska beskrivningen enligt RDF-standard.
  * Exempel: `https://lagen.nu/1998:204.rdf`
* **Turtle (Linked Data):** Lägg till `.ttl`. Detta ger en mer människläsbar RDF-representation i Turtle-format.
  * Exempel: `https://lagen.nu/1998:204.ttl`

### Sökning och bläddring via /api/

Webbplatsen har en dedikerad API-slutpunkt på [https://lagen.nu/api/](https://lagen.nu/api/) som returnerar data i JSON-format. Detta API används internt för sökfunktionen på webbplatsen.

#### Sök- och filterparametrar

Följande parametrar har verifierats fungera i det officiella API:et:

* **`q`**: Fritextsökning i alla indexerade dokument. (Krävs ofta för att få resultat.)
* **`basefile`**: Filtrera på dokumentets officiella ID (t.ex. `basefile=1998:204` för SFS-nummer). Ger ofta en unik träff.
* **`utgiven`**: Filtrera på utgivningsår (t.ex. `utgiven=2023`). Detta är det mest stabila sättet att söka på årtal.
* **`creator`**: Filtrera på utgivande myndighet. Kräver fullständig URI (t.ex. `creator=https://lagen.nu/org/2008/finansdepartementet`).
* **`repo`**: Filtrera på datakälla (t.ex. `repo=sfs` för författningar eller `repo=prop` för propositioner).
* **`_pageSize`**: Antal resultat per sida (max 100).
* **`_offset`**: Paginering för att hoppa över resultat.

#### RDF-baserade filter (Avancerat)

Man kan även använda längre RDF-baserade predikat som parametrar för vissa dokumenttyper (som kommittédirektiv):

* `rdf_type`: T.ex. `rdf_type=*Forordning` eller `rdf_type=*Kommittedirektiv`.
* `year-dcterms_issued`: Alternativt årsfilter för vissa typer.

**Verifierade exempel på anrop:**

* Hämta metadata för en specifik lag: `https://lagen.nu/api/?basefile=1998:204`
* Sök efter dokument om "energi" från 2022: `https://lagen.nu/api/?q=energi&utgiven=2022`
* Lista propositioner från 2023: `https://lagen.nu/api/?repo=prop&utgiven=2023`

### Fördjupning

För den som vill förstå exakt hur API:et fungerar eller hitta fler dolda parametrar kan man studera källkoden för projektet på GitHub:

* [staffanm/ferenda](https://github.com/staffanm/ferenda) – Det underliggande ramverket.

---
*Observera: Detta är ett inofficiellt API. Vid maskinell användning bör man visa hänsyn genom att använda en tydlig `User-Agent` och undvika att belasta servern onödigt hårt.*
