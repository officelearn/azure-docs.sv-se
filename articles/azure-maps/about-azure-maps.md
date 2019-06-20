---
title: Översikt över Azure Maps | Microsoft Docs
description: En introduktion till Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2368d097cc0bfe82d0b869c3c8eb48760cc41616
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275109"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?

Azure Maps är en samling med geospatiala tjänster, underbyggd av nyaste mappningsdata tillgängliga tillhandahålla korrekta geografisk kontext till webb- och mobilprogram. Azure Maps består av REST API: er för rendering **Maps** i flera format och satellit bilder **sökning** för adresser, platser och Orienteringspunkter runt om i världen; **Routning** point-to-point, multipoint, multipoint optimering, isochrone, nyttofordon, trafik som påverkas och matrisen routning; visa branschledande trafikflöde och incidenter **Mobility** tjänster som begär offentlig överföring, bikeshare, scooter resurs och bil dela information för att planera rutter att använda alternativa lägen för transport och realtidsdata; upprätta användarens plats via **Geoplats**; och konvertera plats för att **tidszoner**, samt, hämtar du tid på en plats. Dessutom erbjuder Azure Maps-tjänster för **Geofencing**, karta **Data** storage – som är värd för platsinformation i Azure och **Spatial Operations** att ange plats Intelligence genom geospatial analys. Azure Maps-tjänsterna är tillgängliga direkt som REST API:er eller genom **Web SDK** eller **Android SDK**. Med de här verktygen kan utvecklarna snabbt ta fram och skala lösningar som integrerar platsinformation i dina Azure-lösningar inifrån Azure-molnet. Registrera dig för ditt kostnadsfria [Azure Maps-konto](https://azure.microsoft.com/services/azure-maps/) i dag och sätt igång med utvecklingen!

I följande video beskriver vi Azure Maps mer ingående:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kartkontroller

### <a name="web-sdk"></a>Web SDK

Azure mappar webbtjänst-SDK kan du anpassa interaktiva kartor med innehåll och bilder för visning i dina webb- och mobilprogram. Den här kontrollen använder WebGL, så att du kan rendera stora datauppsättningar med höga prestanda. Utveckla med hjälp av JavaScript- eller TypeScript-SDK.

![Azure Maps webb-SDK](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Azure Maps Android SDK kan du skapa kraftfulla mappning av mobila program. 

![Azure Maps Android SDK](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Tjänster i Azure Maps

Azure Maps består av följande nio tjänster som kan tillhandahålla geografisk kontext till dina Azure-program.

### <a name="data-service"></a>Data Service

Data är avgörande för maps och föra närmare kunddata till Azure Maps-tjänsten ska minska svarstiden, öka produktiviteten och skapa kraftfulla, nya scenarier för ljus upp i dina program. Data Service kan du överföra och lagra geospatiala data för använda med spatial drift- eller image-sammansättning för att minska svarstiden, öka produktiviteten och möjliggör nya scenarier i dina program. Mer information om den här tjänsten på den [Data Service API: er](https://docs.microsoft.com/rest/api/maps/data) sidan.

### <a name="mobility-service"></a>Mobilitetstjänsten

Azure Maps mobilitetstjänster ge i realtid location intelligence på Närliggande offentliga överföring tjänster, inklusive stoppas, flödesinformationen och reser mycket tid uppskattningar. Tjänsten gör för att söka efter specifika objekttyper som offentliga överföringen stoppas, delade cyklar/skotrar/bilar runt en viss plats som returnerar en uppsättning överföring objekt med information om objekt. Tjänsten kan utvecklare också begäran överföring detaljer som täcker grundläggande information och ytterligare information, till exempel rad geometri, lista över stoppas, schemalagda och i realtid överföring anslutningsbegäranden och aviseringar. Användarna kan också begära hur många tillgängliga delade cyklar som är kvar i närmaste docka genom att begära dockningsplaceringen stationer information. Mobilitetstjänsten har också möjligheten att söka efter tillgängliga bil resurs fordon returnerar information om till exempel vara tillgängliga och aktuella bränsle nivån.
Azure Maps-mobilitetstjänsten kan i realtid resans planering, returnerar de bästa möjliga väg alternativen och tillhandahåller ett antal reslägen, inklusive gång, att- och offentliga överföring som är tillgängliga i området metro (ort). Utvecklare kan dessutom begära överföring resväg information med ytterligare information sådana geometri för flödet och detaljerad resväg scheman.

Läs mer om tjänsten och de olika funktionerna i vår [API-dokumentation](https://docs.microsoft.com/rest/api/maps/mobility)

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

### <a name="spatial-operations"></a>Spatial Operations

Azure Maps-Spatial åtgärder tar platsinformation och analysera det direkt för att informera våra kunder pågående händelser som händer i tid och utrymme, aktiverar nästan i realtid analys och förutsägande modellering av händelser. Tjänsten gör att Azure Maps kunderna internt att förbättra sin plats intelligens med ett bibliotek med vanliga geospatiala matematiska beräkningar, bland annat tjänster som närmaste punkt, cirkel bra avståndet och buffertar. Läs mer om tjänsten och de olika funktionerna i vår [API-dokumentation](https://docs.microsoft.com/rest/api/maps/spatial).

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
* Trafik incidenter - en uppdaterad bild om trafikstockningar och olyckor i vägnätet.

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

Azure Maps-API är nu tillgänglig i alla länder/regioner förutom följande regioner:

* Argentina
* Kina
* Indien
* Marocko
* Pakistan
* Sydkorea

Kontrollera att platsen för din aktuella IP-adress som inte är i något av de som inte stödda länder/regionerna ovan.

## <a name="next-steps"></a>Nästa steg

Prova att använda en exempelapp med Azure Maps:

> [!div class="nextstepaction"]
> [Snabbstart: Skapa en webbapp](quick-demo-map-app.md)

Håll dig uppdaterad på Azure Maps: 

> [!div class="nextstepaction"]
> [Azure Maps-bloggen](https://azure.microsoft.com/blog/topics/azure-maps/)