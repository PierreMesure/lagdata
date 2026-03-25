# Statens offentliga utredningar

## Officiel källa

Regeringskansliet ([regeringen.se > Dokument & publikationer > Statliga offentliga utredningar](https://www.regeringen.se/rattsliga-dokument/statens-offentliga-utredningar/) och [sou.gov.se](https://www.sou.gov.se))

### Erbjuder de ett API?

❌ Nej

## Alternativa källor

### Kungl. biblioteket

KB tillgängliggör alla SOU från 1867-1999 på [sou.kb.se](https://sou.kb.se). De erbjuder tyvärr inget API men deras dokument har hämtats och tillgängliggjorts av flera andra organisationer som nämns nedan.

### Riksdagens öppna data

Se [den relevanta sidan](../vanliga-källor/riksdagens-oppna-data).

Riksdagsförvaltningen samlar alla statliga utredningar anda sedan 1867.
Observera att det kan dröja flera dagar eller veckor innan vissa dokument hittas i Riksdagens öppna data.

### g0v.se

Se [den relevanta sidan](../vanliga-källor/g0vse).

g0v.se erbjuder en strukturerad lista av alla statliga utredningar som går att ladda ner på regeringen.se med max 24 timmar fördröjning. De äldsta utredningarna som är tillgängliga där är från 1994.

### Lagen.nu

Se [den relevanta sidan](../vanliga-källor/g0vse).

Lagen.nu tillgängliggör statliga utredningar anda sedan 1921.

### Hitta remissvar

Se [den relevanta sidan](../vanliga-källor/hitta-remissvar).

Hitta remissvar erbjuder en lista av alla utredningar sedan 2002 med mycket metadata: förslag, konsekvenskapitel, rensade beteckningsnummer och även en lista av remissvar för varje utredning.

### In- och utredning

Se [den relevanta sidan](../vanliga-källor/in-och-utredning).

In- och utredningar erbjuder en lista av samma utredningar som finns på Riksdagens öppna data med en genererad referenslista (dvs. vilka utredningar citerar varandra).

### Linköpings universitet

Linköpings universitet erbjuder en [söktjänst](https://ep.liu.se/databases/sou/default.aspx) för att göra ordsök i utredningar från 1867 till idag. De har även ett API som är begränsat till 5 sökresultat utan API-nyckel.

```bash
https://www2.bibl.liu.se/api/sou_api/getdata.aspx?q=fritext:hvo&fl=namn,titel,url&wt=json&api_key=test

# Parametrar
# q: sökord, kan kombineras med "titel:" eller "fritext:" för att söka enbart i vissa fält
# fl: vilka kolumner inkluderas
# wt: xml eller json
# api_key: för att få fler än 5 sökresultat

# Kolumner:
# id - löpnummer
# namn - SOU-namnbeteckning, ex '1969:46'
# ar - År
# nummer - nummer
# titel - SOUns titel
# url - url till pdf
# fritext - hela textinnehållet i respektive SOU
```
