# Hitta remissvar

[Hitta remissvar](https://datalabb.statskontoret.se/remissai/) är en tjänst som utvecklades av Statskontorets datalabb 2022-2023. Tjänsten visar SOU, Ds och remissvar och gör det enkelt att bläddra med ordsök, filtrering efter en mängd metadata och semantisk sök (dvs. liknande text med liknande mening).

I gränssnittet kan man också bläddra i utredningarnas förslag och konsekvenskapitel.

## Vilken data finns?

Och det är precis dessa datamängder som går att hämta maskinellt med Hitta remissvars privat API:

- Alla statliga utredningar (SOU) och departementsserien (Ds) sedan 2002 med strukturerade metadata (departement, beteckningsnummer, publiceringsdatum)
- Alla remissvar från regeringen.se
- Utredningarnas förslag och konsekvenskapitel, samt statistik om antalet förslag och inkomna remissvar (de som publiceras på regeringen.se, ej fullständig)

## Privat API

Även om det saknas dokumentation är det lätt att ladda ner maskinellt från Hitta remissvar eftersom tjänsten erbjuder ett REST-API med 4 sökanrop:

```bash
# Sök i utredningarnas lista
https://datalabb.statskontoret.se/remissai/api/Search/Utredningar/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{antal_resultat_per_sida}/{sidonummer}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Utredningar/SOU%202020%3A34/null/true/true/50/0/2002/2026/Statskontoret/Senaste

# Sök i utredningarnas förslag:
https://datalabb.statskontoret.se/remissai/api/Search/Forslag/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{sökord}/{inkludera_ds}/{antal_resultat_per_sida}/{sidonummer}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Forslag/SOU%202020%3A34/null/true/true/fritidshem/50/0/2002/2026/Statskontoret/Senaste

# Sök i utredningarnas konsekvenskapitel
https://datalabb.statskontoret.se/remissai/api/Search/Konsekvenser/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{sökord}/{inkludera_ds}/{antal_resultat_per_sida}/{sidonummer}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Konsekvenser/SOU%202020%3A34/null/true/true/fritidshem/50/0/2002/2026/Statskontoret/Senaste

# Sök i remissvaren
https://datalabb.statskontoret.se/remissai/api/Search/Remissvar/{beteckningsnummer}/null/{inkludera_sou}/{inkludera_ds}/{sökord}/{inkludera_ds}/{antal_resultat_per_sida}/{sidonummer}/{från_år}/{till_år}/{remissinstans}/{sortering}
# Exempel:
https://datalabb.statskontoret.se/remissai/api/Search/Remissvar/SOU%202020%3A34/null/true/true/fritidshem/50/0/2002/2026/Statskontoret/Senaste
```

Det finns även ett API för att göra en semantisk sökning från en paragraf till en annan i databasen. Här är några exempel:

```bash
# Matcha förslag mot förslag, exempel:
https://datalabb.statskontoret.se/remissai/api/Search/MatchaForslagMotForslag/1/true/true/null/50/0/2002/2026/null

# Matcha förslag mot konsekvenser, exempel:
https://datalabb.statskontoret.se/remissai/api/Search/MatchaForslagMotKonsekvenser/SOU%202026%3A21/-1/true/true/null/50/0/2002/2026/null
```

För att förstå vilka parametrar går att använda och vilket format datan kommer i rekommenderar jag att öppna sin webbläsares utvecklarverktyg (*Developer tools*) och titta i nätverksfliken (*Network*) för att se vilka anrop skickas när man använder gränssnittets olika funktioner och filter. Om du tycker att vissa detaljer borde tilläggas i denna dokumentation får du gärna skicka synpunkter eller ännu bättre förbättra sidan själv.
