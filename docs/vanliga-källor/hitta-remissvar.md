# Hitta remissvar

[Hitta remissvar](https://datalabb.statskontoret.se/remissai/) är en tjänst som utvecklades av Statskontorets datalabb 2022-2023. Tjänsten visar SOU, Ds och remissvar och gör det enkelt att bläddra med ordsök, filtrering efter en mängd metadata och semantisk sök (dvs. liknande text med liknande mening).

I gränssnittet kan man också bläddra i utredningarnas förslag och konsekvenskapitel.

## Vilken data finns?

Och det är precis dessa datamängder som går att hämta maskinellt med Hitta remissvars privat API:

- Statliga offentliga utredningar (SOU) och departementsskrivelser (Ds) från 2002 och framåt med strukturerade metadata (departement, beteckningsnummer, publiceringsdatum)
- Remissvar som tjänsten har samlat in från regeringen.se
- Utredningarnas förslag och konsekvenskapitel, samt statistik om antalet förslag och inkomna remissvar (de som publiceras på regeringen.se, ej fullständig)

Det finns ingen garanti för att datamängderna är fullständiga eller uppdaterade.

## Privat API

Webbgränssnittet använder ett internt REST-API som också går att anropa maskinellt. Det är inte ett officiellt dokumenterat, versionshanterat API; adresser och svarsformat kan ändras. Nedan visas fyra sökanrop som använder HTTP GET och returnerar JSON.

`offset` är antalet träffar att hoppa över, inte ett sidonummer. Med 50 träffar per sida används alltså 0, 50, 100 och så vidare. Det fasta `null` efter beteckningsnumret är platsen för titelsökning. `Utredningar` saknar sökordsparametern som finns i de övriga tre anropen. Använd `null` för textfilter som inte ska begränsa sökningen och URL-koda filtervärden, till exempel `SOU 2020:34` som `SOU%202020%3A34`.

```text
# Sök i utredningarnas lista
https://datalabb.statskontoret.se/remissai/api/Search/Utredningar/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{antal_resultat_per_sida}/{offset}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Utredningar/SOU%202020%3A34/null/true/true/50/0/2002/2026/Statskontoret/Senaste

# Sök i utredningarnas förslag:
https://datalabb.statskontoret.se/remissai/api/Search/Forslag/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{sökord}/{antal_resultat_per_sida}/{offset}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Forslag/SOU%202020%3A34/null/true/true/fritidshem/50/0/2002/2026/Statskontoret/Senaste

# Sök i utredningarnas konsekvenskapitel
https://datalabb.statskontoret.se/remissai/api/Search/Konsekvenser/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{sökord}/{antal_resultat_per_sida}/{offset}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Konsekvenser/SOU%202020%3A34/null/true/true/fritidshem/50/0/2002/2026/Statskontoret/Senaste

# Sök i remissvaren
https://datalabb.statskontoret.se/remissai/api/Search/Remissvar/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{sökord}/{antal_resultat_per_sida}/{offset}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Remissvar/SOU%202020%3A34/null/true/true/fritidshem/50/0/2002/2026/Statskontoret/Senaste
```

Det finns även anrop för semantisk matchning. De kräver HTTP POST med `Content-Type: application/json` och ett underlag i anropets kropp. Enbart URL:en med GET ger HTTP 405. Underlaget innehåller textens embedding, alltså dess numeriska vektorrepresentation; det räcker inte att skicka enbart fritext.

Följande exempel kräver `curl` och `jq`. Det hämtar ett förslag, sparar hela JSON-objektet inklusive embedding i `forslag.json` och använder det för att hitta liknande förslag respektive konsekvenstext:

```bash
api_url='https://datalabb.statskontoret.se/remissai/api/Search'

# Hämta ett förslag som underlag för matchningen.
curl --fail --silent --show-error \
  "$api_url/Forslag/SOU%202020%3A34/null/true/true/fritidshem/1/0/2002/2026/null/Senaste" \
  | jq '.[0]' > forslag.json

# Hitta liknande förslag.
curl --fail --silent --show-error \
  --header 'Content-Type: application/json' \
  --data-binary @forslag.json \
  "$api_url/MatchaForslagMotForslag/1/true/true/null/1/0/2002/2026/null"

# Hitta konsekvenstext som liknar förslaget.
curl --fail --silent --show-error \
  --header 'Content-Type: application/json' \
  --data-binary @forslag.json \
  "$api_url/MatchaKonsekvenserMotForslag/1/true/true/null/1/0/2002/2026/null"
```

För att förstå vilka parametrar går att använda och vilket format datan kommer i rekommenderar jag att öppna sin webbläsares utvecklarverktyg (*Developer tools*) och titta i nätverksfliken (*Network*) för att se vilka anrop skickas när man använder gränssnittets olika funktioner och filter. Om du tycker att vissa detaljer borde tilläggas i denna dokumentation får du gärna skicka synpunkter eller ännu bättre förbättra sidan själv.
