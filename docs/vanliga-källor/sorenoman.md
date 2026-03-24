# Sören Öman

Sören Öman är bland annat ordförande i Arbetsdomstolen. [sorenoman.se](https://www.sorenoman.se) är hans personlig webbplats som han använder för att visa sin aktivitet som utredare och som författare av juridiska böcker.

Sören Öman har under de många åren som han har byggt sin webbsida lagt till mer och mer rättsinformation. Även om det kanske började som ett sätt att attrahera fler besökare var det också av rent intresse för rättsinformation och för att tillgängliggöra den bredare.

Eftersom Sören Öman har ibland rensat och strukturerat om uppgifterna manuellt är det en unik samling av rättsinformation som inte går att hitta någonstans, även om det inte går att garantera att allt är korrekt.

## Hämta strukturerad data

sorenoman.se har inget öppet API men webbsidan är byggd med modernare webbstandarder än Regeringskansliets tjänster så det går att komma åt informationen maskinellt nästan utan att skrapa HTML.

På sidan [sorenoman.se/teman/forarbeten](https://www.sorenoman.se/teman/forarbeten/) kan man till exempel ladda ner en lista av alla förarbeten genom att klistra det följande skriptet i konsolen:

```js
(function () {
    var needle = "google.visualization.arrayToDataTable(";

    function extractArrayLiteral(text, startAt) {
      var startCall = text.indexOf(needle, startAt);
      if (startCall === -1) return null;

      var i = startCall + needle.length;
      while (/\s/.test(text[i])) i++;
      if (text[i] !== "[") return null;

      var depth = 0;
      var quote = null;
      var escaped = false;
      var end = -1;

      for (var j = i; j < text.length; j++) {
        var ch = text[j];

        if (quote) {
          if (escaped) escaped = false;
          else if (ch === "\\") escaped = true;
          else if (ch === quote) quote = null;
          continue;
        }

        if (ch === "'" || ch === '"' || ch === "`") {
          quote = ch;
          continue;
        }

        if (ch === "[") depth++;
        else if (ch === "]") {
          depth--;
          if (depth === 0) {
            end = j;
            break;
          }
        }
      }

      if (end === -1) return null;

      return {
        nextIndex: end + 1,
        literal: text.slice(i, end + 1)
      };
    }

    function decodeHtml(text) {
      var t = document.createElement("textarea");
      t.innerHTML = text;
      return t.value.replace(/\u00a0/g, " ").trim();
    }

    function parseForarbeteCell(cell) {
      var result = {
        forarbete: null,
        forarbetsnamn: null
      };

      if (!cell || typeof cell !== "object") {
        result.forarbete = cell;
        return result;
      }

      result.forarbete = cell.v || null;

      var wrapper = document.createElement("div");
      wrapper.innerHTML = cell.f || "";

      var links = wrapper.querySelectorAll("a");
      var firstLink = links[0] || null;
      var secondLink = links[1] || null;

      if (firstLink) {
        var href1 = firstLink.getAttribute("href") || "";
        var m1 = href1.match(/[?&]forarbetsnamn=([^&]+)/);
        if (m1) result.forarbetsnamn = decodeURIComponent(m1[1]);
      }

      if (secondLink) {
        var href2 = secondLink.getAttribute("href") || "";
        var m2 = href2.match(/[?&]utredningsid=([^&]+)/);
        var utredningsnamn = decodeHtml(secondLink.textContent || "");

        if (m2) result.utredningsid = decodeURIComponent(m2[1]);
        if (utredningsnamn) result.utredningsnamn = utredningsnamn;
      }

      return result;
    }

    fetch(location.href)
      .then(function (r) { return r.text(); })
      .then(function (html) {
        var arrays = [];
        var pos = 0;

        while (true) {
          var found = extractArrayLiteral(html, pos);
          if (!found) break;
          pos = found.nextIndex;

          try {
            arrays.push(Function("return (" + found.literal + ");")());
          } catch (e) {}
        }

        var table = arrays.find(function (arr) {
          return Array.isArray(arr) &&
            Array.isArray(arr[0]) &&
            arr[0].join("|") === "Förarbete|Typ|År|Sök|Departement|Utskott";
        });

        if (!table) throw new Error("Main Förarbete table not found.");

        var headers = table[0];
        var rows = table.slice(1).map(function (row) {
          var obj = {};

          headers.forEach(function (header, i) {
            if (header === "Sök" || header === "Typ") return;

            if (header === "Förarbete") {
              Object.assign(obj, parseForarbeteCell(row[i]));
            } else {
              obj[header] = row[i];
            }
          });

          return obj;
        });

        var blob = new Blob([JSON.stringify(rows, null, 2)], {
          type: "application/json"
        });
        var url = URL.createObjectURL(blob);
        var a = document.createElement("a");
        a.href = url;
        a.download = "forarbeten.json";
        a.click();
        setTimeout(function () { URL.revokeObjectURL(url); }, 1000);

        console.log("Downloaded rows:", rows.length);
        console.log(rows[0]);
        return rows;
      })
      .catch(console.error);
  })();
```
