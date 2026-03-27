# Den tryckta versionen

## Officiell källa

Regeringskansliet ([svenskforfattningssamling.se](https://svenskforfattningssamling.se) och tidigare [rkrattsdb.gov.se](https://rkrattsdb.gov.se/sfspdf/))

### Erbjuder de ett öppet API?

❌ Nej

## Alternativa källor

### Privat API

Varken *svenskforfattningssamling.se* eller *krattsdb.gov.se* erbjuder ett API men både webbplatserna följer en viss logik i hur de nämner filerna man kan ladda ner.

Se sidan om [Regeringskansliets rättsdatabaser](../vanliga-källor/rkrattsdb.md) för förklaringar och kodexempel.

### open-sfs

[open-sfs](https://huggingface.co/datasets/PierreMesure/open-sfs) är ett litet projekt som försöker att samla alla författningar i både fulltext-version och tryckt version.

När det gäller tryckta versionen finns allt sedan SFS 1993:1 tillgängligt. Dessa filer hämtas från Regeringskansliets rättsdatabaser (SFS 1998:306 och framåt) samt externa källor för SFS 1993:1 till SFS 1998:305.

Projektet använder paketet [python-sfs](https://github.com/PierreMesure/python-sfs) men nya uppgifter uppdateras idag inte automatiskt.

Här är ett enkelt Python-skript som laddar ner och extraherar PDF-filerna från kolumnen "pdf" i datamängden direkt från HuggingFace. Observera att filen ännu inte är optimerad för att laddas inkrementalt så du kommer behöva 4Gb minnesutrymme och 8Gb RAM.

```python
from datasets import load_dataset

dataset = load_dataset(
    "PierreMesure/open-sfs",
    columns=["beteckning", "pdf"],
)

for row in dataset:
    if row["pdf"] is None:
        continue

    beteckning = row["beteckning"].replace(":", "-")
    f"{beteckning}.pdf".write_bytes(row["pdf"])
```
