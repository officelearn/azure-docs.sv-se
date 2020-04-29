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
ms.openlocfilehash: d2f25f2b786686b8af9bad4ea8ce3c8aea9b589f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371461"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrera webb tjänsten från Google Maps

Både Azure och Google Maps ger till gång till spatiala API: er via REST-webbtjänster. API-gränssnitten för dessa plattformar utför liknande funktioner. Men de använder olika namngivnings konventioner och svars objekt.

I tabellen visas Azure Maps tjänst-API: er, som har liknande funktioner som listade Google Maps-tjänst-API: er.

| API för Google Maps-tjänsten | API för Azure Maps tjänsten                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Vägbeskrivning              | [Routa](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Avstånds mat ris         | [Väg mat ris](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokodning               | [Sök](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Sök efter platser           | [Sök](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Placera Autoavsluta      | [Sök](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Fäst vid väg            | Se avsnittet [Beräkna vägar och vägvisningar](#calculate-routes-and-directions) .            |
| Hastighets begränsningar            | Se avsnittet [Omvänd landskod a koordinat](#reverse-geocode-a-coordinate) .                  |
| Statisk karta              | [Rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Tidszon               | [Tidszon](https://docs.microsoft.com/rest/api/maps/timezone)                              |

Följande tjänst-API: er är för närvarande inte tillgängliga i Azure Maps:

- Höjning
- Geolocation
- Plats information och foton – telefonnummer och webbplats-URL finns i Azure Maps Search API.
- Kart-URL: er
- Närmaste vägar – detta kan åstadkommas med hjälp av webb-SDK: n som visas [här](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), men inte tillgänglig som en tjänst för närvarande.
- Statisk gata vy

Azure Maps har flera andra REST-webbtjänster som kan vara av intresse:

- [Spatialdata](https://docs.microsoft.com/rest/api/maps/spatial): avlastning av komplexa spatiala beräkningar och åtgärder, till exempel polystaket, till en tjänst.
- [Trafik](https://docs.microsoft.com/rest/api/maps/traffic): åtkomst till trafik flöde och incident data i real tid.

## <a name="geocoding-addresses"></a>Adresser för att koda

Kodning är en process för att konvertera en adress till en koordinat. Till exempel "1 Microsoft Way, Redmond, WA" omvandlar till longitud:-122,1298, latitud: 47,64005. Sedan kan koordinaterna användas för olika typer av syfte, till exempel placering av en markör på en karta.

Azure Maps tillhandahåller flera metoder för att koda adresser:

- [**Kodning av fritt format**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Ange en enskild adress sträng och bearbeta begäran omedelbart. "1 Microsoft Way, Redmond, WA" är ett exempel på en enskild adress sträng. Det här API: et rekommenderas om du snabbt behöver koda enskilda adresser.
- [**Kodning av strukturerad adress**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Ange delar av en enskild adress, till exempel gatu namn, stad, land och post nummer och bearbeta begäran omedelbart. Det här API: et rekommenderas om du behöver en kort kod för enskilda adresser snabbt och data redan har tolkats i sina enskilda adress delar.
- [**Kodning av batch-adresser**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): skapa en begäran som innehåller upp till 10 000 adresser och behandla dem under en viss tids period. Alla adresser kommer att kodas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned. Detta rekommenderas för att kunna koda stora data mängder.
- [**Fuzzy-sökning**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): det här API: et kombinerar adressens kod för att söka efter orienterings punkter. Detta API tar i en sträng med fritt format. Den här strängen kan vara en adress, plats, landmärke, orienterings punkt eller kategori av intressant kategori. Denna API bearbetar begäran nära real tid. Detta API rekommenderas för program där användare söker efter adresser eller intressanta punkter i samma text ruta.
- [**Fuzzy batch-sökning**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): skapa en begäran som innehåller upp till 10 000 adresser, platser, landmärken eller anslags punkter och låta dem bearbetas under en viss tids period. Alla data kommer att bearbetas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned.

Följande tabell innehåller en kors referens till Google Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` och `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`– stad/stad<br/>`municipalitySubdivision`– till, sub/Super City<br/>`countrySubdivision`delstat eller provins<br/>`countrySecondarySubdivision`-län<br/>`countryTertiarySubdivision`– distrikt<br/>`countryCode`– landskod för två bokstäver |
| `key`                       | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen. |
| `language`                  | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Ett exempel på hur du använder Sök tjänsten finns dokumenterat [här](how-to-search-for-address.md). Se till att gå igenom [metod tipsen för sökning](how-to-use-best-practices-for-search.md).

> [!TIP]
> De kostnads fria adresserna för kodning och fuzzy Search-API: er kan användas i läget komplettera `&typeahead=true` automatiskt genom att lägga till i URL: en för begäran. Detta talar om för servern att indatamängden är troligt vis delvis och att sökningen försätts i förutsägande läge.

## <a name="reverse-geocode-a-coordinate"></a>Omvändly-kod a-koordinat

Omvänd polykodning är processen att konvertera geografiska koordinater till en ungefärlig adress. Koordinater med "longitud:-122,1298, latitud: 47,64005" konvertera till "1 Microsoft Way, Redmond, WA".

Azure Maps innehåller flera omvända Omkodnings metoder:

- [**Omvänd landskod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Ange en enda geografisk koordinat för att få den ungefärliga adressen som motsvarar den här koordinaten. Bearbetar begäran nära real tid.
- [**Kors gatan omvänd landskod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Ange en enda geografisk koordinat för att hämta närliggande kors gatan och bearbeta begäran omedelbart. Du kan till exempel få följande Cross Streets, 1st och Main St.
- [**Omvänd landskod för batch-adress**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): skapa en begäran som innehåller upp till 10 000 koordinater och har bearbetats under en viss tids period. Alla data kommer att bearbetas parallellt på servern. När begäran har slutförts kan du ladda ned en fullständig uppsättning resultat.

Den här tabellen kors referenser till Google Maps API-parametrar med jämförbara API-parametrar i Azure Maps.

| Google Maps API-parameter   | Jämförbar Azure Maps API-parameter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen. |
| `language`                  | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *EJ TILLÄMPLIGT*     |
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
- [**Fuzzy-sökning**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): det här API: et kombinerar adressens kod för att söka efter orienterings punkter. Detta API tar i en sträng med fri form som kan vara en adress, plats, landmärke, intresse punkt eller punkt för intresse kategori. Den bearbetar begäran nära real tid. Detta API rekommenderas för program där användare söker efter adresser eller intressanta punkter i samma text ruta.
- [**Sök i geometrin**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Sök efter anslags punkter som är inom en angiven geometri. Du kan till exempel söka efter en orienterings punkt inom en polygon.
- [**Sök längs väg**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Sök efter anslags punkter som är utmed en angiven väg Sök väg.
- [**Fuzzy batch-sökning**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): skapa en begäran som innehåller upp till 10 000 adresser, platser, landmärken eller anslags punkt. Bearbetade begäran under en viss tids period. Alla data kommer att bearbetas parallellt på servern. När begäran har slutfört bearbetningen kan du ladda ned en fullständig uppsättning resultat.

För närvarande har Azure Maps inte något jämförbart API till API: et för texts ökning i Google Maps.

> [!TIP]
> POI search, POI kategori search och fuzzy Search-API: er kan användas i läget komplettera automatiskt genom `&typeahead=true` att lägga till i fråge-URL: en. Detta talar om för servern att indatamängden är troligt vis delvis. API: n kommer att genomföra sökningen i förutsägande läge.

Läs igenom [metod tipsen för Sök efter](how-to-use-best-practices-for-search.md) dokumentation.

### <a name="find-place-from-text"></a>Hitta plats från text

Använd Azure Maps [POI Sök](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) och [fuzzy search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) för att söka efter anslags poäng efter namn eller adress.

Tabellen kors refererar till Google Maps API-parametrar med de jämförbara Azure Maps API-parametrarna.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter |
|---------------------------|-------------------------------------|
| `fields`                  | *EJ TILLÄMPLIGT*                               |
| `input`                   | `query`                             |
| `inputtype`               | *EJ TILLÄMPLIGT*                               |
| `key`                     | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen. |
| `language`                | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` och`radius`<br/>`topLeft` och `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Närliggande sökning

Använd [söknings](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) -API: t i närheten för att hämta närliggande intressanta punkter i Azure Maps.

I tabellen visas Google Maps API-parametrar med de jämförbara Azure Maps API-parametrarna.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen. |
| `keyword`                   | `categorySet` och `brandSet`        |
| `language`                  | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .  |
| `location`                  | `lat` och `lon`                     |
| `maxprice`                  | *EJ TILLÄMPLIGT*                               |
| `minprice`                  | *EJ TILLÄMPLIGT*                               |
| `name`                      | `categorySet` och `brandSet`        |
| `opennow`                   | *EJ TILLÄMPLIGT*                               |
| `pagetoken`                 | `ofs` och `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *EJ TILLÄMPLIGT*                               |
| `type`                      | `categorySet –`Se dokumentation om [Sök kategorier som stöds](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Beräkna vägar och vägbeskrivningar

Beräkna vägar och vägbeskrivning med Azure Maps. Azure Maps har många av samma funktioner som Google Maps-routningstjänsten, till exempel:

- Ankomst-och avgångs tider.
- I real tids-och förutsägande baserade trafik vägar.
- Olika transport sätt. Till exempel, framförande, framförande och bekallning.

> [!NOTE]
> Azure Maps kräver att alla waypoints är koordinater. Adresser måste kodas först.

Tjänsten Azure Maps routning innehåller följande API: er för att beräkna vägar:

- [**Beräkna väg**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): beräkna en väg och behandla begäran omedelbart. Detta API stöder både GET-och POST-förfrågningar. POST-begäranden rekommenderas när du anger ett stort antal waypoints eller när du använder flera väg alternativ för att säkerställa att URL-begäran inte blir för lång och orsaka problem. POSTernas riktnings riktning i Azure Maps har ett alternativ som tar i tusentals [stöd punkter](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) och kommer att använda dem för att återskapa en logisk väg väg mellan dem (fäst på väg). 
- [**Batch-väg**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): skapa en begäran som innehåller upp till 1 000 cirkulations förfrågan och behandla dem under en viss tids period. Alla data kommer att bearbetas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned.
- [**Mobilitets tjänster**](https://docs.microsoft.com/rest/api/maps/mobility): beräkna vägar och vägbeskrivningar med hjälp av offentlig överföring.

Tabellen kors refererar till Google Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps.

| Google Maps API-parameter    | Jämförbar Azure Maps API-parameter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`– koordinater i formatet`"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen. |
| `language`                     | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .   |
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
> Som standard returnerar Azure Maps Route API bara en sammanfattning. Det returnerar avståndet och tiderna och koordinaterna för vägens sökväg. Använd `instructionsType` parametern för att hämta inaktiverade instruktioner. Och Använd `routeRepresentation` parametern för att filtrera ut sammanfattningen och vägens sökväg.

Azure Maps routnings-API har ytterligare funktioner som inte är tillgängliga i Google Maps. När du migrerar din app bör du överväga att använda dessa funktioner, men du kanske tycker att de är användbara.

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

Dessutom stöder Route service i Azure Maps [beräkning av flyttbara intervall](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Beräkning av dirigerbart intervall kallas även ISO Kron. Den genererar en polygon som täcker ett utrymme som kan flyttas till i vilken riktning som helst från en ursprungs punkt. Alla under en angiven tids period eller mängd bränsle eller avgift.

Läs igenom [metod tipsen för routning av](how-to-use-best-practices-for-routing.md) dokumentation.

## <a name="retrieve-a-map-image"></a>Hämta en kart bild

Azure Maps tillhandahåller ett API för att återge statiska kart bilder med data som har överanvänds. [Kart bildens rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) -api i Azure Maps är jämförbart med API: t för statisk mappning i Google Maps.

> [!NOTE]
> Azure Maps kräver att Center, alla markörer och Sök vägarna som ska vara koordinater i formatet "longitud, latitud". Google Maps använder därför formatet "latitud, longitud". Adresser måste vara kodade först.

Tabellen kors refererar till Google Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`– anges som en del av URL-sökvägen. För närvarande stöds endast PNG. |
| `key`                       | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen. |
| `language`                  | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .  |
| `maptype`                   | `layer`och `style` – se dokumentation om [mappnings format som stöds](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *Ej tillämpligt* – det här är en funktion för att koda en funktion. Använd `countrySet` parametern när du använder API: et för Azure Maps-kodning.  |
| `scale`                     | *EJ TILLÄMPLIGT*                              |
| `size`                      | `width`och `height` – kan vara upp till 8192x8192 i storlek. |
| `style`                     | *EJ TILLÄMPLIGT*                              |
| `visible`                   | *EJ TILLÄMPLIGT*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> I Azure Maps panel systemet är paneler två gånger den storlek på kart paneler som används i Google Maps. Eftersom värdet för zoomnings nivå i Azure Maps visas på en zoomnings nivå närmare Azure Maps jämfört med Google Maps. Du kan kompensera för den här skillnaden genom att minska zoomnings nivån i de begär Anden som du migrerar.

Mer information finns i [instruktionen How-to på Map image rendering API](how-to-render-custom-data.md).

Förutom att kunna generera en statisk kart bild ger tjänsten Azure Maps åter givning möjlighet att direkt komma åt kart paneler i raster (PNG) och vektor format:

- [**Kart panel**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Hämta raster (png) och vektor paneler för bas Maps (vägar, gränser, bakgrund).
- [**Panel för kart bilder**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): Hämta paneler för flyg-och satellit bilder.

> [!TIP]
> Många Google Maps-program där växlar från interaktiva kart upplevelser till statiska kart bilder några år sedan. Detta skedde som en metod för att spara pengar. I Azure Maps är det oftast mer kostnads effektivt att använda den interaktiva kart kontrollen i Web SDK. De interaktiva kart kontroll avgifterna baseras på antalet panel belastningar. Kart paneler i Azure Maps är stora. Det tar ofta bara några paneler att återskapa samma Map-vy som en statisk karta. Kart paneler cachelagras automatiskt av webbläsaren. Det innebär att den interaktiva kart kontrollen ofta genererar en bråkdel av en transaktion när du skapar en statisk kart visning. Panorering och zoomning kommer att läsa in fler paneler. Det finns dock alternativ i kart kontrollen för att inaktivera det här beteendet. Den interaktiva kart kontrollen tillhandahåller också många fler visualiserings alternativ än de statiska kart tjänsterna.

### <a name="marker-url-parameter-format-comparison"></a>Jämförelse av parameter format för markör-URL

**Före: Google Maps**

Lägg till markörer `markers` med hjälp av parametern i URL: en. `markers` Parametern tar i ett format och en lista över platser som ska återges på kartan med det formatet enligt nedan:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Om du vill lägga till fler stilar `markers` använder du parametrarna till URL: en med ett annat format och en uppsättning med platser.

Ange markör platser med formatet "latitud, longitud".

Lägg till markör format med `optionName:value` formatet, med flera format åtskiljda med pipe\|()-tecken som detta "optionName1\|: värde1 optionName2: värde2". Observera att alternativ namn och värden åtskiljs med kolon (:). Använd följande namn på stil-alternativet för att formatera markörer i Google Maps:

- `color`– Färgen på standard markör ikonen. Kan vara en 24-bitars hexadecimal färg (`0xrrggbb`) eller något av följande värden: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`– Ett enkelt alfanumeriskt tecken som ska visas ovanpå ikonen.
- `size`– Markörens storlek. Kan vara `tiny`, `mid`eller `small`.

Använd följande format alternativ namn för anpassade ikoner i Google Maps:

- `anchor`– Anger hur ikon bilden justeras mot koordinaten. Kan vara ett pixel värde (x, y) eller något av följande värden: `top`, `bottom` `left`, `bottomleft` `bottomright`,, `center`,,, eller. `topleft` `right` `topright`
- `icon`– En URL som pekar mot ikon bilden.

Vi kan till exempel lägga till en röd, medels Tor markör till kartan vid longitud:-110, latitud: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps-markör](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Efter: Azure Maps**

Lägg till markörer till en statisk kart bild genom `pins` att ange parametern i URL: en. Som Google Maps anger du ett format och en lista över platser i parametern. `pins` Parametern kan anges flera gånger för att stödja markörer med olika format.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Om du vill använda ytterligare format lägger `pins` du till ytterligare parametrar till URL: en med ett annat format och en uppsättning med platser.

I Azure Maps måste PIN-platsen vara i formatet "longitud latitud". Google Maps använder formatet "latitud, longitud". Ett blank steg, inte ett kommatecken, avgränsar longitud och latitud i Azure Maps format.

`iconType` Anger vilken typ av PIN-kod som ska skapas. Den kan ha följande värden:

- `default`– Standard ikonen för PIN-kod.
- `none`– Ingen ikon visas, endast etiketter kommer att återges.
- `custom`– Anger att en anpassad ikon ska användas. En URL som pekar på ikon bilden kan läggas till i slutet av `pins` parametern efter PIN-kodens plats information.
- `{udid}`– Ett unikt data-ID (UDID) för en ikon som lagras i Azure Maps data lagrings plattform.

Lägg till PIN-format `optionNameValue` med formatet. Separera flera format med pipe ()\|-tecknen. Till exempel: `iconType|optionName1Value1|optionName2Value2`. Alternativ namn och värden är inte separerade. Använd följande format alternativ namn till format markörer:

- `al`– Anger markörens opacitet (alfa). Välj ett tal mellan 0 och 1.
- `an`– Anger fäst punkten. Ange X-och y-pixelvärdena i formatet "X y".
- `co`– PIN-kodens färg. Ange en 24-bitars hexadecimal färg: `000000` till `FFFFFF`.
- `la`– Anger etikettens ankare. Ange X-och y-pixelvärdena i formatet "X y".
- `lc`– Etikettens färg. Ange en 24-bitars hexadecimal färg: `000000` till `FFFFFF`.
- `ls`– Etikettens storlek i bild punkter. Välj ett tal som är större än 0.
- `ro`– Ett värde i grader för att rotera ikonen. Välj ett tal mellan-360 och 360.
- `sc`– Ett skalnings värde för PIN-ikonen. Välj ett tal som är större än 0.

Ange etikett värden för varje PIN-plats. Den här metoden är mer effektiv än att använda ett enda etikett värde för alla markörer i listan över platser. Etikett svärdet kan vara en sträng med flera tecken. Omsluta strängen med enkla citat tecken för att säkerställa att den inte är förväxlad som ett format eller ett plats värde.

Nu ska vi lägga till en`FF0000`röd () standard ikon med etiketten "utrymmes nål", placerad under (15 50). Ikonen är i longitud:-122,349300, latitud: 47,620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps markör](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Lägg till tre PIN-koder med etikett värden "1", "2" och "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps flera markörer](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Sök vägs-URL parameter format jämförelse

**Före: Google Maps**

Lägg till linjer och polygon till en statisk kart bild med `path` hjälp av parametern i URL: en. `path` Parametern tar i ett format och en lista över platser som ska återges på kartan, enligt nedan:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Använd ytterligare format genom att lägga `path` till ytterligare parametrar till URL: en med ett annat format och en uppsättning med platser.

Sök vägs platser anges med `latitude1,longitude1|latitude2,longitude2|…` formatet. Sökvägar kan vara kodade eller innehålla adresser för punkter.

Lägg till Sök vägs format `optionName:value` med formatet, separera flera format med pipe (\|)-tecknen. Och separata alternativ namn och värden med kolon (:). Så här: `optionName1:value1|optionName2:value2`. Följande format alternativ namn kan användas för att formatera sökvägar i Google Maps:

- `color`– Färgen på banan eller polygonens kontur. Kan vara en 24-bitars hexadecimal färg (`0xrrggbb`), en 32-bitars hex-färg`0xrrggbbbaa`() eller något av följande värden: svart, brun, grön, lila, gul, blå, grå, orange, röd, vit.
- `fillColor`– Färgen för att fylla Ban ytan med (Polygon). Kan vara en 24-bitars hexadecimal färg (`0xrrggbb`), en 32-bitars hex-färg`0xrrggbbbaa`() eller något av följande värden: svart, brun, grön, lila, gul, blå, grå, orange, röd, vit.
- `geodesic`– Anger om sökvägen ska vara en linje som följer jordens böjning.
- `weight`– Bredden på Sök vägs linjen i bild punkter.

Lägg till en röd linje opacitet och pixel tjock lek på kartan mellan koordinaterna i URL-parametern. I exemplet nedan har linjen 50% opacitet och en tjocklek på fyra bild punkter. Koordinaterna är longitud:-110, latitud: 45 och longitud:-100, latitud: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps-sammansatt linje](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Efter: Azure Maps**

Lägg till linjer och polygoner till en statisk kart bild genom att `path` ange parametern i URL: en. Som Google Maps anger du ett format och en lista över platser i den här parametern. Ange `path` parametern flera gånger för att återge flera cirklar, linjer och polygoner med olika format.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

När den kommer till Sök vägs platser kräver Azure Maps att koordinaterna ska vara i formatet "longitud Latitude". Google Maps använder formatet "latitud, longitud". Ett blank steg, inte ett kommatecken, avgränsar longitud och latitud i Azure Maps format. Azure Maps stöder inte kodade sökvägar eller adresser för punkter. Ladda upp större data uppsättningar som en polyjson-fil i Azure Maps data Storage API som dokumenteras [här](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Lägg till Sök vägs format `optionNameValue` med formatet. Separera flera format per pipe (\|)-tecken, som `optionName1Value1|optionName2Value2`det här. Alternativ namn och värden är inte separerade. Använd följande format alternativ namn för att formatera sökvägar i Azure Maps:

- `fa`– Fyllnings färg opaciteten (alpha) som används vid åter givning av polygoner. Välj ett tal mellan 0 och 1.
- `fc`– Fyllnings färgen som används för att återge ytan i en polygon.
- `la`– Linje färg opaciteten (alfa) som används vid åter givning av linjer och konturen av polygoner. Välj ett tal mellan 0 och 1.
- `lc`– Linje färgen som används för att återge linjer och konturen för polygoner.
- `lw`– Bredden på linjen i bild punkter.
- `ra`– Anger en cirkel-radie i meter.

Lägg till en röd linje opacitet och pixel tjock lek mellan koordinaterna i URL-parametern. I exemplet nedan har linjen 50% opacitet och en tjocklek på fyra bild punkter. Koordinaterna har följande värden: longitud:-110, latitud: 45 och longitud:-100, latitud: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps polylinje](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Beräkna en avstånds mat ris

Azure Maps tillhandahåller API för avstånds mat ris. Använd detta API för att beräkna res tiderna och avståndet mellan en uppsättning platser, med en avstånds mat ris. Det är jämförbart med API: et för avstånds mat ris i Google Maps.

- [**Väg mat ris**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): beräknar asynkrona res tider och avstånd för en uppsättning ursprung och mål. Stöder upp till 700 celler per begäran. Det är antalet ursprung multiplicerat med antalet destinationer. Med den begränsningen i åtanke är exempel på möjliga mat ris dimensioner: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> En begäran till distans mat ris API: et kan bara göras med en POST-begäran med information om ursprung och mål i bröd texten i begäran. Dessutom kräver Azure Maps att alla ursprung och mål måste vara koordinater. Adresser måste vara kodade först.

Den här tabellen kors referenser till Google Maps API-parametrar med de jämförbara Azure Maps API-parametrarna.

| Google Maps API-parameter      | Jämförbar Azure Maps API-parameter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`– Ange i POSTens brödtext som en JSON. |
| `key`                          | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen. |
| `language`                     | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`– Ange i POSTens brödtext som en JSON.  |
| `region`                       | *Ej tillämpligt* – den här funktionen är en kod relaterad. Använd `countrySet` parametern när du använder API: et för Azure Maps-kodning. |
| `traffic_model`                | *Ej tillämpligt* – kan bara ange om trafik data ska användas med `traffic` parametern. |
| `transit_mode`                 | Det finns för närvarande inte stöd för *N/A* -transit-baserade avstånds-matriser.  |
| `transit_routing_preference`   | Det finns för närvarande inte stöd för *N/A* -transit-baserade avstånds-matriser.  |
| `units`                        | *Ej tillämpligt* – Azure Maps endast använder Metric-systemet. |

> [!TIP]
> Alla avancerade cirkulations alternativ som är tillgängliga i Azure Maps routing API stöds i Azure Maps avstånds mat ris API. Alternativen för avancerad routning omfattar: Truck-routning, motor specifikationer och så vidare.

Läs igenom [metod tipsen för routning av](how-to-use-best-practices-for-routing.md) dokumentation.

## <a name="get-a-time-zone"></a>Hämta en tidszon

Azure Maps tillhandahåller ett API för att hämta tids zonen för en koordinat. Azure Maps tidszons-API: et är jämförbar med tids zons-API: et i Google Maps:

- [**Tidszon per koordinat**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Ange en koordinat och ta emot tids zons informationen för koordinaten.

Den här tabellen kors referenser till Google Maps API-parametrar med jämförbara API-parametrar i Azure Maps.

| Google Maps API-parameter | Jämförbar Azure Maps API-parameter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Se även [autentiseringen med Azure Maps](azure-maps-authentication.md) -dokumentationen.       |
| `language`                  | `language`– Se dokumentation om [språk som stöds](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Förutom detta API tillhandahåller Azure Maps ett antal API: er för tids zoner. Dessa API: er konverterar tiden baserat på namn eller ID för tids zonen:

- [**Tidszon med ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): returnerar aktuell, historisk och framtida tids zons information för angivet ID för IANA-tidszon.
- [**Tids zons uppräknings-IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): returnerar en fullständig lista över IANA-tidszonens ID. Uppdateringar av tjänsten IANA visas i systemet inom en dag.
- [**Enum för tidszon Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): returnerar en fullständig lista över Windows tids zons-ID: n.
- [**Version för tids zonens IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): returnerar det aktuella IANA-versions numret som används av Azure Maps.
- [**Time Zone Windows till IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): returnerar ett motsvarande IANA-ID, med ett giltigt Windows-tidszons-ID. Flera IANA-ID: n kan returneras för ett enda Windows-ID.

## <a name="client-libraries"></a>Klientbibliotek

Azure Maps tillhandahåller klient bibliotek för följande programmeringsspråk:

- Java Script, typescript, Node. js – [dokumentation](how-to-use-services-module.md) \| [NPM-paket](https://www.npmjs.com/package/azure-maps-rest)

Dessa klient bibliotek med öppen källkod är för andra programmeringsspråk:

- .Net standard 2,0 – [GitHub Project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-paket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Ytterligare resurser

Här följer ytterligare dokumentation och resurser för Azure Maps REST-tjänsterna.

- [Metodtips för sökning](how-to-use-best-practices-for-search.md)
- [Söka efter en adress](how-to-search-for-address.md)
- [Metod tips för routning](how-to-use-best-practices-for-routing.md)
- [Dokumentation om Azure Maps REST service API-referens](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps REST-tjänsterna.

> [!div class="nextstepaction"]
> [Metod tips för att använda Sök tjänsten](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Metod tips för att använda routningstjänsten](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Så här använder du modulen tjänster (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)
