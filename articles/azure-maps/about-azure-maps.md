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
ms.openlocfilehash: dbcb0e87159efc7cc33101cdd5eee55afbdf3c10
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516643"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?

Azure Maps är en samling geospatiala tjänster som använder nya mappnings data för att tillhandahålla korrekt geografisk kontext till webb-och mobil program. Azure Maps tillhandahåller:

* REST API: er för åter givning av kartor i flera format och i satellit-bilder.
* Söker efter adresser, platser och punkter av intresse runtom i världen.
* Routning punkt-till-plats, Multipoint, Multipoint-optimering, isochrone, nytto fordon, trafik påverkan och mat ris routning; Visa trafik flöde och incidenter.
* Mobilitets tjänster för att begära offentlig transitering och alternativa transport sätt (t. ex. cykel resurs, Scooter resurs och bil delning) och planera vägar i real tid. 
* Upprätta en användar plats genom geolokalisering och konvertera plats till tids zoner. 
* Tjänster för avgränsning och kart data lagring med plats information som finns i Azure. 
* Plats information via Geospatial analys. 

Förutom REST-API: er är Azure Maps Services tillgängliga via webb-SDK eller Android SDK. De här verktygen hjälper utvecklare att snabbt utveckla och skala lösningar som integrerar plats information i Azure-lösningar. 

Du kan registrera dig för ett kostnads fritt [Azure Maps-konto](https://azure.microsoft.com/services/azure-maps/) och börja utveckla.

I följande video beskriver vi Azure Maps mer ingående:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kart kontroller

### <a name="web-sdk"></a>Web SDK

Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och bilder för visning i dina webb-eller mobil program. Den här kontrollen använder WebGL, så du kan återge stora data mängder med hög prestanda. Utveckla med SDK genom att använda Java Script eller TypeScript.

![Exempel karta över populations ändringar](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Använd Azure Maps Android SDK för att skapa appar för mobil mappning. 

![Kart exempel på en mobil enhet](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Tjänster i Azure Maps

Azure Maps består av följande nio tjänster som kan tillhandahålla geografisk kontext till dina Azure-program.

### <a name="data-service"></a>Data Service

Data är ett absolut krav för Maps. Använd data service för att ladda upp och lagra geospatiala data för användning med spatiala åtgärder eller bild sammansättning.  Att ta med kund information närmare Azure Maps tjänsten minskar svars tiden, ökar produktiviteten och skapar nya scenarier i dina program. Mer information om den här tjänsten finns i [dokumentationen för data service API](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Mobilitetstjänsten

Med tjänsten Azure Maps mobilitet kan du planera real tids resor. Den returnerar bästa möjliga väg alternativ och ger en rad olika rese lägen. För Metro-områden kan de här lägena omfatta promenad, cykla och offentlig överföring. Utvecklare kan begära transit färdväg, t. ex. linje geometri, listor över stopp, schemalagda och real tids införslar samt tjänst aviseringar.

Tjänsten tillåter också sökningar efter vissa objekt typer, till exempel delade cyklar, scooters eller bilar runt en plats. Användare kan begära hur många tillgängliga delade cyklar som finns kvar i närmaste Docker. De kan söka efter tillgängliga bil aktie bilar och hitta information som framtida tillgänglighet och aktuell bränsle nivå.

Mer information om tjänsten finns i [dokumentationen för Mobility API](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Renderingstjänst

Åter givnings tjänsten hjälper utvecklare att skapa webb-och mobil program runt mappning. Tjänsten använder antingen grafiska rasterbilder av hög kvalitet, tillgängliga i 19 zoomningsnivåer, eller helt anpassningsbara kartbilder i vektorformat.

![Exempel på en karta från åter givnings tjänsten](media/about-azure-maps/Introduction_Map.png)

Nu finns förhandsversions-API:er med återgivningstjänsten, så att utvecklare kan arbeta med satellitbilder. Mer information finns i rendera [API-dokumentationen](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Routningstjänst

I Route Service finns robusta geometriberäkningar för verklig infrastruktur och anvisningar om flera transportlägen. Tjänsten gör det möjligt för utvecklare att beräkna riktningar efter ett antal färdmedel, till exempel bil, lastbil, cykel eller gående. Tjänsten kan också inkludera indata som trafikförhållanden, viktbegränsningar eller transport av farligt material.

![Exempel på en karta från Route service](media/about-azure-maps/Introduction_Route.png)

Route service erbjuder en för hands version av avancerade funktioner, till exempel: 

* Batchbearbetning av flera väg begär Anden.
* Matriser med res tid och avstånd mellan en uppsättning ursprung och mål.
* Hitta vägar eller avstånd som användarna kan färdas utifrån tid-eller bränsle krav. 

Mer information om routningsfunktioner finns i [Route API-dokumentationen](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Söktjänst

Sök tjänsten hjälper utvecklare att söka efter adresser, platser, företags listor efter namn eller kategori och annan geografisk information. Sök tjänsten kan omvända hår [kod](https://en.wikipedia.org/wiki/Reverse_geocoding) adresser och korsade gator utifrån latitud och longitud.

![Exempel på en sökning på en karta](media/about-azure-maps/Introduction_Search.png)

Sök tjänsten innehåller även avancerade funktioner som:

* Sök längs en väg.
* Sök i ett större område.
* Gruppera en grupp med Sök begär Anden.
* Sök efter större områden i stället för en plats punkt. 

API:er för sökningar inom batch och område finns för närvarande som förhandsversion. Mer information om Sök funktionerna finns i [Search API-dokumentationen](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Tjänst för spatial verksamhet

Tjänsten Azure Maps spatial drift tar plats information och analyserar den i farten och hjälper till att informera kunder om pågående händelser i tid och utrymme. Den möjliggör analys i nära real tid och förutsägande modellering av händelser. 

Tjänsten gör det möjligt för kunderna att förbättra sin plats information med ett bibliotek med vanliga geospatiala matematiska beräkningar, inklusive närmaste punkt, fantastiska cirkel avstånd och buffertar. Läs mer om tjänsten och de olika funktionerna i API-dokumentationen för [spatiala åtgärder](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Tidszontjänst

Med tids zons tjänsten kan du fråga aktuell, historisk och framtida tids zons information med hjälp av antingen latitud-/longitud-par eller ett [IANA-ID](https://www.iana.org/). Tids zons tjänsten tillåter också:

* Konvertera Microsoft Windows Time-Zone-ID till IANA-tidszoner.
* Hämtar en tids zons förskjutning till UTC.
* Hämtar den aktuella tiden i en tidszon. 

Ett typiskt JSON-svar för en fråga till tids zons tjänsten ser ut som i följande exempel:

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

Mer information om den här tjänsten finns i [API-dokumentationen för Time Zone](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Trafiktjänst

Traffic service är en uppsättning webb tjänster som utvecklare kan använda för att skapa webb-och mobil program som kräver trafik information. Tjänsten tillhandahåller två typer av data:

* Trafikflöde: Real tids hastighet och res tider för alla nyckel vägar i nätverket.
* Trafik incidenter: En uppdaterad vy över trafiken sylt och incidenter runt vägtrafik nätverket.

![Exempel på en karta med trafik information](media/about-azure-maps/Introduction_Traffic.png)

Mer information finns i dokumentationen om [Traffic API](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Tjänst-till-plats-tjänst

Använd tjänsten IP to Location för att förhandsgranska den hämtade lands koden med två bokstäver för en IP-adress. Den här tjänsten kan hjälpa dig att skräddarsy och förbättra användar upplevelsen genom att tillhandahålla anpassat program innehåll baserat på geografisk plats.

Mer information om REST-API: er för tjänsten IP to Location finns i [Azure Maps API-dokumentation för geolokalisering](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programmeringsmodell

Azure Maps har skapats för mobilitet och kan hjälpa dig att utveckla plattforms oberoende program. Den använder en programmerings modell som är språk oberoende och har stöd för JSON-utdata via [REST-API: er](https://docs.microsoft.com/rest/api/maps/).

Azure Maps har dessutom en praktisk [JavaScript-kartkontroll ](https://docs.microsoft.com/javascript/api/azure-maps-control) med en enkel programmeringsmodell för snabb och enkel utveckling av både webb- och mobilprogram.

## <a name="usage"></a>Användning

Att komma åt Azure Maps-tjänsterna är en fråga om att gå till [Azure Portal](https://portal.azure.com) och skapa ett Azure Maps-konto.

I Azure Maps används ett schema för nyckelbaserad autentisering. Ditt konto har två nycklar som redan har skapats för dig. Börja integrera de här platsfunktioner i ditt program genom att använda någon av dina nycklar och göra en begäran till Azure Maps-tjänsten.

## <a name="supported-regions"></a>Regioner som stöds

Azure Maps-API: er är för närvarande tillgängliga i alla länder/regioner förutom följande:

* Kina
* Sydkorea

Kontrollera att platsen för din aktuella IP-adress inte är i någon av de länder som nämns ovan.

## <a name="next-steps"></a>Nästa steg

Prova en exempel app som demonstrerar Azure Maps:

> [!div class="nextstepaction"]
> [Snabbstart: Skapa en webbapp](quick-demo-map-app.md)

Håll dig uppdaterad om Azure Maps: 

> [!div class="nextstepaction"]
> [Azure Maps blogg](https://azure.microsoft.com/blog/topics/azure-maps/)
