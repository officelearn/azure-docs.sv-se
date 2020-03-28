---
title: 'Handledning: Migrera webbtjänster från Google Maps | Microsoft Azure Maps'
description: Så här migrerar du webbtjänster från Google Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2f25f2b786686b8af9bad4ea8ce3c8aea9b589f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371461"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrera webbtjänst från Google Maps

Både Azure och Google Maps ger åtkomst till rumsliga API:er via REST-webbtjänster. API-gränssnitten för dessa plattformar utför liknande funktioner. Men de använder var och en olika namngivningskonventioner och svarsobjekt.

Tabellen visar Azure Maps-tjänst-API:er, som har liknande funktioner som de listade Google Maps-tjänst-API:erna.

| Api för Tjänsten Google Maps | Api för Azure Maps-tjänsten                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Vägbeskrivning              | [Routa](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Avstånd Matrix         | [Ruttmatris](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokodning               | [Sök](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Platser Sök           | [Sök](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Placera Komplettera automatiskt      | [Sök](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Fäst mot väg            | Se [Avsnittet Beräkna rutter och vägbeskrivningar.](#calculate-routes-and-directions)            |
| Hastighetsgränser            | Se [Omvänd geokod ett koordinatavsnitt.](#reverse-geocode-a-coordinate)                  |
| Statisk karta              | [Rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Tidszon               | [Tidszon](https://docs.microsoft.com/rest/api/maps/timezone)                              |

Följande tjänst-API:er är för närvarande inte tillgängliga i Azure Maps:

- Höjd
- Geolocation
- Platser detaljer och foton - Telefonnummer och webbadress är tillgängliga i Azure Maps sök-API.
- Mappa webbadresser
- Närmaste vägar - Detta kan uppnås med hjälp av Web SDK som visas [här](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), men inte tillgänglig som en tjänst för närvarande.
- Statisk gatuvy

Azure Maps har flera ytterligare REST-webbtjänster som kan vara av intresse:

- [Rumsliga operationer](https://docs.microsoft.com/rest/api/maps/spatial): Avlasta komplexa rumsliga beräkningar och operationer, till exempel geofencing, till en tjänst.
- [Trafik](https://docs.microsoft.com/rest/api/maps/traffic): Få tillgång till trafikflöde och incidentdata i realtid.

## <a name="geocoding-addresses"></a>Geokodningsadresser

Geokodning är processen att konvertera en adress till en koordinat. Till exempel konverteras "1 Microsoft-sätt, Redmond, WA" till longitud: -122.1298, latitud: 47.64005. Sedan kan koordinater användas för olika typer av ändamål, till exempel att placera en centrering av en markör på en karta.

Azure Maps innehåller flera metoder för geokodningsadresser:

- [**Geokodning av frihandsadress:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)Ange en enda adresssträng och bearbeta begäran omedelbart. "1 Microsoft sätt, Redmond, WA" är ett exempel på en enda adress sträng. Det här API:et rekommenderas om du snabbt behöver geokoda enskilda adresser.
- [**Strukturerad adressgeokodning**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Ange de delar av en enskild adress, till exempel gatunamn, ort, land och postnummer och bearbeta begäran omedelbart. Det här API:et rekommenderas om du behöver geokoda enskilda adresser snabbt och data redan tolkas i dess enskilda adressdelar.
- [**Batchadressgeokodning**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Skapa en begäran som innehåller upp till 10 000 adresser och få dem bearbetade under en tidsperiod. Alla adresser kommer att geokodas parallellt på servern och när den är klar kan den fullständiga resultatuppsättningen laddas ner. Detta rekommenderas för geokodning av stora datauppsättningar.
- [**Fuzzy sökning:**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)Detta API kombinerar adress geokodning med intressepunkt sökning. Det här API:et har en sträng i fri form. Den här strängen kan vara en adress, plats, landmärke, intressepunkt eller intressepunktskategori. Detta API bearbetar begäran nära realtid. Det här API:et rekommenderas för program där användare söker efter adresser eller intressanta platser i samma textruta.
- [**Fuzzy batch sökning:**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)Skapa en begäran som innehåller upp till 10.000 adresser, platser, landmärken eller intressepunkt och få dem bearbetade under en tidsperiod. Alla data kommer att behandlas parallellt på servern och när den är klar kan den fullständiga resultatuppsättningen laddas ner.

Följande tabell korsrefererar Google Maps API-parametrar med jämförbara API-parametrar i Azure Maps.

| API-parametern Google Maps | Jämförbar AZURE Maps API-parameter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` och `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`- stad / stad<br/>`municipalitySubdivision`– grannskap, sub / super stad<br/>`countrySubdivision`- delstat eller provins<br/>`countrySecondarySubdivision`- län<br/>`countryTertiarySubdivision`- distrikt<br/>`countryCode`- Två bokstäver landskod |
| `key`                       | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)  |
| `region`                    | `countrySet`                       |

Ett exempel på hur du använder söktjänsten dokumenteras [här](how-to-search-for-address.md). Var noga med att granska [metodtips för sökning](how-to-use-best-practices-for-search.md).

> [!TIP]
> Geokodnings- och fuzzy-api:erna för frihandsadress kan användas `&typeahead=true` i automatiskt kompletteringsläge genom att lägga till url:en för begäran. Detta kommer att tala om för servern att indatatexten sannolikt är partiell och sökningen kommer att gå in i prediktivt läge.

## <a name="reverse-geocode-a-coordinate"></a>Omvänd geokoda en koordinat

Omvänd geokodning är processen att konvertera geografiska koordinater till en ungefärlig adress. Koordinater med "longitud: -122.1298, latitud: 47.64005" konvertera till "1 Microsoft sätt, Redmond, WA".

Azure Maps innehåller flera omvända geokodningsmetoder:

- [**Omvänd adress geokodare:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)Ange en enda geografisk koordinat för att hämta den ungefärliga adressen som motsvarar den här koordinaten. Bearbetar begäran nära realtid.
- [**Cross Street omvänd geokodare:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)Ange en enda geografisk koordinat för att få närliggande cross street information och bearbeta begäran omedelbart. Till exempel kan du få följande tvärgator 1st Ave och Main St.
- [**Omvänd geokodare för batchadress:**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)Skapa en begäran som innehåller upp till 10 000 koordinater och få dem bearbetade under en tidsperiod. Alla data kommer att bearbetas parallellt på servern. När begäran är klar kan du hämta hela uppsättningen resultat.

Den här tabellen korsreferererar Google Maps API-parametrar med jämförbara API-parametrar i Azure Maps.

| API-parametern Google Maps   | Jämförbar AZURE Maps API-parameter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)  |
| `latlng`                    | `query`  |
| `location_type`             | *Ej tillämpligt*     |
| `result_type`               | `entityType`    |

Granska [metodtips för sökning](how-to-use-best-practices-for-search.md).

Azure Maps omvänd geokodning API har några ytterligare funktioner, som inte är tillgängliga i Google Maps. Dessa funktioner kan vara användbara för att integrera med ditt program när du migrerar appen:

- Hämta hastighetsbegränsningsdata
- Hämta information om väganvändning: lokal väg, arteriell, begränsad åtkomst, ramp och så vidare
- Hämta den sida av gatan där en koordinat finns

## <a name="search-for-points-of-interest"></a>Söka efter platser av intresse

Intressanta data kan sökas i Google Maps med hjälp av api:et för platssökning. Det här API:et innehåller tre olika sätt att söka efter intressanta platser:

- **Hitta plats från text:** Söker efter en intressepunkt baserat på namn, adress eller telefonnummer.
- **Sökning i närheten**: Söker efter intressanta platser som ligger inom ett visst avstånd från en plats.
- **TextSökning:** Söker efter platser med hjälp av en frihandstext, som innehåller intressepunkt och platsinformation. Till exempel "pizza i New York" eller "restauranger nära main st".

Azure Maps innehåller flera sök-API:er för intressanta platser:

- [**POI-sökning:**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)Sök efter intressanta platser efter namn. Till exempel "Starbucks".
- [**POI kategori sökning:**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)Sök efter intressanta platser efter kategori. Till exempel "restaurang".
- [**Sökning i närheten**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Söker efter intressanta platser som ligger inom ett visst avstånd från en plats.
- [**Fuzzy sökning:**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)Detta API kombinerar adress geokodning med intressepunkt sökning. Det här API:et har en sträng i fri form som kan vara en adress, plats, landmärke, intressepunkt eller intressepunktskategori. Den bearbetar begäran nära realtid. Det här API:et rekommenderas för program där användare söker efter adresser eller intressanta platser i samma textruta.
- [**Sök inom geometri**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Sök efter intressanta platser som finns inom en angiven geometri. Sök till exempel efter en intressepunkt inom en polygon.
- [**Sök längs rutt**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Sök efter intressanta platser som ligger längs en angiven ruttsökväg.
- [**Fuzzy batch sökning:**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)Skapa en begäran som innehåller upp till 10.000 adresser, platser, landmärken, eller intressepunkt. Bearbetade begäran under en viss tidsperiod. Alla data kommer att bearbetas parallellt på servern. När begäran är klar kan du hämta hela uppsättningen resultat.

Azure Maps har för närvarande inte ett jämförbart API som textsök-API:et i Google Maps.

> [!TIP]
> POI-sökning, POI-kategorisökning och fuzzy search API:er kan användas `&typeahead=true` i automatiskt komplettera läge genom att lägga till url:en för begäran. Detta kommer att tala om för servern att indatatexten sannolikt är partiell. API:et utför sökningen i prediktivt läge.

Läs [metodtipsen för](how-to-use-best-practices-for-search.md) sökdokumentation.

### <a name="find-place-from-text"></a>Sök plats från text

Använd Azure Maps [POI-sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) och [fuzzy-sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) för att söka efter intressanta platser efter namn eller adress.

Tabellen korsrefererar Google Maps API-parametrar med jämförbara Azure Maps API-parametrar.

| API-parametern Google Maps | Jämförbar AZURE Maps API-parameter |
|---------------------------|-------------------------------------|
| `fields`                  | *Ej tillämpligt*                               |
| `input`                   | `query`                             |
| `inputtype`               | *Ej tillämpligt*                               |
| `key`                     | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md) |
| `language`                | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)  |
| `locationbias`            | `lat`och `lon``radius`<br/>`topLeft` och `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Sökning i närheten

Använd [sök-API:et i närheten](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) för att hämta intressanta platser i närheten i Azure Maps.

Tabellen visar Google Maps API-parametrar med jämförbara Azure Maps API-parametrar.

| API-parametern Google Maps | Jämförbar AZURE Maps API-parameter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md) |
| `keyword`                   | `categorySet` och `brandSet`        |
| `language`                  | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)  |
| `location`                  | `lat` och `lon`                     |
| `maxprice`                  | *Ej tillämpligt*                               |
| `minprice`                  | *Ej tillämpligt*                               |
| `name`                      | `categorySet` och `brandSet`        |
| `opennow`                   | *Ej tillämpligt*                               |
| `pagetoken`                 | `ofs` och `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *Ej tillämpligt*                               |
| `type`                      | `categorySet –`Se dokumentation [för sökkategorier som stöds.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Beräkna rutter och vägbeskrivningar

Beräkna rutter och vägbeskrivningar med Hjälp av Azure Maps. Azure Maps har många av samma funktioner som routningstjänsten i Google Maps, till exempel:

- Ankomst- och avgångstider.
- Realtids- och prediktiva trafikvägar.
- Olika transportsätt. Såsom körning, promenader, cykling.

> [!NOTE]
> Azure Maps kräver att alla waypoints ska vara koordinater. Adresser måste geokodas först.

Routningstjänsten Azure Maps tillhandahåller följande API:er för beräkning av vägar:

- [**Beräkna flöde:**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)Beräkna en flöde och få begäran bearbetad omedelbart. Det här API:et stöder både GET- och POST-begäranden. POST-begäranden rekommenderas när du anger ett stort antal waypoints eller när du använder många av ruttalternativen för att säkerställa att URL-begäran inte blir för lång och orsakar problem. POST-ruttriktningen i Azure Maps har ett alternativ som tar in tusentals [stödpunkter](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) och använder dem för att återskapa en logisk vägväg mellan dem (fäst mot väg). 
- [**Batchflöde**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Skapa en begäran som innehåller upp till 1 000 flödesbegäran och få dem bearbetade under en tidsperiod. Alla data kommer att behandlas parallellt på servern och när den är klar kan den fullständiga resultatuppsättningen laddas ner.
- [**Mobilitetstjänster**](https://docs.microsoft.com/rest/api/maps/mobility): Beräkna rutter och vägbeskrivningar med kollektivtrafik.

Tabellen korsrefererar Google Maps API-parametrar med jämförbara API-parametrar i Azure Maps.

| API-parametern Google Maps    | Jämförbar AZURE Maps API-parameter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`– koordinater i formatet`"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *Saknas* – Den här funktionen är geokodningsrelaterad. Använd *parametern countrySet* när du använder Azure Maps geokodnings-API: ning.  |
| `traffic_model`               | *Ej till* - Kan bara ange om trafikdata ska användas med trafikparametern. *traffic* |
| `transit_mode`                | Se [dokumentation för mobilitetstjänster](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Se [dokumentation för mobilitetstjänster](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *Saknas* – Azure Maps använder bara måttsystemet.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Som standard returnerar Azure Maps route API bara en sammanfattning. Den returnerar avstånd och tider och koordinaterna för ruttbanan. Använd `instructionsType` parametern för att hämta turn-by-turn-instruktioner. Och använd `routeRepresentation` parametern för att filtrera bort sammanfattningen och vägsökvägen.

Azure Maps routning API har ytterligare funktioner som inte är tillgängliga i Google Maps. När du migrerar appen kan du använda dessa funktioner.

- Stöd för rutttyp: kortast, snabbaste, drillande och mest bränsleeffektiva.
- Stöd för ytterligare reselägen: buss, motorcykel, taxi, lastbil och skåpbil.
- Stöd för 150 waypoints.
- Beräkna flera restider i en enda begäran. historisk trafik, direktsänd trafik, ingen trafik.
- Undvik ytterligare vägtyper: samåkningsvägar, grusvägar, redan använda vägar.
- Ange anpassade områden att undvika.
- Begränsa höjden, som rutten kan stiga upp.
- Rutt baserad på motorspecifikationer. Beräkna rutter för förbrännings- eller elfordon baserat på motorspecifikationer och resterande bränsle eller laddning.
- Stöd parametrar för nyttofordonsrutt. Till exempel fordonsdimensioner, vikt, antal axlar och lasttyp.
- Ange maximal fordonshastighet.

Utöver detta stöder flödestjänsten i Azure Maps [beräkning av dirigerbara intervall](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Beräkning av dirigerbara intervall kallas också isochrones. Det innebär att generera en polygon som täcker ett område som kan resas till i någon riktning från en ursprungspunkt. Allt under en angiven tid eller mängd bränsle eller laddning.

Granska [metodtipsen för routningsdokumentation.](how-to-use-best-practices-for-routing.md)

## <a name="retrieve-a-map-image"></a>Hämta en kartbild

Azure Maps tillhandahåller ett API för rendering av statiska kartavbildningar med överlagrad data. [Kartbilden gör](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API i Azure Maps är jämförbar med det statiska kart-API:et i Google Maps.

> [!NOTE]
> Azure Maps kräver mitten, alla markören och sökvägsplatserna som koordinater i formatet "longitud,latitude". Medan Google Maps använder formatet "latitud,longitud". Adresser måste geokodas först.

Tabellen korsrefererar Google Maps API-parametrar med jämförbara API-parametrar i Azure Maps.

| API-parametern Google Maps | Jämförbar AZURE Maps API-parameter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`– anges som en del av URL-sökvägen. För närvarande stöds endast PNG. |
| `key`                       | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)  |
| `maptype`                   | `layer`och `style` – Se dokumentation [för kartformat som stöds.](supported-map-styles.md) |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *Ej till någon* – Det här är en geokodningsrelaterad funktion. Använd `countrySet` parametern när du använder Azure Maps geokodnings-API: et.  |
| `scale`                     | *Ej tillämpligt*                              |
| `size`                      | `width`och `height` – kan vara upp till 8192x8192 i storlek. |
| `style`                     | *Ej tillämpligt*                              |
| `visible`                   | *Ej tillämpligt*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> I azure maps-panelsystemet är paneler dubbelt så stora som kartpaneler som används i Google Maps. Som sådan zoomnivå värdet i Azure Maps kommer att visas en zoomnivå närmare i Azure Maps jämfört med Google Maps. För att kompensera för den här skillnaden minska zoomnivån i de begäranden som du migrerar.

Mer information finns i [hur du kan styra på kartbilden rendera API](how-to-render-custom-data.md).

Förutom att kunna generera en statisk kartbild ger Azure Maps renderingstjänsten möjlighet att direkt komma åt kartpaneler i rasterformat (PNG) och vektorformat:

- [**Kartpanel**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Hämta raster (PNG) och vektorpaneler för baskartor (vägar, gränser, bakgrund).
- [**Karta bilder kakel:**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)Hämta antenn och satellit bilder plattor.

> [!TIP]
> Många Google Maps-program byttes från interaktiva kartupplevelser till statiska kartbilder för några år sedan. Detta gjordes som en kostnadsbesparande metod. I Azure Maps är det oftast mer kostnadseffektivt att använda den interaktiva kartkontrollen i Web SDK. Den interaktiva kartkontroll avgifter baserat antalet kakel laster. Kartpaneler i Azure Maps är stora. Ofta tar det bara ett fåtal paneler för att återskapa samma kartvy som en statisk karta. Kartpaneler cachelagras automatiskt av webbläsaren. Den interaktiva kartkontrollen genererar därför ofta en bråkdel av en transaktion när du återskapar en statisk kartvy. Panorering och zoomning kommer att ladda fler brickor; Det finns dock alternativ i kartkontrollen för att inaktivera det här beteendet. Den interaktiva kartkontrollen ger också mycket mer visualiseringsalternativ än de statiska karttjänsterna.

### <a name="marker-url-parameter-format-comparison"></a>Jämförelse av parameterformat för markör-URL

**Före: Google Maps**

Lägg till markörer med parametern `markers` i URL:en. Parametern `markers` tar i ett format och en lista över platser som ska återges på kartan med det formatet som visas nedan:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Om du vill lägga `markers` till ytterligare format använder du parametrarna i URL:en med ett annat format och en uppsättning platser.

Ange markörplatser med formatet "latitud,longitud".

Lägg till markörformat med `optionName:value` formatet, med\|flera format avgränsade med pipe\|( ) tecken som det här alternativetName1:value1 optionName2:value2". Alternativets namn och värden avgränsas med ett kolon (:). Använd följande namn på stilalternativet för att formatera markörer i Google Maps:

- `color`– Färgen på standardmarkörikonen. Kan vara en 24-bitars`0xrrggbb`hex färg ( ) eller något av följande värden; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`– Ett enda alfanumeriskt tecken med versaler som ska visas ovanpå ikonen.
- `size`- Markörens storlek. Kan `tiny`vara `mid`, `small`eller .

Använd följande formatalternativnamn för anpassade ikoner i Google Maps:

- `anchor`– Anger hur ikonen ska justeras mot koordinaten. Kan vara ett pixelvärde (x,y) eller något av följande värden. `top`, `bottom` `left`, `right` `center`, `topleft` `topright`, `bottomleft`, `bottomright`, , eller .
- `icon`– En url som pekar på ikonbilden.

Till exempel ska vi lägga till en röd, medelstor markör på kartan vid longitud: -110, latitud: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps markör](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Efter: Azure Maps**

Lägg till markörer i en statisk `pins` kartbild genom att ange parametern i URL:en. Precis som Google Maps anger du ett format och en lista över platser i parametern. Parametern `pins` kan anges flera gånger för att stödja markörer med olika format.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Om du vill använda `pins` ytterligare format lägger du till ytterligare parametrar i URL:en med ett annat format och en uppsättning platser.

I Azure Maps måste fästplatsen vara i formatet "longitud latitude". Google Maps använder formatet "latitud,longitud". Ett blanksteg, inte ett kommatecken, separerar longitud och latitud i Azure Maps-formatet.

Anger `iconType` vilken typ av stift som ska skapas. Den kan ha följande värden:

- `default`– Standardstiftsikonen.
- `none`– Ingen ikon visas, bara etiketter återges.
- `custom`– Anger att en anpassad ikon ska användas. En URL som pekar på ikonbilden kan `pins` läggas till i slutet av parametern efter pin-platsinformationen.
- `{udid}`– Ett unikt data-ID (UDID) för en ikon som lagras i Azure Maps Data Storage-plattformen.

Lägg till pin-format med `optionNameValue` formatet. Separera flera format med\|pipe () tecken. Till exempel: `iconType|optionName1Value1|optionName2Value2`. Alternativnamnen och värdena är inte åtskilda. Använd följande formatalternativnamn för att formatera markörer:

- `al`– Anger markörens opacitet (alfa). Välj ett tal mellan 0 och 1.
- `an`– Anger stiftankaret. Ange X- och y-pixelvärden i formatet "x y".
- `co`– Färgen på stiftet. Ange en 24-bitars `000000` hexfärg: till `FFFFFF`.
- `la`– Anger etikettankaret. Ange X- och y-pixelvärden i formatet "x y".
- `lc`– Etikettens färg. Ange en 24-bitars `000000` hexfärg: till `FFFFFF`.
- `ls`– Etikettens storlek i pixlar. Välj ett tal som är större än 0.
- `ro`– Ett värde i grader för att rotera ikonen. Välj ett tal mellan -360 och 360.
- `sc`– Ett skalvärde för stiftikonen. Välj ett tal som är större än 0.

Ange etikettvärden för varje stiftplats. Den här metoden är effektivare än att använda ett enda etikettvärde på alla markörer i listan över platser. Etikettvärdet kan vara en sträng med flera tecken. Radbryta strängen med enstaka citattecken för att säkerställa att den inte misstas som ett format- eller platsvärde.

Låt oss lägga till`FF0000`en röd ( ) standardikon, med etiketten "Space Needle", placerad nedan (15 50). Ikonen är på longitud: -122.349300, latitud: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Markören Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Lägg till tre stift med etikettvärdena "1", "2" och "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps flera markörer](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Jämförelse av url-parameterformat för sökväg

**Före: Google Maps**

Lägg till linjer och polygon i `path` en statisk kartbild med parametern i URL:en. Parametern `path` tar i ett format och en lista över platser som ska återges på kartan, som visas nedan:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Använd ytterligare format `path` genom att lägga till ytterligare parametrar i URL:en med ett annat format och en uppsättning platser.

Sökvägsplatser anges `latitude1,longitude1|latitude2,longitude2|…` med formatet. Sökvägar kan kodas eller innehålla adresser för poäng.

Lägg till banformat med `optionName:value` formatet, separera\|flera format med pipe () tecken. Och separata alternativnamn och värden med ett kolon (:). Så här: `optionName1:value1|optionName2:value2`. Följande formatalternativnamn kan användas för att formatera sökvägar i Google Maps:

- `color`– Färgen på banan eller polygonkonturen. Kan vara en 24-bitars`0xrrggbb`hex färg ( ), en`0xrrggbbbaa`32-bitars hex färg ( ) eller något av följande värden: svart, brun, grön, lila, gul, blå, grå, orange, röd, vit.
- `fillColor`– Färgen som ska fyllas i banområdet med (polygon). Kan vara en 24-bitars`0xrrggbb`hex färg ( ), en`0xrrggbbbaa`32-bitars hex färg ( ) eller något av följande värden: svart, brun, grön, lila, gul, blå, grå, orange, röd, vit.
- `geodesic`– Anger om banan ska vara en linje som följer jordens krökning.
- `weight`– Tjockleken på banlinjen i pixlar.

Lägg till en röd linjeopacitet och pixeltjocklek på kartan mellan koordinaterna i URL-parametern. I exemplet nedan har linjen en opacitet på 50 % och en tjocklek på fyra pixlar. Koordinaterna är longitud: -110, latitud: 45 och longitud: -100, latitud: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps polyline](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Efter: Azure Maps**

Lägg till linjer och polygoner i en `path` statisk kartbild genom att ange parametern i URL:en. Precis som Google Maps anger du en formatmall och en lista över platser i den här parametern. Ange `path` parametern flera gånger om du vill återge flera cirklar, linjer och polygoner med olika format.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

När det gäller sökvägsplatser kräver Azure Maps att koordinaterna är i formatet "longitud latitude". Google Maps använder formatet "latitud,longitud". Ett blanksteg, inte ett kommatecken, separerar longitud och latitud i Azure Maps-formatet. Azure Maps stöder inte kodade sökvägar eller adresser för punkter. Ladda upp större datauppsättningar som en GeoJSON-fil till Azure Maps Data Storage API som dokumenteras [här](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Lägg till sökvägsformat med `optionNameValue` formatet. Separera flera format\|efter pipe ( `optionName1Value1|optionName2Value2`) tecken, så här . Alternativnamnen och värdena är inte åtskilda. Använd följande formatalternativnamn för att formatera sökvägar i Azure Maps:

- `fa`- Fyllningsfärgens opacitet (alfa) som används vid rendering av polygoner. Välj ett tal mellan 0 och 1.
- `fc`- Fyllningsfärgen som används för att återge området för en polygon.
- `la`– Linjefärgsopaciteten (alfa) som används vid renderingslinjer och polygonernas kontur. Välj ett tal mellan 0 och 1.
- `lc`– Den linjefärg som används för att återge linjer och konturerna av polygoner.
- `lw`– Bredden på linjen i pixlar.
- `ra`– Anger en cirklarradie i meter.

Lägg till en röd linjeopacitet och pixeltjocklek mellan koordinaterna i URL-parametern. I exemplet nedan har linjen 50 % opacitet och en tjocklek på fyra pixlar. Koordinaterna har följande värden: longitud: -110, latitud: 45 och longitud: -100, latitud: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Polylinje för Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Beräkna en avståndsmatris

Azure Maps tillhandahåller distansmatris-API:et. Använd det här API:et för att beräkna restiderna och avstånden mellan en uppsättning platser, med en avståndsmatris. Det är jämförbart med avståndsmatrisENs API i Google Maps.

- [**Ruttmatris:**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)Beräknar asynkront restider och avstånd för en uppsättning ursprung och destinationer. Stöder upp till 700 celler per begäran. Det är antalet ursprung multiplicerat med antalet destinationer. Med denna begränsning i åtanke, exempel på möjliga matris dimensioner är: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> En begäran till distansmatris API kan endast göras med hjälp av en POST-begäran med ursprungs- och målinformation i brödtexten för begäran. Dessutom kräver Azure Maps att alla ursprung och destinationer ska koordineras. Adresser måste geokodas först.

Den här tabellen korsreferererar Google Maps API-parametrar med jämförbara Azure Maps API-parametrar.

| API-parametern Google Maps      | Jämförbar AZURE Maps API-parameter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`– i post-begärans brödtexten som GeoJSON. |
| `key`                          | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`– i post-begärans brödtexten som GeoJSON.  |
| `region`                       | *Saknas* – Den här funktionen är geokodningsrelaterad. Använd `countrySet` parametern när du använder Azure Maps geokodnings-API: et. |
| `traffic_model`                | *Ej till* - Kan bara ange om trafikdata ska användas med parametern. `traffic` |
| `transit_mode`                 | *Saknas* - Transitbaserade avståndsmatriser stöds för närvarande inte.  |
| `transit_routing_preference`   | *Saknas* - Transitbaserade avståndsmatriser stöds för närvarande inte.  |
| `units`                        | *Saknas* – Azure Maps använder bara måttsystemet. |

> [!TIP]
> Alla avancerade routningsalternativ som är tillgängliga i Azure Maps routning API stöds i Azure Maps avstånd matris API. Avancerade routningsalternativ inkluderar: lastbilsruttning, motorspecifikationer och så vidare.

Granska [metodtipsen för routningsdokumentation.](how-to-use-best-practices-for-routing.md)

## <a name="get-a-time-zone"></a>Skaffa en tidszon

Azure Maps tillhandahåller ett API för att hämta tidszonen för en koordinat. Tidszons-API:et för Azure Maps är jämförbart med tidszons-API:et i Google Maps:

- [**Tidszon efter koordinat**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Ange en koordinat och ta emot tidszonsinformationen för koordinaten.

Den här tabellen korsreferererar Google Maps API-parametrar med jämförbara API-parametrar i Azure Maps.

| API-parametern Google Maps | Jämförbar AZURE Maps API-parameter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Se även [dokumentationen Autentisering med Azure Maps.](azure-maps-authentication.md)       |
| `language`                  | `language`– Se dokumentation [för språk som stöds.](supported-languages.md)    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Utöver det här API:et tillhandahåller Azure Maps ett antal API:er för tidszon. Dessa API:er konverterar tiden baserat på namn eller ID:er för tidszonen:

- [**Tidszon efter ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): Returnerar aktuell, historisk och framtida tidszonsinformation för det angivna IANA-tidszons-ID:et.
- [**Tidszon Enum IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): Returnerar en fullständig lista över IANA-tidszons-ID: er. Uppdateringar av IANA-tjänsten återspeglas i systemet inom en dag.
- [**Tidszon Uppräkning Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Returnerar en fullständig lista över Windows tidszons-ID: er.
- [**Tidszon IANA-version:**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)Returnerar det aktuella IANA-versionsnumret som används av Azure Maps.
- [**Tidszon Windows till IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): Returnerar ett motsvarande IANA-ID, med ett giltigt Windows tidszons-ID. Flera IANA-ID:er kan returneras för ett enda Windows-ID.

## <a name="client-libraries"></a>Klientbibliotek

Azure Maps tillhandahåller klientbibliotek för följande programmeringsspråk:

- JavaScript, TypeScript, Node.js – [dokumentation](how-to-use-services-module.md) \| [NPM-paket](https://www.npmjs.com/package/azure-maps-rest)

Dessa klientbibliotek med öppen källkod är för andra programmeringsspråk:

- .NET Standard 2.0 – [GitHub-projektet](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-paketet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Ytterligare resurser

Följande är ytterligare dokumentation och resurser för Azure Maps REST-tjänsterna.

- [Metodtips för sökning](how-to-use-best-practices-for-search.md)
- [Söka efter en adress](how-to-search-for-address.md)
- [Metodtips för routning](how-to-use-best-practices-for-routing.md)
- [Azure Maps REST Service API-referensdokumentation](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps REST-tjänsterna.

> [!div class="nextstepaction"]
> [Metodtips för att använda söktjänsten](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Metodtips för användning av routningstjänsten](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Så här använder du tjänstemodulen (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)
