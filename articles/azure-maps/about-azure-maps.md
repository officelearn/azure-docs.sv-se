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
ms.openlocfilehash: 5f41263113568cf9f3771119135be8db37119181
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442948"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?

Azure Maps är en samling med geospatiala tjänster med nya mappningsdata som används för att tillhandahålla korrekta geografisk kontext till webb- och mobilprogram. Azure Maps ger:

* REST API: er för att återge maps i flera format och i satellit bilder.
* Söker efter adresser, platser och Orienteringspunkter runt om i världen.
* Routning point-to-point, multipoint, multipoint optimering, isochrone, nyttofordon, trafik som påverkas och matrisen Routning; Visa flödet i nätverkstrafiken och incidenter.
* Mobilitetstjänster för att begära offentliga överföring och alternativa lägen för transport (till exempel bikeshare, scooter filresursen och bil resursen) och planera vägar i realtid. 
* Upprätta användarens plats via geoplats och konvertera plats till tidszoner. 
* Tjänster för geofencing och karta datalagring med platsinformation i Azure. 
* Location intelligence genom geospatial analys. 

Förutom REST API: er är Azure Maps-tjänsterna tillgängliga via Web SDK- eller Android SDK. Dessa verktyg hjälpa utvecklare att snabbt utveckla och skala lösningar där platsinformation integreras i Azure-lösningar. 

Du kan registrera dig för en kostnadsfri [Azure Maps-konto](https://azure.microsoft.com/services/azure-maps/) och börja utveckla.

I följande video beskriver vi Azure Maps mer ingående:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kartkontroller

### <a name="web-sdk"></a>Web SDK

Azure mappar webbtjänst-SDK kan du anpassa interaktiva kartor med innehåll och bilder för visning i dina webb- och mobilprogram. Den här kontrollen gör användning av WebGL, så att du kan rendera stora datauppsättningar med höga prestanda. Utveckla med SDK med hjälp av JavaScript- eller TypeScript.

![Ändra exempelkarta från population](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Använd Azure Maps Android SDK för att skapa mappning av mobila program. 

![Mappa exempel på en mobil enhet](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Tjänster i Azure Maps

Azure Maps består av följande nio tjänster som kan tillhandahålla geografisk kontext till dina Azure-program.

### <a name="data-service"></a>Data Service

Data är avgörande för maps. Använd Data Service för att överföra och lagra geospatiala data för användning med spatial drift- eller image-sammansättning.  Föra närmare kunddata till Azure Maps-tjänsten ska minska svarstiden, öka produktiviteten och skapa nya scenarier i dina program. Mer information om den här tjänsten finns i [Data Service API-dokumentation](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Mobilitetstjänsten

Azure Maps mobilitetstjänsten kan planera i realtid resa. Den returnerar de bästa möjliga väg alternativen och erbjuder en mängd olika reslägen. För appar i metro (ort) områden, kan dessa lägen innehålla walking cykling och offentliga överföring. Utvecklare kan begära överföring resväg detaljer som rad geometri, en lista över stoppas, schemalagda och anslutningsbegäranden i realtid och aviseringar.

Tjänsten kan också sökningar för specifika objekttyper, till exempel delade cyklar, skotrar eller bilar runt en plats. Användarna kan begära hur många tillgängliga delade cyklar lämnas i närmaste dockan. De kan söka efter tillgängliga bil-share fordon och hitta information som framtida tillgänglighet och den aktuella bränsle nivån.

Mer information om tjänsten finns i [Mobility API-dokumentation](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Renderingstjänst

Rendering-tjänsten hjälper utvecklare att skapa webb- och mobilprogram runt mappning. Tjänsten använder antingen grafiska rasterbilder av hög kvalitet, tillgängliga i 19 zoomningsnivåer, eller helt anpassningsbara kartbilder i vektorformat.

![Exempel på en karta från rendering-tjänsten](media/about-azure-maps/Introduction_Map.png)

Nu finns förhandsversions-API:er med återgivningstjänsten, så att utvecklare kan arbeta med satellitbilder. Mer information finns i [dokumentation om API: er](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Routningstjänst

I Route Service finns robusta geometriberäkningar för verklig infrastruktur och anvisningar om flera transportlägen. Tjänsten gör det möjligt för utvecklare att beräkna riktningar efter ett antal färdmedel, till exempel bil, lastbil, cykel eller gående. Tjänsten kan också inkludera indata som trafikförhållanden, viktbegränsningar eller transport av farligt material.

![Exempel på en karta från Route service](media/about-azure-maps/Introduction_Route.png)

Route service erbjuder en förhandsversion av avancerade funktioner, till exempel: 

* Satsvis bearbetning av flera begäranden.
* Matriser av färdas tid och avståndet mellan en uppsättning ursprung och mål.
* Vägar för att hitta eller avstånd som användare kan färdas baserat på tid eller bränsle. 

Mer information om funktioner för routning finns i [väg API-dokumentation](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Söktjänst

Söktjänsten hjälper utvecklare att söka efter adresser, platser, Företagslistor efter namn eller kategori, och annan geografisk information. Söktjänsten kan [omvänt geokoda](https://en.wikipedia.org/wiki/Reverse_geocoding) adresser och gatukorsningar baserat på Latitude och longitudes.

![Exempel på en sökning på en karta](media/about-azure-maps/Introduction_Search.png)

Söktjänsten tillhandahåller även avancerade funktioner som:

* Sök längs en väg.
* Sök i ett större område.
* Batch-en grupp med sökförfrågningar.
* Sök efter större område i stället för en plats-plats. 

API:er för sökningar inom batch och område finns för närvarande som förhandsversion. Mer information om sökfunktionerna kan läsa den [Search API-dokumentation](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Spatial Operations-tjänsten

Azure Maps Spatial Operations-tjänsten tar platsinformation och analyserar direkt för att informera kunder om pågående händelser som händer i tid och utrymme. Det gör att nästan i realtid analys och förutsägande modellering av händelser. 

Tjänsten gör det möjligt för kunder att förbättra sin plats intelligens med ett bibliotek med vanliga geospatiala matematiska beräkningar, inklusive närmaste punkt, cirkel bra avståndet och buffertar. Mer information om tjänsten och de olika funktionerna finns i [Spatial Operations-API-dokumentation](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Tidszontjänst

Time Zone service kan du fråga aktuell, historisk och framtida tidszonsinformation med hjälp av antingen latitud/longitud par eller en [IANA-ID](https://www.iana.org/). Time Zone service kan även användas för:

* Konvertera Microsoft Windows tidszon ID till IANA-tidszoner.
* Hämta en tidszonsförskjutning till UTC.
* Hämta den aktuella tiden i en annan tidszon. 

Ett typiskt JSON-svar för en fråga till Time Zone service ser ut som i följande exempel:

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

Mer information om den här tjänsten finns i [tidszon API-dokumentation](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Trafiktjänst

Traffic service är en uppsättning webbtjänster som utvecklare kan använda för att skapa webbprogram och mobilappar som kräver information om trafik. Tjänsten tillhandahåller två typer av data:

* Flödet i nätverkstrafiken: I realtid observerade hastigheter och resor tider för alla viktiga vägar i nätverket.
* Trafik incidenter: En uppdaterad vy över trafikstockningar och olyckor i vägnätet.

![Exempel på en karta med trafikinformation](media/about-azure-maps/Introduction_Traffic.png)

Mer information finns i den [trafik API-dokumentation](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP-adress till platstjänsten

Använd IP-adress till platstjänsten om du vill förhandsgranska hämtade två bokstäver landskoden för en IP-adress. Den här tjänsten kan hjälpa dig att skräddarsy och förbättra användarupplevelsen genom att tillhandahålla anpassade program innehåll baserat på geografisk plats.

Mer information om REST-API: er för IP-adress till platstjänsten läsa den [Azure Maps Geoplats API-dokumentation](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programmeringsmodell

Azure Maps har skapats för rörlighet och hjälper dig att utveckla program för flera plattformar. Den använder en programmeringsmodell som är språkoberoende och har stöd för JSON-utdata via [REST API: er](https://docs.microsoft.com/rest/api/maps/).

Azure Maps har dessutom en praktisk [JavaScript-kartkontroll ](https://docs.microsoft.com/javascript/api/azure-maps-control) med en enkel programmeringsmodell för snabb och enkel utveckling av både webb- och mobilprogram.

## <a name="usage"></a>Användning

Åtkomst till Azure Maps-tjänsterna är en fråga för att övergå till den [Azure-portalen](https://portal.azure.com) och skapa ett Azure Maps-konto.

I Azure Maps används ett schema för nyckelbaserad autentisering. Ditt konto har två nycklar som redan har skapats för dig. Börja integrera de här platsfunktioner i ditt program genom att använda någon av dina nycklar och göra en begäran till Azure Maps-tjänsten.

## <a name="supported-regions"></a>Regioner som stöds

Azure Maps-API: er är tillgängliga i alla länder/regioner Förutom dessa:

* Argentina
* Kina
* Indien
* Marocko
* Pakistan
* Sydkorea

Kontrollera att platsen för din aktuella IP-adress som inte är i något av de som inte stödda länder/regionerna.

## <a name="next-steps"></a>Nästa steg

Pröva en exempelapp som visar Azure Maps:

> [!div class="nextstepaction"]
> [Snabbstart: Skapa en webbapp](quick-demo-map-app.md)

Håll dig uppdaterad på Azure Maps: 

> [!div class="nextstepaction"]
> [Azure Maps-bloggen](https://azure.microsoft.com/blog/topics/azure-maps/)
