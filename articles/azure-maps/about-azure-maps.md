---
title: Översikt över Azure Maps | Microsoft Docs
description: En introduktion till Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 07/12/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 93fe8dc3f8ff991cd6c48923d9e2073e4e93f1ad
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040855"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?
Azure Maps är en samling geospatiala tjänster som backas upp av nya mappningsdata. Du kan ange exakt geografisk kontext till webb- och mobilprogram. Den innehåller REST API:er för att rendera kartor, söka efter intressanta platser, trafikförhållanden, tidszoner och IP till plats-tjänster. Med de här API:erna och välbekanta verktyg kan du snabbt utveckla och skala lösningar som integrerar platsinformation i dina Azure-lösningar. Tillsammans med REST API:erna får du en webbaserad JavaScript-kontroll som gör utveckling över flera medier enkel, flexibel och portabel. 

I följande video beskriver vi Azure Maps mer ingående:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

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

Med tidszontjänsten kan du skicka begäranden om aktuell, historisk och framtida tidszonsinformation med hjälp av antingen latitud- och longitudpar eller [IANA ID](http://www.iana.org/). Time Zone Service kan även användas för att konvertera Microsoft Windows tidszon-ID till IANA-tidszoner, hämta en tidszonsförskjutning mot UTC och hämta den aktuella tiden i respektive tidszon. Ett typiskt JSON-svar på en fråga till Time Zone Service ser ut så här:

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

Tjänsten för IP till plats finns för närvarande som förhandsversion. Med den kan du hämta landskoden med två bokstäver för en angiven IP-adress. Med den här tjänsten kan du anpassa programmet och uppfylla särskilda geopolitiska begränsningar samt ändra innehållet baserat på den geografiska platsen för en bättre användarupplevelse. 


## <a name="programming-model"></a>Programmeringsmodell

Azure Maps är byggt för flexibilitet och fungerar på olika plattformar. Programmeringsmodellen är språkoberoende och har stöd för JSON-utdata via [REST API:er](https://docs.microsoft.com/rest/api/maps/). 

Azure Maps har dessutom en praktisk [JavaScript-kartkontroll ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/?view=azure-iot-typescript-latest) med en enkel programmeringsmodell för snabb och enkel utveckling av både webb- och mobilprogram. 


## <a name="usage"></a>Användning

Du kommer åt karttjänsterna via [Azure-portalen](http://portal.azure.com) där du kan skapa ett Azure Maps-konto. 

I Azure Maps används ett schema för nyckelbaserad autentisering. Ditt konto har två nycklar som har genererats åt dig. Börja integrera de här platsfunktioner direkt i dina program med någon av dina nycklar i begärandena till Azure Maps-tjänsten.

## <a name="supported-regions"></a>Regioner som stöds
Azure Maps-API:t är nu tillgängligt i alla länder och regioner utom följande: 

* Argentina
* Kina
* Indien
* Marocko
* Pakistan
* Sydkorea

Kontrollera din aktuella IP-adress och dess plats, så att den inte är kopplad till någon av de länder som nämns ovan.

## <a name="next-steps"></a>Nästa steg

- Mer information om de nya funktionerna i Azure Maps: 
    - [Ruttmatris, isokroner, hitta IP-adress med mera](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/). 
- Gå vidare och testa en provapp som visar tjänsten
    - [Starta en demokarta för interaktiv sökning](quick-demo-map-app.md)
