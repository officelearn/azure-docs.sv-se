---
title: 'Självstudie: Migrera webb tjänster från Google Maps | Microsoft Azure Maps'
description: Så här migrerar du webb tjänster från Google Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 50bdc0722328f857279b2cbd9a6e4cee740b9df8
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048937"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrera webb tjänsten från Google Maps

Azure Maps och Google Maps ger till gång till spatiala API: er via REST-webbtjänster. API-gränssnitten för dessa två plattformar utför liknande funktioner, men de använder olika namngivnings konventioner och svars objekt.

I följande tabell visas Azure Maps tjänst-API: et, som innehåller liknande funktioner som ett API för Google Maps-tjänsten.

| API för Google Maps-tjänsten | API för Azure Maps tjänsten                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Direction              | [Styra](https://docs.microsoft.com/rest/api/maps/route)                               |
| Avstånds mat ris         | [Väg mat ris](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geokodning               | [Sök](https://docs.microsoft.com/rest/api/maps/search)                             |
| Sök efter platser           | [Sök](https://docs.microsoft.com/rest/api/maps/search)                             |
| Placera Autoavsluta      | [Sök](https://docs.microsoft.com/rest/api/maps/search)                             |
| Statisk karta              | [Återge](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Tidszon               | [Tidszon](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Följande tjänst-API: er är för närvarande inte tillgängliga i Azure Maps:

- Höjning
- Geoplats
- Placera information och placera foton. Telefonnummer och webbplats-URL finns i Azure Maps Search API.
- Kart-URL: er
- Korsa. Hastighets begränsnings data är tillgängliga via väg-och omvända API: er för omdirigering i Azure Maps.
- Statisk gata vy

Azure Maps har flera andra REST-webbtjänster som kan vara av intresse:

- [Spatialdata](https://docs.microsoft.com/rest/api/maps/spatial): avlastning av komplexa spatiala beräkningar och åtgärder, till exempel polystaket, till en tjänst.
- [Trafik](https://docs.microsoft.com/rest/api/maps/traffic): åtkomst till trafik flöde och incident data i real tid.

## <a name="geocoding-addresses"></a>Adresser för att koda

Kodning är en process för att konvertera en adress till en koordinat. Till exempel "1 Microsoft Way, Redmond, WA" omvandlar till "longitud:-122,1298, latitud: 47,64005". Koordinater är nödvändiga för att placera en markör på en karta eller centrera en karta.

Azure Maps tillhandahåller flera metoder för att koda adresser:

- [**Kodning av fritt format**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Ange en enskild adress sträng och bearbeta begäran omedelbart. En enda sträng adress är "1 Microsoft Way, Redmond, WA". Den här metoden rekommenderas när du snabbt behöver koda enskilda adresser.
- [**Kodning av strukturerad adress**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Ange delar av en enskild adress och bearbeta begäran nära real tid. I delar av en adress ingår gatu namn, ort, land och post nummer. Den här metoden rekommenderas för två huvud scenarier. Data har redan parsats som enskilda adress delar. Eller så måste du snabbt och enkelt koda enskilda adresser.
- [**Kodning av batch-adresser**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): skapa en begäran som innehåller upp till 10 000 adresser och bearbeta begäran under en viss tids period. Alla adresser kommer att kodas parallellt på servern. När en omslutning slutförs, blir hela uppsättningen resultat nedladdnings bar. Den här metoden rekommenderas för att kunna koda stora data mängder.
- [**Fuzzy-sökning**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): detta API kombinerar adressens kod för att söka efter orienterings punkter. Detta API tar i en sträng med fri form och bearbetar begäran nära real tid. En fri Forms sträng kan vara en adress, plats, landmärke, orienterings punkt eller kategori av intressant kategori. Detta API rekommenderas när samma text ruta används för att fråga adresser och intressanta punkter.
- [**Fuzzy batch-sökning**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): skapa en begäran som innehåller upp till 10 000 adresser och bearbeta begäran under en viss tids period. Du kan begära platser, landmärken eller anslags punkter. Alla data kommer att bearbetas på servern och parallellt. När du är klar hämtas hela uppsättningen resultat.

Följande tabell innehåller en kors referens till Google Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` och `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` stad/stad<br/>`municipalitySubdivision` – till, sub/Super City<br/>`countrySubdivision`-delstat eller provins<br/>`countrySecondarySubdivision`-län<br/>`countryTertiarySubdivision`-distrikt<br/>`countryCode`-två bokstäver, landskod |
| `key`                       | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation. |
| `language`                  | `language` – se dokumentation om [språk som stöds](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Ett exempel på hur du använder Sök tjänsten finns dokumenterat [här](how-to-search-for-address.md). Se till att gå igenom [metod tipsen för sökning](how-to-use-best-practices-for-search.md).

> [!TIP]
> De kostnads fria adresserna för kodning och fuzzy Search-API: er kan användas i läget komplettera automatiskt genom att lägga till `&amp;typeahead=true` till URL: en för begäran. Detta talar om för servern att indatamängden är troligt vis delvis och att sökningen försätts i förutsägande läge.

## <a name="reverse-geocode-a-coordinate"></a>Omvändly-kod a-koordinat

Omvänd polykodning är processen att konvertera geografiska koordinater till en ungefärlig adress. Koordinater med "longitud:-122,1298, latitud: 47,64005" konvertera till "1 Microsoft Way, Redmond, WA".

Azure Maps innehåller flera omvända Omkodnings metoder:

- [**Omvänd landskod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Ange en enda geografisk koordinat för att få den ungefärliga adressen som motsvarar den här koordinaten. Bearbeta begäran nära real tid.
- [**Kors gatan omvänd landskod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Ange en enda geografisk koordinat och hämta information om närliggande kors gatan. Bearbeta begäran nära real tid.
- [**Omvänd landskod för batch-adress**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): skapa en begäran som innehåller upp till 10 000 koordinater och bearbetade begäran under en viss tids period. Alla data kommer att bearbetas parallellt på servern. När du är klar hämtas hela uppsättningen resultat.

Följande tabell innehåller en kors referens till Google Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps.

| Google Maps API-parameter   | Jämförbar Azure Maps API-parameter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation. |
| `language`                  | `language` – se dokumentation om [språk som stöds](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *Saknas*     |
| `result_type`               | `entityType`    |

Granska [metod tips för sökning](how-to-use-best-practices-for-search.md).

Azure Maps omvänte-API för omväntitet har vissa ytterligare funktioner som inte är tillgängliga i Google Maps. Dessa funktioner kan vara användbara för att integrera med ditt program när du migrerar din app:

- Hämta hastighets gräns data
- Hämta information om användning av vägar: lokal väg, arterial, begränsad åtkomst, ramp och så vidare
- Hämta sidan av gatan där en koordinat finns

## <a name="search-for-points-of-interest"></a>Söka efter platser av intresse

Du kan söka efter intressanta data i Google Maps med platser Sök-API. Detta API tillhandahåller tre olika sätt att söka efter intressanta punkter:

- **Hitta plats från text:** Söker efter en orienterings punkt baserat på dess namn, adress eller telefonnummer.
- **Närliggande sökning**: söker efter anslags punkter som ligger inom ett visst avstånd från en plats.
- **Texts ökning:** Söker efter platser med hjälp av en fri Forms text som innehåller orienterings punkt och plats information. Till exempel "pizza i New York" eller "restauranger nära Main St".

Azure Maps tillhandahåller flera Sök-API: er för intressanta punkter:

- [**POI sökning**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Sök efter intressanta platser efter namn. Till exempel "Starbucks".
- [**POI kategoris ökning**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Sök efter intressanta platser efter kategori. Till exempel "restaurang".
- [**Närliggande sökning**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): söker efter anslags punkter som ligger inom ett visst avstånd från en plats.
- [**Fuzzy-sökning**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): det här API: et kombinerar adressens kod för att söka efter orienterings punkter. Detta API tar i en sträng med fritt format. En adress, en plats, ett landmärke, en orienterings punkt, en punkt i en intresse kategori och så vidare. Detta API kan bearbeta begäran nära real tid. Detta API rekommenderas när användare söker efter adresser eller intressanta punkter med samma text ruta.
- [**Sök i geometrin**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Sök efter anslags punkter som är inom en angiven geometri (Polygon).
- [**Sök längs väg**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Sök efter anslags punkter som är utmed en angiven väg Sök väg.
- [**Fuzzy batch-sökning**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): skapa en begäran som innehåller upp till 10 000 adresser, platser, landmärken eller anslags punkter och låta dem bearbetas under en viss tids period. Alla data kommer att bearbetas parallellt på servern. När begäran har slutfört bearbetningen hämtar du hela uppsättningen med resultat.

För närvarande har Azure Maps inte något jämförbart API till API: et för texts ökning i Google Maps.

> [!TIP]
> Sök-API: t för POI, POI och fuzzy Search-API: er kan användas i Autoavsluta-läge. Lägg till `&amp;typeahead=true` i fråge-URL: en. Detta talar om för servern att indatamängden är troligt vis delvis och att sökningen slutförs i förutsägande läge.

Granska [metod tips för sökning](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Hitta plats från text

Använd Azure Maps Sök-API: et för [POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) eller API: et för [fuzzy search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) för att söka efter antecknings punkter efter namn eller adress.

I följande tabell visas API-parametrarna för Google Maps med respektive Azure Maps API-parametrar.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter |
|---------------------------|-------------------------------------|
| `fields`                  | *Saknas*                               |
| `input`                   | `query`                             |
| `inputtype`               | *Saknas*                               |
| `key`                     | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation. |
| `language`                | `language` – se dokumentation om [språk som stöds](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` och `radius`<br/>`topLeft` och `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Närliggande sökning

I Azure Maps kan du hämta närliggande punkter av intressen med hjälp av [söknings](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) -API: t i närheten.

Följande tabell innehåller en kors referens till Google Maps API-parametrar med Azure Maps API-parametrar.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation. |
| `keyword`                   | `categorySet` och `brandSet`        |
| `language`                  | `language` – se dokumentation om [språk som stöds](supported-languages.md) .  |
| `location`                  | `lat` och `lon`                     |
| `maxprice`                  | *Saknas*                               |
| `minprice`                  | *Saknas*                               |
| `name`                      | `categorySet` och `brandSet`        |
| `opennow`                   | *Saknas*                               |
| `pagetoken`                 | `ofs` och `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *Saknas*                               |
| `type`                      | `categorySet –` finns i dokumentationen för [Sök kategorier som stöds](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Beräkna vägar och vägbeskrivningar

Beräkna vägar och vägbeskrivning med Azure Maps. Azure Maps har många av samma funktioner som Google Maps-routningstjänsten, till exempel:

- ankomst-och avgångs tider.
- i real tids-och förutsägande baserade trafik vägar.
- Olika transport sätt. Till exempel, framförande, framförande och bekallning.

> [!NOTE]
> Azure Maps kräver att alla waypoints är koordinater. Adresser måste vara kodade först.

Tjänsten Azure Maps routning innehåller följande API: er för att beräkna vägar:

- [**Beräkna väg**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): beräkna en väg och bearbeta begäran nära real tid. Detta API stöder både GET-och POST-förfrågningar. POST-förfrågningar rekommenderas för att ange ett stort antal waypoints eller använda flera väg alternativ. Genom att använda POST ser du till att URL-begäran inte blir för lång och orsakar problem.
- [**Batch-väg**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): skapa en begäran som innehåller upp till 1 000 Route-begäran. Begär Anden som bearbetas under en viss tids period. Alla data processer parallellt på servern. Ladda ned uppsättningen så snart begäran slutförs och resultaten är klara.
- [**Mobilitets tjänster**](https://docs.microsoft.com/rest/api/maps/mobility): beräkna vägar och vägbeskrivningar med hjälp av offentlig överföring.

Följande tabell innehåller en kors referens till Google Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps.

| Google Maps API-parameter    | Jämförbar Azure Maps API-parameter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – koordinater i formatet `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation. |
| `language`                     | `language` – se dokumentation om [språk som stöds](supported-languages.md) .   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *Ej tillämpligt* – den här funktionen är en kod relaterad. Använd parametern *countrySet* när du använder API: et för Azure Maps-kodning.  |
| `traffic_model`               | *Ej tillämpligt* – kan bara ange om trafik data ska användas med *Traffic* -parametern. |
| `transit_mode`                | Se [dokumentationen om mobilitets tjänster](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Se [dokumentationen om mobilitets tjänster](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *Ej tillämpligt* – Azure Maps endast använder Metric-systemet.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Som standard returnerar API för Azure Maps väg bara en sammanfattning (avstånd och tider) och koordinaterna för vägens sökväg. Använd parametern `instructionsType` för att hämta inaktiverade instruktioner. Använd parametern `routeRepresentation` för att filtrera ut sammanfattningen och vägens sökväg.

API för Azure Maps routning har många ytterligare funktioner som inte är tillgängliga i Google Maps. Det kan vara användbart att integrera dessa funktioner när du migrerar appen:

- Stöd för väg typ: kortaste, snabbast, trilling och mest bränsle effektiv.
- Stöd för ytterligare rese lägen: buss, motorcykel, taxi, Truck och van.
- Stöd för 150 waypoints.
- Beräkna flera rese tider i en enskild begäran. historisk trafik, direkt trafik, ingen trafik.
- Undvik ytterligare väg typer: Carpool-vägar, unpaved-vägar, redan använda vägar.
- Ange anpassade områden för att undvika.
- Begränsa höjningen som vägen kan Ascend.
- Dirigera baserat på specifikationer för motor. Beräkna vägar för förbrännings-eller elmotor fordon baserade på produktspecifikationer och återstående bränsle eller laddning.
- Stöd för parametrar för kommersiell väg i fordonet. Såsom fordons dimensioner, vikt, antal Axels och Last typ.
- Ange högsta fordons hastighet.

Förutom dessa funktioner stöder Route service i Azure Maps [beräkning av flyttbara intervall](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Beräkning av dirigerbart intervall kallas även ISO Kron. Den genererar ett utrymme som omfattas av en polygon. Sedan beräknar du resan i valfri riktning från en ursprungs punkt. Allt när du överväger en viss tids period och mängden bränsle eller avgift.

## <a name="retrieve-a-map-image"></a>Hämta en kart bild

Azure Maps tillhandahåller ett API för att återge statiska kart bilder med data som har överanvänds. [Kart bildens rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) -api i Azure Maps är jämförbart med API: t för statisk mappning i Google Maps.

> [!NOTE]
> Azure Maps kräver att Center, alla markörer och Sök vägs platserna är koordinater i formatet "longitud, latitud". Google Maps använder å andra sidan "latitud, longitud"-formatet. Adresser måste vara kodade först.

I följande tabell visas API-parametrarna för Google Maps med de jämförbara Azure Maps API-parametrarna.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – anges som en del av URL-sökvägen. För närvarande stöds endast PNG. |
| `key`                       | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation. |
| `language`                  | `language` – se dokumentation om [språk som stöds](supported-languages.md) .  |
| `maptype`                   | `layer` och `style` – se dokumentation om [mappnings format som stöds](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *Ej tillämpligt* – det här är en funktion för att koda en funktion. Använd parametern `countrySet` när du använder API: et för Azure Maps-kodning.  |
| `scale`                     | *Saknas*                              |
| `size`                      | `width` och `height` – kan vara upp till 8192x8192 i storlek. |
| `style`                     | *Saknas*                              |
| `visible`                   | *Saknas*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps använder ett panel system med paneler som är två gånger så stora som de kart paneler som används i Google Maps. Som ett sådant värde för zoomnings nivån visas en zoomnings nivå närmare Azure Maps jämfört med Google Maps. Minska zoomnings nivån med en, i de begär Anden som du migrerar. Decrementing värde för zoomnings nivå kompenserar för variationen i panel systemen.

Mer information finns i [instruktionen How-to på Map image rendering API](how-to-render-custom-data.md).

Förutom att skapa en statisk kart bild ger tjänsten Azure Maps Render möjlighet att direkt komma åt kart paneler i raster (PNG) och vektor format:

- [**Kart panel**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Hämta raster (png) och vektor paneler för bas Maps (vägar, gränser, bakgrund).
- [**Panel för kart bilder**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): Hämta paneler för flyg-och satellit bilder.

> [!TIP]
> För några år sedan har många Google Maps-program växlats från interaktiva kart upplevelser till statiska kart bilder som en metod för att spara pengar. I Azure Maps är det ofta mycket mer kostnads effektivt att använda den interaktiva kart kontrollen i Web SDK. Tjänst avgiften baseras på antalet kart paneler som programmet läser in. Kart paneler i Azure Maps är stora. Det tar ofta bara några paneler att återskapa samma Map-vy som en statisk karta. Kart paneler cachelagras automatiskt av webbläsaren. Det innebär att den interaktiva kart kontrollen ofta genererar en bråkdel av en transaktion när du skapar en statisk kart visning. Panorering och zoomning kommer att läsa in fler paneler, men det finns alternativ i kart kontrollen för att inaktivera det här beteendet. Den interaktiva kart kontrollen tillhandahåller också många fler visualiserings alternativ än statiska kart tjänster.

### <a name="marker-url-parameter-format-comparison"></a>Jämförelse av parameter format för markör-URL

**Före: Google Maps**

I Google Maps kan du lägga till markörer till en statisk kart bild med hjälp av `markers`-parametern i URL: en. Parametern `markers` tar i ett format och en lista över platser som ska återges på kartan med det formatet enligt nedan:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Ytterligare format kan användas genom att lägga till ytterligare `markers` parametrar till URL: en med ett annat format och en uppsättning med platser.

Markör platser anges med formatet "latitud, longitud".

Markör format i Google Maps läggs till med formatet `optionName:value`, med flera format avgränsade med lod tecken (\|). Så här: "optionName1: värde1\|optionName2: värde2". Observera att alternativ namn och värden åtskiljs med kolon (:). Följande format alternativ namn kan användas för att formatera markörer i Google Maps:

- `color` – färgen på standard markör ikonen. Kan vara en 24-bitars hexadecimal färg (`0xrrggbb`) eller något av följande värden: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – ett enkelt alfanumeriskt tecken som ska visas ovanpå ikonen.
- `size` – markörens storlek. Kan vara `tiny`, `mid`eller `small`.

Du kan lägga till anpassade ikoner i Google Maps med följande format alternativ namn:

- `anchor` – anger hur ikon bilden ska justeras mot koordinaten. Kan vara ett pixel värde (x, y) eller något av följande värden: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`eller `bottomright`.
- `icon` – en URL som pekar mot ikon bilden.

I Google Maps kan till exempel en röd, medels Tor markör läggas till i kartan vid koordinater (longitud:-110, latitud: 45) med följande URL-parameter:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps markör](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Efter: Azure Maps**

I Azure Maps lägger du till markörer till en statisk kart bild genom att ange parametern `pins` i URL: en. Som Google Maps anger du ett format och en lista över platser i den här parametern. Ange `pins` parameter flera gånger för att stödja markörer med olika format.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Om du vill använda ytterligare format lägger du till ytterligare `pins` parametrar till URL: en med ett annat format och en uppsättning med platser.

För PIN-platsen kräver Azure Maps att koordinaterna ska vara i formatet "longitud Latitude". Google Maps använder formatet "latitud, longitud". Ett blank steg, inte ett kommatecken, avgränsar longitud och latitud i Azure Maps format.

I `iconType` anges vilken typ av PIN-kod som ska skapas. Det kan ha följande värden:

- `default` – standard ikonen för PIN-kod.
- `none` – ingen ikon visas, endast etiketter kommer att återges.
- `custom` – anger att en anpassad ikon ska användas. En URL som pekar på ikon bilden kan läggas till i slutet av `pins`-parametern efter PIN-kodens plats information.
- `{udid}` – ett unikt data-ID (UDID) för en ikon som lagras i Azure Maps data lagrings plattform.

Lägg till PIN-format i Azure Maps med `optionNameValue`-formatet. Separera flera format med vertikalstreck (\|). Till exempel: `iconType|optionName1Value1|optionName2Value2`. Alternativ namn och värden är inte separerade. Använd följande format alternativ namn till format markörer i Azure Maps:

- `al` – anger markörens opacitet (alfa). Välj ett tal mellan 0 och 1.
- `an` – anger PIN-ankaret. X-och y-pixelvärdena anges i formatet "x y".
- `co` – PIN-kodens färg. Måste vara en 24-bitars hexadecimal färg: `000000` för att `FFFFFF`.
- `la` – anger etikettens ankare. X-och y-pixelvärdena anges i formatet "x y".
- `lc` – etikettens färg. Måste vara en 24-bitars hexadecimal färg: `000000` för att `FFFFFF`.
- `ls` – etikettens storlek i bild punkter. Välj ett tal som är större än 0.
- `ro` – ett värde i grader för att rotera ikonen. Välj ett tal mellan-360 och 360.
- `sc` – ett skalnings värde för PIN-ikonen. Välj ett tal som är större än 0.

Etikett värden anges för varje PIN-plats. Den här metoden är mer effektiv än att använda ett enda etikett värde för alla markörer i listan över platser. Etikett svärdet kan vara en sträng med flera tecken. Omsluta strängen med enkla citat tecken för att säkerställa att den inte är förväxlad som ett format eller ett plats värde.

I Azure Maps lägger du till exempel till en röd (`FF0000`) standard ikon med etiketten "utrymmes nål", placerad under (15 50), med ikon i koordinater (longitud:-122,349300, latitud: 47,620180) med följande URL-parameter:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps markör](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

I följande exempel läggs tre PIN-koder till med etikett värden "1", "2" och "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps flera markörer](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Sök vägs-URL parameter format jämförelse

**Före: Google Maps**

I Google Maps kan linjer och polygoner läggas till i en statisk kart bild med hjälp av parametern `path` i URL: en. Parametern `path` tar i ett format och en lista över platser som ska återges på kartan, enligt nedan:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Ytterligare format kan användas genom att lägga till ytterligare `path` parametrar till URL: en med ett annat format och en uppsättning med platser.

Sök vägs platser i Google Maps anges med formatet `latitude1,longitude1|latitude2,longitude2|…`. Sökvägar kan vara kodade eller innehålla adresser för punkter.

Sök vägs format i Google Maps läggs till med formatet `optionName:value`, med flera format separerade med vertikalstreck (\|). Så här: `optionName1:value1|optionName2:value2`. Observera att alternativ namn och värden åtskiljs med kolon (:). Följande format alternativ namn kan användas för att formatera sökvägar i Google Maps:

- `color` – färgen på banan eller polygonens kontur. Kan vara en 24-bitars hexadecimal färg (`0xrrggbb`), en 32-bitars hexadecimal färg (`0xrrggbbbaa`) eller något av följande värden: svart, brun, grön, lila, gul, blå, grå, orange, röd, vit.
- `fillColor` – färgen som fyller Sök vägs ytan med (Polygon). Kan vara en 24-bitars hexadecimal färg (`0xrrggbb`), en 32-bitars hexadecimal färg (`0xrrggbbbaa`) eller något av följande värden: svart, brun, grön, lila, gul, blå, grå, orange, röd, vit.
- `geodesic` – anger om sökvägen ska vara en linje som följer jordnings kurvan.
- `weight` – bredden på Sök vägs linjen i bild punkter.

I Google Maps kan du lägga till en röd linje opacitet och pixel tjock lek i kartan mellan koordinaterna i URL-parametern. I exemplet nedan har linjen 50% opacitet och en tjocklek på fyra bild punkter. Koordinaterna är longitud:-110, latitud: 45 och longitud:-100, latitud: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps polyline](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Efter: Azure Maps**

I Azure Maps lägger du till linjer och polygoner till en statisk kart bild genom att ange parametern `path` i URL: en. Som Google Maps anger du ett format och en lista över platser i den här parametern. Ange `path` parameter flera gånger för att återge flera cirklar, linjer och polygoner med olika format.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

När den kommer till Sök vägs platser kräver Azure Maps att koordinaterna ska vara i formatet "longitud Latitude". Google Maps använder formatet "latitud, longitud". Ett blank steg, inte ett kommatecken, avgränsar longitud och latitud i Azure Maps format. Azure Maps stöder inte kodade sökvägar eller adresser för punkter. Överför större data uppsättningar när en polyjson fyller i Azure Maps data lagrings-API: et som dokumenteras [här](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

I Azure Maps lägger du till Sök vägs format med `optionNameValue`-formatet. Separera flera format efter pipes (\|) tecken som den här `optionName1Value1|optionName2Value2`. Alternativ namn och värden är inte separerade. Använd följande format alternativ namn för att formatera sökvägar i Azure Maps:

- `fa` – fyllnings färg opaciteten (alpha) som används vid åter givning av polygoner. Välj ett tal mellan 0 och 1.
- `fc` – fyllnings färgen som används för att återge ytan i en polygon.
- `la` – linje färg opaciteten (alpha) som används när du återger linjer och konturen för polygoner. Välj ett tal mellan 0 och 1.
- `lc` – linje färgen som används för att återge linjer och konturen för polygoner.
- `lw` – bredden på linjen i bild punkter.
- `ra` – anger en cirkel-radie i meter.

I Azure Maps lägger du till en röd linje opacitet och pixel tjock lek mellan koordinaterna i URL-parametern. I exemplet nedan har linjen 50% opacitet och en tjocklek på fyra bild punkter. Koordinaterna har följande värden: longitud:-110, latitud: 45 och longitud:-100, latitud: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps polylinje](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Beräkna en avstånds mat ris

Azure Maps tillhandahåller API för avstånds mat ris. Använd detta API för att beräkna res tiderna och avståndet mellan en uppsättning platser, med en avstånds mat ris. Det är jämförbart med API: et för avstånds mat ris i Google Maps.

- [**Väg mat ris**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): beräknar asynkrona res tider och avstånd för en uppsättning ursprung och mål. Har stöd för upp till 700 celler per begäran (antalet ursprung multiplicerat med antalet destinationer. Med den begränsningen i åtanke är exempel på möjliga mat ris dimensioner: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> En begäran till distans mat ris API: et kan bara göras med en POST-begäran med information om ursprung och mål i bröd texten i begäran. Dessutom kräver Azure Maps att alla ursprung och mål måste vara koordinater. Adresser måste vara kodade först.

Följande tabell innehåller en kors referens till Google Maps API-parametrar med de jämförbara Azure Maps API-parametrarna.

| Google Maps API-parameter      | Jämförbar Azure Maps API-parameter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – ange som en del JSON i POSTens brödtext. |
| `key`                          | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation. |
| `language`                     | `language` – se dokumentation om [språk som stöds](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – ange som en del JSON i POSTens brödtext.  |
| `region`                       | *Ej tillämpligt* – den här funktionen är en kod relaterad. Använd parametern `countrySet` när du använder API: et för Azure Maps-kodning. |
| `traffic_model`                | *Ej tillämpligt* – kan bara ange om trafik data ska användas med parametern `traffic`. |
| `transit_mode`                 | Det finns för närvarande inte stöd för *N/A* -transit-baserade avstånds-matriser.  |
| `transit_routing_preference`   | Det finns för närvarande inte stöd för *N/A* -transit-baserade avstånds-matriser.  |
| `units`                        | *Ej tillämpligt* – Azure Maps endast använder Metric-systemet. |

> [!TIP]
> Alla avancerade cirkulations alternativ som är tillgängliga i Azure Maps routing API stöds i Azure Maps avstånds mat ris API. Alternativen för avancerad routning är: Ruck routning, motor specifikationer och så vidare.

## <a name="get-a-time-zone"></a>Hämta en tidszon

Azure Maps tillhandahåller ett API för att hämta tids zonen för en koordinat. Azure Maps tidszons-API: et är jämförbar med tids zons-API: et i Google Maps:

- [**Tidszon per koordinat**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Ange en koordinat och ta emot tids zons informationen för koordinaten.

Följande tabell innehåller en kors referens till Google Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Se även [autentisering med Azure Maps](azure-maps-authentication.md) dokumentation.       |
| `language`                  | `language` – se dokumentation om [språk som stöds](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Förutom det här API: et tillhandahåller Azure Maps-plattformen ett antal API: er för tids zoner. Dessa API: er konverterar tiden baserat på namn eller ID för tids zonen:

- [**Tidszon med ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): returnerar aktuell, historisk och framtida tids zons information för angivet ID för IANA-tidszon.
- [**Tids zons uppräknings-IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): returnerar en fullständig lista över IANA-tidszonens ID. Uppdateringar av tjänsten IANA visas i systemet inom en dag.
- [**Enum för tidszon Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): returnerar en fullständig lista över Windows tids zons-ID: n.
- [**Version för tids zonens IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): returnerar det aktuella IANA-versions numret som används av Azure Maps.
- [**Time Zone Windows till IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): returnerar ett motsvarande IANA-ID, med ett giltigt Windows-tidszons-ID. Flera IANA-ID: n kan returneras för ett enda Windows-ID.

## <a name="client-libraries"></a>Klientbibliotek

Azure Maps tillhandahåller klient bibliotek för följande programmeringsspråk:

- Java Script, TypeScript, Node. js – [dokumentation](how-to-use-services-module.md) \| [NPM-paket](https://www.npmjs.com/package/azure-maps-rest)

Klient bibliotek med öppen källkod för andra programmeringsspråk:

- .NET standard 2,0 – [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-paket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Ytterligare resurser

Här följer några ytterligare dokumentation och resurser för Azure Maps REST-tjänsterna.

- [Metod tips för sökning](how-to-use-best-practices-for-search.md)
- [Sök efter en adress](how-to-search-for-address.md)
- [Dokumentation om Azure Maps REST service API-referens](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps REST-tjänsterna.

> [!div class="nextstepaction"]
> [Metod tips för att använda Sök tjänsten](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Så här använder du modulen tjänster (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)