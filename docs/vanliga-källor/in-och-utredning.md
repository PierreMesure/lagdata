# In- och utredning

[In- och utredning](https://datalabb.statskontoret.se/sousok/) är en tjänst som utvecklades av Statskontorets datalabb 2021. Tjänsten visar SOU och Ds och gör det enkelt att bläddra med ordsök, filtrering efter viss metadata och att få statistik om hur ofta utredningar citerar varandra.

## Vilken data finns?

Man kan se vilka utredningar citerar en utredning (*in-referenser*) och vilka utredningar en utredning citerar (*ut-referenser*).

Tjänsten inkluderar alla SOU och Ds från Riksdagens öppna data men datan uppdateras ej kontinuerligt och de senaste dokumenten saknas.

## Privat API

Även om det saknas dokumentation är det lätt att ladda ner maskinellt från In- och utredning eftersom tjänsten erbjuder ett REST-API med 4 sökanrop:

```bash
# Sök i utredningarnas lista
https://datalabb.statskontoret.se/sousok/api/Documents/DocumentSearch/?search_string={sökord}&start_date={från_år}&end_date={till_år}&page_number={sidonummer}&sort={sortering}&doc_types={dokument_typ_som_inkluderas}
# Exempel:
https://datalabb.esv.se/sousok/api/Documents/DocumentSearch/?search_string=klimat&start_date=1990&end_date=2025&page_number=0&sort=rel&doc_types=SOU,Ds
```

För att förstå vilka parametrar går att använda och vilket format datan kommer i rekommenderar jag att öppna sin webbläsares utvecklarverktyg (*Developer tools*) och titta i nätverksfliken (*Network*) för att se vilka anrop skickas när man använder gränssnittets olika funktioner och filter. Om du tycker att vissa detaljer borde tilläggas i denna dokumentation får du gärna skicka synpunkter eller ännu bättre förbättra sidan själv.
