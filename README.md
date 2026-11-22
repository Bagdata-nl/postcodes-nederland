# Postcodes Nederland (PC4)

Alle 4.074 Nederlandse postcodegebieden op vier cijfers, met woonplaats, gemeente, provincie en
coördinaten. Open data uit de Basisregistratie Adressen en Gebouwen (BAG), elke maand bijgewerkt.
Gratis, zonder account, ook voor commercieel gebruik.

**Stand:** BAG-peildatum 10 november 2026 · 4.074 postcodegebieden · 342 gemeenten

## Downloaden

| Bestand | Inhoud | Licentie |
| --- | --- | --- |
| [pc4.csv](data/pc4.csv) · [json](data/pc4.json) · [geojson](data/pc4.geojson) | woonplaats, gemeente, provincie, aantallen en coördinaten | CC0 |
| [pc4-cbs.csv](data/pc4-cbs.csv) · [json](data/pc4-cbs.json) | hetzelfde, plus inwoners, leeftijd, huishoudens, WOZ-waarde en koop/huur van het CBS (2024) | CC BY 4.0 |

Een vaste link naar de nieuwste stand, bijvoorbeeld voor een script:

```
https://raw.githubusercontent.com/Bagdata-nl/postcodes-nederland/main/data/pc4.csv
```

Een oudere maand nodig? Elke stand heeft een [tag](https://github.com/Bagdata-nl/postcodes-nederland/tags) met de peildatum.
Wat er per maand veranderde staat in [CHANGELOG.md](CHANGELOG.md).

## Voorbeeld

| postcode4 | woonplaats | gemeente | provincie | aantalAdressen | lat | lon |
| --- | --- | --- | --- | --- | --- | --- |
| 1011 | Amsterdam | Amsterdam | Noord-Holland | 7673 | 52.3709920 | 4.9041730 |
| 1012 | Amsterdam | Amsterdam | Noord-Holland | 9356 | 52.3735030 | 4.8950940 |
| 1013 | Amsterdam | Amsterdam | Noord-Holland | 15083 | 52.3878110 | 4.8837060 |

GitHub toont [pc4.geojson](data/pc4.geojson) als kaart, met één punt per postcodegebied.

## Kolommen

CSV: komma als scheidingsteken, UTF-8, kopregel, punt als decimaalteken. Codes zoals `gemeenteCode`
hebben voorloopnullen: lees ze in als tekst.

**pc4** (bron: BAG)

| Kolom | Betekenis |
| --- | --- |
| `postcode4` | Postcode op vier cijfers |
| `woonplaats` | Woonplaats met de meeste adressen in het gebied |
| `woonplaatsId` | BAG-code van die woonplaats |
| `gemeenteCode` | Gemeentecode van het CBS (vier cijfers) |
| `gemeente` | Gemeente met de meeste adressen in het gebied |
| `provincieCode` | Provinciecode (PV20 t/m PV31) |
| `provincie` | Provincie |
| `aantalWoonplaatsen` | Over hoeveel woonplaatsen het gebied valt |
| `aantalPostcodes` | Aantal postcodes op zes posities in het gebied |
| `aantalAdressen` | Aantal adressen (nummeraanduidingen) |
| `aantalWoningen` | Aantal verblijfsobjecten met een woonfunctie |
| `lat` | Breedtegraad van het midden van de adressen, WGS84 |
| `lon` | Lengtegraad van het midden van de adressen, WGS84 |
| `rdX` | X van hetzelfde punt in Rijksdriehoek (EPSG:28992), meters |
| `rdY` | Y van hetzelfde punt in Rijksdriehoek (EPSG:28992), meters |

**pc4-cbs** heeft dezelfde kolommen en daarnaast (bron: CBS):

| Kolom | Betekenis |
| --- | --- |
| `inwoners` | Aantal inwoners |
| `mannen` | Aantal mannen |
| `vrouwen` | Aantal vrouwen |
| `inwoners0tot15` | Inwoners van 0 t/m 14 jaar |
| `inwoners15tot25` | Inwoners van 15 t/m 24 jaar |
| `inwoners25tot45` | Inwoners van 25 t/m 44 jaar |
| `inwoners45tot65` | Inwoners van 45 t/m 64 jaar |
| `inwoners65plus` | Inwoners van 65 jaar en ouder |
| `huishoudens` | Aantal particuliere huishoudens |
| `eenpersoonshuishoudens` | Aantal eenpersoonshuishoudens |
| `huishoudensMetKinderen` | Eenouder- plus tweeouderhuishoudens |
| `gemHuishoudensgrootte` | Gemiddelde grootte van een huishouden |
| `gemWozWaarde` | Gemiddelde WOZ-waarde van een woning, in euro |
| `percKoopwoningen` | Aandeel koopwoningen, in procenten |
| `percHuurwoningen` | Aandeel huurwoningen, in procenten |
| `stedelijkheid` | 1 (zeer sterk stedelijk) t/m 5 (niet stedelijk) |
| `cbsJaar` | Het jaar van de CBS-cijfers |

Goed om te weten:

- Een postcodegebied kan over meer woonplaatsen of gemeenten vallen. `woonplaats` en `gemeente` zijn die
  met de meeste adressen; `aantalWoonplaatsen` zegt over hoeveel woonplaatsen het gebied valt.
- De coördinaat is het midden van alle adressen in het gebied, niet het midden van het vlak op de kaart.
- Een leeg CBS-veld betekent dat het CBS de waarde afschermt (te weinig inwoners of woningen) of niet kent.
  4.070 van de 4.074 gebieden hebben CBS-cijfers.
- Het CBS rondt aantallen af op vijftallen. De cijfers lopen achter op de BAG; `cbsJaar` zegt van welk jaar
  ze zijn.

## Gebruiken

Python:

```python
import pandas as pd

url = "https://raw.githubusercontent.com/Bagdata-nl/postcodes-nederland/main/data/pc4.csv"
pc4 = pd.read_csv(url, dtype={"postcode4": str, "gemeenteCode": str, "woonplaatsId": str})
print(pc4[pc4.gemeente == "Utrecht"][["postcode4", "woonplaats", "lat", "lon"]])
```

JavaScript:

```js
const url = "https://raw.githubusercontent.com/Bagdata-nl/postcodes-nederland/main/data/pc4.json";
const pc4 = await (await fetch(url)).json();
const gebied = pc4.find((p) => p.postcode4 === "3511");
console.log(gebied.woonplaats, gebied.lat, gebied.lon);
```

SQL (PostgreSQL):

```sql
CREATE TABLE pc4 (
  postcode4 char(4) PRIMARY KEY, woonplaats text, woonplaatsId text, gemeenteCode char(4), gemeente text,
  provincieCode text, provincie text, aantalWoonplaatsen int, aantalPostcodes int, aantalAdressen int,
  aantalWoningen int, lat numeric(9,6), lon numeric(9,6), rdX numeric(10,3), rdY numeric(10,3)
);
\copy pc4 FROM 'pc4.csv' WITH (FORMAT csv, HEADER true)
```

## Hoe vaak bijgewerkt

Het Kadaster levert elke maand een nieuwe stand van de BAG. Kort daarna staat hier de nieuwe versie.

## Meer detail nodig?

Dit bestand stopt bij vier cijfers. Postcodes op zes posities met straat en huisnummerreeksen, en alle
adressen van Nederland met BAG-id's, staan op [bagdata.nl](https://bagdata.nl/?utm_source=github&utm_medium=referral&utm_campaign=bagdata.nl_www-github). De
[documentatie van de PC4-tabel](https://bagdata.nl/gratis-pc4-cbs/?utm_source=github&utm_medium=referral&utm_campaign=bagdata.nl_www-github) staat daar ook.

## Bron en licentie

- `pc4.*`: BAG, Kadaster. Publiek domein ([CC0 1.0](LICENSE)); bronvermelding hoeft niet, een link wordt
  gewaardeerd.
- `pc4-cbs.*`: de CBS-kolommen komen uit *Kerncijfers per postcode* van het CBS.
  [CC BY 4.0](LICENSE-CBS.md); vermeld bij gebruik: **Bron: CBS (© CBS, © ESRI Nederland), BAGdata.nl**.
- Samengesteld door [BAGdata.nl](https://bagdata.nl/?utm_source=github&utm_medium=referral&utm_campaign=bagdata.nl_www-github). Zonder garantie. Een fout gevonden? Maak een
  [issue](https://github.com/Bagdata-nl/postcodes-nederland/issues) aan.

## English

All 4.074 four-digit postcode areas (PC4) of the Netherlands with place, municipality, province and
coordinates, from the Dutch national register of addresses and buildings (BAG). Updated every month; this
version reflects the register on 2026-11-10. `pc4.*` is public domain (CC0). `pc4-cbs.*` adds
population, household and housing figures from Statistics Netherlands (CBS, 2024) under CC BY 4.0.
Column names are Dutch: `woonplaats` is the place, `gemeente` the municipality, `aantalAdressen` the
number of addresses.
