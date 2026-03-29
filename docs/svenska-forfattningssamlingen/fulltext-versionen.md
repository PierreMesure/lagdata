# Fulltext-versionen

## Officiell källa

Regeringskansliet ([rkrattsbaser.gov.se](https://rkrattsbaser.gov.se/sfsr) och även [beta.rkrattsbaser.gov.se](https://beta.rkrattsbaser.gov.se))

### Erbjuder de ett öppet API?

❌ Nej

## Alternativa källor

### Privat API

Den senaste versionen av Regeringskansliets rättsdatabas har ett grymt privat API. Viktigt ändå att veta att texten som går att hämta där måste formatteras om. Vissa radbrytningar måste tas bort, formattering för de olika rubriker och listor måste återskapas, och det finns inga länkar mellan texterna även när de refererar till varandra. Därfor kan det vara värt att hämta från en källa som har gjort det arbetet.

Se sidan om [Regeringskansliets rättsdatabaser](../vanliga-källor/rkrattsdb.md) för förklaringar och kodexempel.

### Riksdagens öppna data

Se [den relevanta sidan](../vanliga-källor/riksdagens-oppna-data).

I Riksdagens öppna data hittar man såklart svenska författningar. De går att ladda ner som text eller HTML. Observera att text-versionen är mycket närmare Regeringskansliets med radbrytningar och inga rubriker. I HTML-versionen har Riksdagsförvaltningen försökt att slå ihop linjer som tillhör samma paragraf och att återskapa rubriker.

### SE-Lex

Se [den relevanta sidan](../vanliga-källor/se-lex).

SE-Lex har [`sfs-processor`](https://github.com/se-lex/sfs-processor) som är ett CLI-verktyg som läser in SFS från [Regeringskansliets rättsdatabaser](../vanliga-källor/rkrattsdb.md) och omvandlar till olika format:

#### Git

SFS omvandlas kontinuerligt till Git commits och pushas till [Git-repo se-lex/sfs](https://github.com/se-lex/sfs). Man kan där följa ändringarna av lagar och författningar över tid, med Git som är världens mest använda versionshanteringsverktyg.

#### HTML (med ELI)

`sfs-processor` kan också exportera SFS till HTML-sidor, som är korrekt upptaggade för [ELI](https://eur-lex.europa.eu/eli-register/what_is_eli.html), den juridiska standard som EU tagit fram för publicerad lagstiftning.

#### Markdown med temporala taggar

Det finns också möjlighet att exportera till Markdown, med eller utan temporala taggar. Dessa XML-taggar innehåller information om status för varje del av dokumentet, om det är i kraft (giltigt vid given tidpunkt) eller har upphört.

```html
<section class="kapitel" selex:status="ikraft" selex:ikraft_datum="2025-01-01">
### 1 § En paragraf
...
</section>

<section class="paragraf" selex:status="upphavd" selex:upphor_datum="2023-12-31">
#### 2 § En paragraf
...
</section>

<section class="kapitel" selex:status="ikraft" selex:ikraft_villkor="den dag regeringen bestämmer">
### 3 § Rubrik för villkorat ikraftträdande
...
</section>
```

### open-sfs

[open-sfs](https://huggingface.co/datasets/PierreMesure/open-sfs) är ett litet projekt som försöker att samla alla författningar i både fulltext-version och tryckt version.

Projektet använder paketet [python-sfs](https://github.com/PierreMesure/python-sfs) men nya uppgifter uppdateras idag inte automatiskt.

Här är ett enkelt Python-skript som laddar ner och extraherar fulltext-versioner från kolumnen "text" i datamängden direkt från HuggingFace. Observera att filen ännu inte är optimerad för att laddas inkrementalt så du kommer behöva 4Gb minnesutrymme och 8Gb RAM.

```python
from datasets import load_dataset

dataset = load_dataset(
    "PierreMesure/open-sfs",
    columns=["beteckning", "text"],
)
```

### lagen.nu

Se [den relevanta sidan](../vanliga-källor/lagen-nu).

Lagen.nu erbjuder kanske den mest kompletta versionen av svenska författningssamlingen. Utöver en justering i textens format har Staffan även sett till att inkludera metadata för många delar av texten: referenser i samma eller andra författningar, författningskommentarer. Lagen.nu bevarar även tidigare versioner när en författning ändras, medan de andra aktörerna enbart tillgängliggör den senaste versionen som fulltext.

Problemet är dock att lagen.nu har väldigt lite dokumentation för sitt API och metadatan sitter i ett XML-format som kan vara svårt att ta sig an.
