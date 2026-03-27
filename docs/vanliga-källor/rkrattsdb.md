# RKs rättsdatabaser

Anledningen till att jag skriver "rättsdatabaser" i pluralform är att Regeringskansliet underhåller många olika system som kallas för det. Och de tillgängliggör olika typer av dokument på olika sätt.

Den viktigaste dokumenttypen som tillhandahållas av en rättsdatabas brukar vara svenska författningssamlingen, med alla lagar, förordningar... Regeringskansliet erbjuder den i två former:

- tryckta versionen (egentligen PDF-filer med en elektronisk stämpel som säkerställer auktenticiteten) på två olika webbsidor
- en fulltext-version som extraheras från tryckta versionen på ett ganska rudimentärt sätt som inte behåller formatteringen

## rkrattsdb.gov.se: PDF-filer 1998 - 2018

[rkrattsdb.gov.se](https://rkrattsdb.gov.se) är Regeringskansliets äldsta rättsdatabas och det är svårt att veta hur gammal den faktiskt är.

Den tillgängliggör tre dokumenttyper:

- [SFS](https://rkrattsdb.gov.se/sfspdf/) som PDF-filer från SFS 1998:306 till SFS 2018:159
- [Kommittédirektiv](https://rkrattsdb.gov.se/kompdf/) som PDF-filer från Dir. 2006:1 till Dir. 2019:25
- [Förordningsmotiv](https://rkrattsdb.gov.se/forpdf/) som PDF-filer från Fm 2000:1 till Fm 2016:01

Det finns en rudimentär sökmotor för alla dokumenttyper men sökresultaten hämtas som HTML-lista som måste konverteras till ett bättre format.

Men länkarna till PDF-filerna följer ett tydligt mönster och slutar alltid med `{YY}/{YY}{NNNN}.PDF`:

```bash
# Exempel för SFS 2000:1:
https://rkrattsdb.gov.se/SFSdoc/00/000001.PDF

# Exempel för kommittédirektiv 2006:100
https://rkrattsdb.gov.se/KOMdoc/06/060100.PDF

# Exempel för kommittédirektiv 2013:2
https://rkrattsdb.gov.se/FORdoc/13/130002.PDF
```

Så man kan också nöja sig med att testa dem maskinellt och ladda ner de filerna som finns:

```python
OLD_DOMAIN = "https://rkrattsdb.gov.se"
OLD_URL = OLD_DOMAIN + "/SFSdoc/{}/{}{}.pdf"

for year in range(1998, 2019):
    FAILURES = 0
    last_digits = year % 100
    last_digits_str = f"{last_digits:02d}"
    year_folder = f"pdf/{last_digits_str}"
    os.makedirs(year_folder, exist_ok=True)

    for lopnummer in range(1, 10000):
        if year == 1998 and lopnummer < 306:
            continue

        lopnummer_str = f"{lopnummer:04d}"
        url = OLD_URL.format(last_digits_str, last_digits_str, lopnummer_str)
        response = requests.get(url)

        if response.status_code == 200:
            with open(
                f"{year_folder}/{last_digits_str}{lopnummer_str}.pdf",
                "wb",
            ) as file:
                file.write(response.content)
        else:
            FAILURES += 1

            if FAILURES == 10:
                break
```

Den logiken har jag använt för SFS i projektet [python-sfs](https://github.com/PierreMesure/python-sfs).

## svenskforfattningssamling.se: SFS i PDF-filer från 2018

[svenskforfattningssamling.se](https://svenskforfattningssamling.se) tar vidare där den gamla databasen slutade och tillgängliggör alla SFS i sin tryckta version (PDF-filer) från 2018:160 och framåt. Tjänsten slutar dock tillgängliggöra kommittédirektiv och förordningsmotiv, dessa dokument hittas nu bara på regeringen.se.

Webbsidan erbjuder ett ännu mer minimalistiskt sökgränssnitt (enbart sök efter SFS-nummer) och fortfarande inget API.

Däremot följer länkarna till PDF-filerna fortfarande en viss logik och slutar med: `{YYYY}-{MM}/SFS{YYYY}-{N}.pdf` så det går att hämta dem maskinellt, till exempel med det skriptet:

```python
NEW_DOMAIN = "https://svenskforfattningssamling.se"
NEW_URL = NEW_DOMAIN + "/sites/default/files/sfs/{}-{}/SFS{}-{}.pdf"

for year in range(2018, 2026):
    FAILURES = 0
    last_digits = year % 100
    last_digits_str = f"{last_digits:02d}"
    year_folder = f"pdf/{last_digits_str}"
    os.makedirs(year_folder, exist_ok=True)

    month = 1

    for lopnummer in range(1, 10000):
        if year == 2018 and lopnummer < 160:
            continue

        lopnummer_str = f"{lopnummer:04d}"

        for m in range(month, 13):
            month_str = f"{m:02d}"
            url = NEW_URL.format(year, month_str, year, lopnummer)
            response = requests.get(url)
            if response.status_code == 200:
                with open(
                    f"{year_folder}/{last_digits_str}{lopnummer_str}.pdf",
                    "wb",
                ) as file:
                    print(f"Saving SFS {year}:{lopnummer_str}...")
                    file.write(response.content)

                month = m
                break
            elif m == 12:
                FAILURES += 1

                if FAILURES == 10:
                    break
```

Den logiken går också att hitta i projektet [python-sfs](https://github.com/PierreMesure/python-sfs).

## rkrattsbaser.gov.se: fulltext-versioner

[rkrattsbaser.gov.se](https://rkrattsbaser.gov.se) ska inte blandas med *rkrattsdb.gov*.se, det är två olika webbsidor som Regeringskansliet verkar ha utvecklat sida vid sida tills man bestämde att flytta den tryckta versionen av dokumenten till *svenskforfattningssamling.se* och *regeringen.se*. *rkrattsbaser.gov.se* fortsatte dock sin långa karriär med ett oförändrat syfte: att erbjuda en fulltext-version av författningarna och vissa andra dokument som kommittédirektiv och kommittéregistret.

Tjänsten har inget API och att hämta information från den kräver processering av HTML-sidorna. Därför vill jag hänvisa den som letar efter fulltext-versioner av svenska författningssamlingen att titta på nästa sektion.

För den som letar efter information om kommittéerna kan *rkrattsbaser.gov.se* dock vara en vettig källa, kommittédirektiv anda sedan 1995 samlas där i fulltext-version. Och lika många kommittéberättelser. Men samma information och mer kan hittas i [Riksdagens öppna data](./riksdagens-oppna-data.md).

Listan av kommittéerna kan till exempel hämtas med skrapning genom att anropa:

```bash
# Observera att fritext ska lämnas tomt för att få alla kommittéer
# och att sidnummer börjar vid 1 och slutar vid (antalet kommittéer / 30) + 1
https://rkrattsbaser.gov.se/komm?fritext=&page={sidnummer}
```

## beta.rkrattsbaser.gov.se: den efterlängtade arvtagaren

Under våren 2023 publicerade Regeringskansliet en ny version av sin rättsdatabas som visar väldigt lovande potential. Den kallas för "betasidan", vilket visar en väldigt positiv signal att myndigheten försöker att utveckla sina tjänster på ett mer agilt och inkrementalt sätt med sina användare. Och även om tjänsten visar än så länge bara lagar och förordningar informerar man att "[y]tterligare databaser kommer att läggas till". Tyvärr har projektet helt stannat av efter den här första leveransen och det går inte att se några förändringar sedan 2023.

Den betasidan använder trots allt ett modernt API, inte ett öppet och dokumenterat sådant men ändå mycket mer användbart om man tar lite tid för att förstå dess logik.

Det ligger på:

```bash
https://beta.rkrattsbaser.gov.se/elasticsearch/SearchEsByRawJson
```

API-standarden som används följer i stort sett Elasticsearchs API-standard, vilket gör det lätt att bygga avancerade sökfrågor med sortering och filter. Det gör också att man kan hämta upp till 10 000 objekt i taget och suga hela rättsdatabasen med ett fåtals anrop.

Här är några exempel från projektet [python-sfs](https://github.com/PierreMesure/python-sfs):

```bash
BETA_URL = "https://beta.rkrattsbaser.gov.se/elasticsearch/SearchEsByRawJson"

def _post(payload):
    return requests.request("POST", BETA_URL, json=payload).json()["hits"]["hits"]

# Hämta en specifik författning med dess beteckningsnummer
def get_law(id):
    payload_dict = {
        "api": "search",
        "json": {"query": {"bool": {"must": [{"term": {"beteckning.keyword": id}}]}}},
    }

    return _post(payload_dict)

# Hämta alla författningar för ett visst år
def get_year(year):
    payload_dict = {
        "searchIndexes": ["Sfs"],
        "api": "search",
        "json": {
            "sort": [{"beteckningSortable.sort": {"order": "asc"}}],
            "query": {
                "bool": {
                    "must": [
                        {
                            "bool": {
                                "should": [
                                    {"terms": {"publiceringsar.keyword": [year]}}
                                ]
                            }
                        },
                        {"term": {"publicerad": True}},
                    ]
                }
            },
            "size": 10000,
            "from": 0,
        },
    }

    return _post(payload_dict)

# Hämta alla författningar som uppdaterades sedan ett visst datum
def get_newer_items(date):
    payload_dict = {
        "searchIndexes": ["Sfs"],
        "api": "search",
        "json": {
            "sort": [{"beteckningSortable.sort": {"order": "asc"}}],
            "query": {
                "bool": {
                    "must": [
                        {"range": {"uppdateradDateTime": {"gt": date}}},
                        {"term": {"publicerad": True}},
                    ]
                }
            },
            "size": 10000,
            "from": 0,
        },
    }

    return _post(payload_dict)
```
