# Regleringsbrev

## Officiel källa

Regeringskansliet, via Statskontoret ([statskontoret.se/statsliggaren](https://www.statskontoret.se/statsliggaren/))

### Erbjuder de ett API?

❌ Nej

## Alternativa källor

### Privat API

Statsliggaren har inget riktigt API men det finns ändå en underliggande logik och det går att ladda ner dokumenten maskinellt om man förstår den.

Varje regleringsbrev och varje bilaga får ett ID-nummer när det läggs till i systemet. Och man kan ladda ner ett brev:

```bash
# HTML-sidan
https://www.statskontoret.se/statsliggaren/regleringsbrev/index?RbId={ID}

# HTML-sidan med enbart ändringar (för ett ändringsbeslut)
https://www.statskontoret.se/statsliggaren/regleringsbrev/index?RbId={ID}&version=EndastAndringar

# PDF-filen
https://www.statskontoret.se/regleringsbrev/{ID}/pdf

# PDF-filen med enbart ändringar (för ett ändringsbeslut)
https://www.statskontoret.se/regleringsbrev/{ID}/pdf?Version=EndastAndringar

# Bilaga
https://www.statskontoret.se/regleringsbrev/bilaga/{ID}
```

### Projektet Öppna Statsliggaren

!!! info "Obs: Samma person ligger bakom detta projekt och Lagdata"

Projektet består av ett Python-paket för att maskinellt ladda ner alla regleringsbrev från Statsliggaren. Källkoden och dokumentationen finns på [Github](https://github.com/civictechsweden/oppna-statsliggaren).
