---
title: Översikt | Microsoft Azure Maps
description: Lär dig mer om tjänster och funktioner i Microsoft Azure Maps och hur du använder dem i dina program.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fdac12350db785e6194cd3d057f4d2adfefa5969
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335734"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?

Azure Maps är en samling geospatiala tjänster som använder nya mappningsdata för att ge geografisk kontext till webb- och mobilappar. Azure Maps innehåller:

* REST API:er för att återge kartor i flera stilar och i satellitbilder.''
* Sök tjänster för att hitta adresser, platser och intressanta platser runt om i världen.
* Olika routningsalternativ; som punkt-till-punkt, multipoint, multipointoptimering, isochrone, nyttofordon, trafikpåverkand och matrisroutning.
* Trafikflödesvy och incidentvy för program som kräver trafikinformation.
* Mobilitetstjänst för att begära kollektivtrafiklogistik, planera rutter i realtid och begära information för alternativa transportsätt.
* Tidszons- och geolokaliseringstjänster och konvertera en plats till tidszoner.
* Geofencing-tjänst och mappning av datalagring, med platsinformation i Azure. 
* Platsinformation genom geospatial analys. 

Dessutom är Azure Maps-tjänster tillgängliga via Web SDK eller Android SDK. De här verktygen hjälper utvecklare att snabbt utveckla och skala lösningar som integrerar platsinformation i Azure-lösningar. 

Du kan registrera dig för ett kostnadsfritt [Azure Maps-konto](https://azure.microsoft.com/services/azure-maps/) och börja utveckla.

I följande video beskriver vi Azure Maps mer ingående:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kartkontroller

### <a name="web-sdk"></a>Web SDK

Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och dina egna bilder. Du kan använda den här interaktiva kartan för både dina webb- eller mobilappar. Kartkontrollen använder WebGL, så att du kan återge stora datauppsättningar med hög prestanda. Utveckla med SDK med hjälp av JavaScript eller TypeScript.

![Exempel karta över befolkningsförändring](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Använd Azure Maps Android SDK för att skapa mobila mappningsprogram. 

![Mappa exempel på en mobil enhet](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Tjänster i Azure Maps

Azure Maps består av följande nio tjänster som kan ge geografisk kontext till dina Azure-program.

### <a name="data-service"></a>Datatjänst

Data är absolut nödvändigt för kartor. Använd Datatjänsten för att ladda upp och lagra geospatiala data för användning med rumsliga operationer eller bildsammansättning.  Om du för kunddata närmare Azure Maps-tjänsten minskar svarstiden, ökar produktiviteten och skapar nya scenarier i dina program. Mer information om den här tjänsten finns i [datatjänstens API-dokumentation](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Mobilitetstjänsten

Azure Maps Mobility-tjänsten tillåter reseplanering i realtid. Det returnerar bästa möjliga ruttalternativ och ger olika typer av reselägen. För storstadsområden kan dessa lägen omfatta promenader, cykling och kollektivtrafik. Du kan begära transit resväg, linjegeometri, listor över stopp, schemalagda ankomster, ankomster i realtid och servicevarningar.

Tjänsten tillåter också sökningar efter specifika objekttyper runt en plats. Användare kan söka efter delade cyklar, skotrar eller bilar runt en plats. Användare kan begära antalet tillgängliga cyklar i närmaste docka och söka tillgängliga fordon för samåkning. Och användare kan hitta detaljer som framtida tillgänglighet av fordon och nuvarande bränslenivå.

Mer information om tjänsten finns i [dokumentationen till Mobilitets-API.](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Renderingstjänst

Render-tjänsten hjälper utvecklare att skapa webb- och mobilappar med kartfunktioner. Tjänsten använder antingen grafiska rasterbilder av hög kvalitet, tillgängliga i 19 zoomningsnivåer, eller helt anpassningsbara kartbilder i vektorformat.

![Exempel på en karta från tjänsten Render](media/about-azure-maps/intro_map.png)

Nu finns förhandsversions-API:er med återgivningstjänsten, så att utvecklare kan arbeta med satellitbilder. Mer information finns i [dokumentationen till Render API](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Routningstjänst

I Route Service finns robusta geometriberäkningar för verklig infrastruktur och anvisningar om flera transportlägen. Tjänsten gör det möjligt för utvecklare att beräkna riktningar över ett antal reselägen som bil, lastbil, cykel eller promenader. Tjänsten tar också hänsyn till insatsvaror som trafikförhållanden, viktbegränsningar eller transport av farligt material.

![Exempel på en karta från tjänsten Rutt](media/about-azure-maps/intro_route.png)

Route-tjänsten erbjuder en förhandsgranskning av avancerade funktioner, till exempel: 

* Batchbearbetning av flera flödesbegäranden.
* Matriser av restid och avstånd mellan en uppsättning ursprung och destinationer.
* Hitta rutter eller avstånd som användare kan resa baserat på tids- eller bränslekrav. 

Mer information om routningsfunktionerna finns i [dokumentationen för Route API](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Söktjänst

Söktjänsten hjälper utvecklare att söka efter adresser, platser, företagsuppgifter efter namn eller kategori och annan geografisk information. Söktjänsten kan [återföra geokodadresser](https://en.wikipedia.org/wiki/Reverse_geocoding) och korsgator baserat på breddgrader och longituder.

![Exempel på en sökning på en karta](media/about-azure-maps/intro_search.png)

Söktjänsten innehåller även avancerade funktioner som:

* Sök längs en rutt.
* Sök inom ett större område.
* Gruppera en grupp sökbegäranden.
* Sök efter större område i stället för en platspunkt. 

API:er för sökningar inom batch och område finns för närvarande som förhandsversion. Mer information om sökfunktionerna finns i [dokumentationen till sök-API](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Tjänsten Spatial Operations

Azure Maps spatial operations-tjänsten tar platsinformation. Den analyserar snabbt platsinformation för att informera kunderna om pågående händelser som händer i tid och rum. Det möjliggör nära realtidsanalys och prediktiv modellering av händelser. 

Tjänsten gör det möjligt för kunder att förbättra sin platsinformation med ett bibliotek med vanliga geospatiala matematiska beräkningar. Vanliga beräkningar inkluderar närmaste punkt, stort cirkelavstånd och buffertar. Mer information om tjänsten och de olika funktionerna finns i [API-dokumentationen för Spatial Operations](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Tidszontjänst

Med tjänsten Tidszon kan du fråga aktuell, historisk och framtida tidszonsinformation. Du kan använda antingen latitud- och longitudpar eller ett [IANA-ID](https://www.iana.org/). Tidszonstjänsten möjliggör också:

* Konvertera tidszons-ID:er för Microsoft Windows till IANA-tidszoner.
* Hämtar en tidszonsförskjutning till UTC.
* Hämta aktuell tid i en vald tidszon. 

Ett typiskt JSON-svar för en fråga till tidszonstjänsten ser ut som följande exempel:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Mer information om den här tjänsten finns i [api-dokumentationen för tidszon](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Trafiktjänst

Tjänsten Traffic är en uppsättning webbtjänster som utvecklare kan använda för webb- eller mobilappar som kräver trafikinformation. Tjänsten tillhandahåller två typer av data:

* Trafikflöde: Realtid observerade hastigheter och restider för alla viktiga vägar i nätverket.
* Trafikincidenter: En aktuell bild av trafikstockningar och incidenter runt vägnätet.

![Exempel på en karta med trafikinformation](media/about-azure-maps/intro_traffic.png)

Mer information finns i [dokumentationen för Trafik-API](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP till plats-tjänst

Använd tjänsten IP till plats för att förhandsgranska den hämtade landskoden med två bokstäver för en IP-adress. Den här tjänsten kan hjälpa dig att förbättra användarupplevelsen genom att tillhandahålla anpassat programinnehåll baserat på geografisk plats.

Mer information om IP till plats-tjänsten i REST API finns i [Azure Maps Geolocation API-dokumentationen](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programmeringsmodell

Azure Maps är byggt för mobilitet och kan hjälpa dig att utveckla plattformsoberoende program. Den använder en programmeringsmodell som är språkoberoende och stöder JSON-utdata via [REST API:er](https://docs.microsoft.com/rest/api/maps/).

Dessutom erbjuder Azure Maps en bekväm [JavaScript-kartkontroll](https://docs.microsoft.com/javascript/api/azure-maps-control) med en enkel programmeringsmodell. Utvecklingen är snabb och enkel för både webb- och mobilapplikationer.

## <a name="usage"></a>Användning

Att komma åt Azure Maps-tjänsterna är en fråga om att gå till [Azure-portalen](https://portal.azure.com) och skapa ett Azure Maps-konto.

I Azure Maps används ett schema för nyckelbaserad autentisering. Ditt konto levereras med två nycklar som redan genererats för dig, använd båda nycklarna. Börja integrera dessa platsfunktioner i ditt program och gör förfrågningar till Azure Maps-tjänsterna.

Azure Maps delar kundadress-/platsfrågor ("Frågor") med tomtom för mappning av funktioner. Frågor är inte länkade till någon kund eller slutanvändare när de delas med TomTom och kan inte användas för att identifiera individer. Microsoft håller för närvarande på att lägga till TomTom i underleverantörslistan för onlinetjänster. Observera att mobilitets- och vädertjänsterna som inkluderar integration med Moovit och AccuWeather för närvarande finns i [PREVIEW](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Regioner som stöds

Azure Maps API:er är för närvarande tillgängliga i alla länder och regioner utom dessa:

* Kina
* Sydkorea

Kontrollera att platsen för din aktuella IP-adress finns i ett land som stöds.

## <a name="next-steps"></a>Nästa steg

Prova en exempelapp som visar Azure Maps:

> [!div class="nextstepaction"]
> [Snabbstart: Skapa en webbapp](quick-demo-map-app.md)

Håll dig uppdaterad på Azure Maps: 

> [!div class="nextstepaction"]
> [Bloggen Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
