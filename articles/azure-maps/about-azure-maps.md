---
title: Översikt över Azure Maps | Microsoft Docs
description: En introduktion till Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 09/12/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9bf168a0f06d662526d172687e54327b55d50bd8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104275"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?

Azure Maps är en samling geospatiala tjänster som backas upp av nya mappningsdata. Du kan ange exakt geografisk kontext till webb- och mobilprogram. Den innehåller REST API:er för rendering av kartor och söka efter platser av intresse. API:erna kan också hitta vägar till platser av intresse, trafikförhållanden, tidszoner och en plats från en IP-adress. API:erna använder välbekanta verktyg för att snabbt utveckla och skala lösningar som integrerar platsinformation i dina Azure-lösningar. Utöver dess REST API:er får du en webbaserad [JavaScript-kartkontroll](https://docs.microsoft.com/javascript/api/azure-maps-control) som gör utveckling enkel, flexibel och portabel över flera medier.

I följande video beskriver vi Azure Maps mer ingående:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kartkontroller

### <a name="web-control"></a>Webbkontroll

Med webbkontrollen för Azure Maps kan du anpassa interaktiva kartor med eget innehåll och bilder för visning i dina webb- och mobilprogram. Den här kontrollen använder WebGL, så att du kan rendera stora datauppsättningar med höga prestanda. Utveckla med kontrollen med JavaScript eller TypeScript.

![Webbkontroll för Azure Maps](media/about-azure-maps/Introduction_WebMapControl.png)

## <a name="services-in-azure-maps"></a>Tjänster i Azure Maps

Azure Maps består av följande sex tjänster som kan tillhandahålla geografisk kontext till dina Azure-program.

### <a name="render-service"></a>Renderingstjänst

Render Service är utformad för utvecklare som skapar webb- och mobilprogram runt mappning. Tjänsten använder antingen grafiska rasterbilder av hög kvalitet, tillgängliga i 19 zoomningsnivåer, eller helt anpassningsbara kartbilder i vektorformat.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Nu finns förhandsversions-API:er med återgivningstjänsten, så att utvecklare kan arbeta med satellitbilder. Mer information finns i dokumentationen om [Azure Maps-renderings-API:er](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Routningstjänst

I Route Service finns robusta geometriberäkningar för verklig infrastruktur och anvisningar om flera transportlägen. Tjänsten gör det möjligt för utvecklare att beräkna riktningar efter ett antal färdmedel, till exempel bil, lastbil, cykel eller gående. Tjänsten kan också inkludera indata som trafikförhållanden, viktbegränsningar eller transport av farligt material.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Ruttjänsten har nu förhandsversioner av avancerade funktioner som bearbetning av flera ruttbegäranden samtidigt, matriser med restid och avstånd mellan ursprung och mål, samt identifiering av rutter och avstånd så att du kan resa baserat på tid eller bränslekrav. Mer information om ruttfunktioner finns i dokumentationen om [rutt-API:er i Azure Maps](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Söktjänst

Söktjänsten är utformad för utvecklare som behöver söka efter adresser, platser, företag efter namn och kategori och annan geografisk information. Söktjänsten kan också [omvänt geokoda](https://en.wikipedia.org/wiki/Reverse_geocoding) adresser och gatukorsningar baserat på latitud/longitud.

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

I söktjänsten finns dessutom avancerade funktioner som att söka längs en rutt, söka i ett större område, bearbeta flera sökbegäranden samtidigt och söka efter ett större område i stället för en viss punkt. API:er för sökningar inom batch och område finns för närvarande som förhandsversion. Mer information om sökfunktionerna finns på sidan med dokumentation om [Sök-API:er för Azure Maps](https://docs.microsoft.com/rest/api/maps/search).

### <a name="time-zone-service"></a>Tidszontjänst

Med tidszontjänsten kan du skicka begäranden om aktuell, historisk och framtida tidszonsinformation med hjälp av antingen latitud- och longitudpar eller [IANA ID](https://www.iana.org/). Time Zone Service kan även användas för att konvertera Microsoft Windows tidszon-ID till IANA-tidszoner, hämta en tidszonsförskjutning mot UTC och hämta den aktuella tiden i respektive tidszon. Ett typiskt JSON-svar på en fråga till Time Zone Service ser ut så här:

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

Mer information om den här tjänsten finns på sidan med dokumentation om [tidszons-API:er i Azure Maps](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Trafiktjänst

Trafiktjänsterna är en svit med webbtjänster som utformats för utvecklare som skapar webb- och mobilprogram som kräver trafik. Tjänsten tillhandahåller två typer av data:

* Traffic flow – realtidsinformation om observerade hastigheter och resetider för alla viktiga vägar i nätverket.
* Traffic incidents – en precis bild över trafikstockningar och olyckor i vägnätet.

![Azure Maps-trafik](media/about-azure-maps/Introduction_Traffic.png)

Du hittar mer information på sidan med dokumentation om [Trafik-API:er i Azure Maps](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location"></a>IP till plats

Tjänsten för IP till plats låter dig förhandsvisa den hämtade landskoden med två bokstäver för en angiven IP-adress. Den här tjänsten kan hjälpa dig att skräddarsy och förbättra användarupplevelsen genom att möjliggöra anpassat programinnehåll baserat på geografisk plats.

Information om REST-API:er för IP till plats-tjänsten finns på sidan om [Azure Maps Geolocation-API:er](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programmeringsmodell

Azure Maps är byggt för flexibilitet och fungerar på olika plattformar. Programmeringsmodellen är språkoberoende och har stöd för JSON-utdata via [REST API:er](https://docs.microsoft.com/rest/api/maps/).

Azure Maps har dessutom en praktisk [JavaScript-kartkontroll ](https://docs.microsoft.com/javascript/api/azure-maps-control) med en enkel programmeringsmodell för snabb och enkel utveckling av både webb- och mobilprogram.

## <a name="usage"></a>Användning

Du kommer åt karttjänsterna via [Azure-portalen](https://portal.azure.com) där du kan skapa ett Azure Maps-konto.

I Azure Maps används ett schema för nyckelbaserad autentisering. Ditt konto har två nycklar som har genererats åt dig. Börja integrera de här platsfunktioner i ditt program genom att använda någon av dina nycklar och göra en begäran till Azure Maps-tjänsten.

## <a name="supported-regions"></a>Regioner som stöds

Azure Maps-API:t är nu tillgängligt i alla länder och regioner utom följande:

* Argentina
* Kina
* Indien
* Marocko
* Pakistan
* Sydkorea

Kontrollera att platsen för din aktuella IP-adress inte är i någon av de länder som nämns ovan.

## <a name="next-steps"></a>Nästa steg

Mer information om de nya funktionerna i Azure Maps:

> [!div class="nextstepaction"]
> [Ruttmatris, isokroner, IP-sökning med mera](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

Gå vidare och testa en exempelapp som visar tjänsten:

> [!div class="nextstepaction"]
> [Starta en demokarta för interaktiv sökning](quick-demo-map-app.md)
