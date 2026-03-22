# g0v.se

!!! info "Samma person ligger bakom g0v.se och Lagdata"

[g0v.se](https://g0v.se) är en gratis tjänst som hämtar alla sidor från regeringen.se varje natt och tillgängliggör webbplatsens information som öppna strukturerade data.

Projektets syfte är att sänka tröskeln för att maskinellt hämta information från regeringens webbplats, att minska belastningen på Regeringskansliets servrar från alla aktörer som behöver idag skrapa den var för sig.

Källkoden är öppen och alla är välkomna att förbättra den.

## Begränsningar

Data från g0v.se uppdateras varje natt kl.03 för att minska belastningen på Regeringskansliets servrar. Enbart de sidorna som markeras som "uppdaterade" hämtas igen, vilket kan leda till att vissa uppdateringar saknas.

g0v.se försöker att återge webbsidans uppgifter utan att rensa dem så en del postprocessering krävs, inte minst för att hämta metadata från webbsidorna. g0v.se samlar inte dokumenten som finns på regeringen.se så dessa måste fortfarande hämtas av varje projekt, även om processen blir mycket enklare än att skrapa och processera regeringen.se:s HTML-sidor.
