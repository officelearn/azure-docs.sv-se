---
title: Översikt | Microsoft Azure Maps
description: Lär dig mer om tjänster och funktioner i Microsoft Azure Maps och hur du använder dem i dina program.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b375ea8fdebd876bdc7ecf157a4cebd5e614dfd0
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443757"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?

Azure Maps är en samling geospatiala tjänster som använder nya mappnings data för att tillhandahålla geografisk kontext till webb-och mobil program. Azure Maps tillhandahåller:

* REST API: er för rendering av kartor i flera format och i satellit-bilder.
* Sök efter tjänster för att hitta adresser, platser och intressanta platser i världen.
* Olika alternativ för routning; till exempel punkt-till-punkt, Multipoint, Multipoint-optimering, isochrone, nytto fordon, trafik påverkan och mat ris routning.
* Vyn trafikflödes visning och incidenter för program som kräver trafik information.
* Mobilitets tjänsten för att begära logistik för offentlig överföring, planera vägar i real tid och begära information för alternativa transport metoder.
* Tidszon och tjänster för geolokalisering och konvertera en plats till tids zoner.
* Att serva och mappa data lagring med plats information som finns i Azure. 
* Plats information via Geospatial analys. 

Dessutom är Azure Maps Services tillgängliga via Web SDK eller Android SDK. De här verktygen hjälper utvecklare att snabbt utveckla och skala lösningar som integrerar plats information i Azure-lösningar. 

Du kan registrera dig för ett kostnads fritt [Azure Maps-konto](https://azure.microsoft.com/services/azure-maps/) och börja utveckla.

I följande video beskriver vi Azure Maps mer ingående:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kart kontroller

### <a name="web-sdk"></a>Web SDK

Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och bilder. Du kan använda den här interaktiva kartan för både dina webb program eller mobila program. Kart kontrollen använder WebGL, vilket innebär att du kan återge stora data mängder med hög prestanda. Utveckla med SDK genom att använda Java Script eller TypeScript.

![Exempel karta över populations ändringar](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Använd Azure Maps Android SDK för att skapa appar för mobil mappning. 

![Kart exempel på en mobil enhet](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Tjänster i Azure Maps

Azure Maps består av följande nio tjänster som kan tillhandahålla geografisk kontext till dina Azure-program.

### <a name="data-service"></a>Datatjänst

Data är absolut nödvändigt för Maps. Använd data service för att ladda upp och lagra geospatiala data för användning med spatiala åtgärder eller bild sammansättning.  Att ta med kund information närmare Azure Maps tjänsten minskar svars tiden, ökar produktiviteten och skapar nya scenarier i dina program. Mer information om den här tjänsten finns i [dokumentationen för data service API](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Mobilitetstjänsten

Med tjänsten Azure Maps mobilitet kan du planera real tids resor. Den returnerar bästa möjliga väg alternativ och ger olika typer av rese lägen. För Metro-områden kan de här lägena omfatta promenad, cykla och offentlig överföring. Du kan begära transit färdväg, rad geometri, listor över stopp, schemalagda införslar, real tids införslar och tjänst aviseringar.

Tjänsten tillåter också sökning efter vissa objekt typer runt en plats. Användare kan söka efter delade cyklar, scooters eller bilar runt en plats. Användare kan begära antalet tillgängliga cyklar i närmaste Docker och söka efter delning av de tillgängliga fordonen. Dessutom kan användarna hitta information som framtida tillgänglighet för fordon och aktuell bränsle nivå.

Mer information om tjänsten finns i [dokumentationen för Mobility API](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Renderingstjänst

Åter givnings tjänsten hjälper utvecklare att skapa webb-och mobil program med kart funktioner. Tjänsten använder antingen grafiska rasterbilder av hög kvalitet, tillgängliga i 19 zoomningsnivåer, eller helt anpassningsbara kartbilder i vektorformat.

![Exempel på en karta från åter givnings tjänsten](media/about-azure-maps/intro_map.png)

Nu finns förhandsversions-API:er med återgivningstjänsten, så att utvecklare kan arbeta med satellitbilder. Mer information finns i [rendera API-dokumentationen](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Routningstjänst

I Route Service finns robusta geometriberäkningar för verklig infrastruktur och anvisningar om flera transportlägen. Tjänsten gör det möjligt för utvecklare att beräkna vägvisningar i ett antal rese lägen, till exempel bil, Last bil, cykel eller promenad. Tjänsten tar också hänsyn till inmatningar som trafik villkor, vikt begränsningar eller transport av farligt material.

![Exempel på en karta från Route service](media/about-azure-maps/intro_route.png)

Route service erbjuder en för hands version av avancerade funktioner, till exempel: 

* Batchbearbetning av flera väg begär Anden.
* Matriser med res tid och avstånd mellan en uppsättning ursprung och mål.
* Hitta vägar eller avstånd som användarna kan färdas utifrån tid-eller bränsle krav. 

Mer information om routningsfunktioner finns i [Route API-dokumentationen](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Söktjänst

Sök tjänsten hjälper utvecklare att söka efter adresser, platser, företags listor efter namn eller kategori och annan geografisk information. Sök tjänsten kan [omvända hår kod](https://en.wikipedia.org/wiki/Reverse_geocoding) adresser och korsade gator utifrån latitud och longitud.

![Exempel på en sökning på en karta](media/about-azure-maps/intro_search.png)

Sök tjänsten innehåller även avancerade funktioner som:

* Sök längs en väg.
* Sök i ett större område.
* Gruppera en grupp med Sök begär Anden.
* Sök efter större områden i stället för en plats punkt. 

API:er för sökningar inom batch och område finns för närvarande som förhandsversion. Mer information om Sök funktionerna finns i [Search API-dokumentationen](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Tjänst för spatial verksamhet

Tjänsten Azure Maps spatiala åtgärder tar plats information. Den analyserar snabbt plats information och hjälper till att informera kunder om pågående händelser i tid och utrymme. Den möjliggör analys i nära real tid och förutsägande modellering av händelser. 

Tjänsten gör det möjligt för kunderna att förbättra sin plats information med ett bibliotek med vanliga geospatiala matematiska beräkningar. Vanliga beräkningar är närmast punkt, fantastisk cirkel avstånd och buffertar. Läs mer om tjänsten och de olika funktionerna i API-dokumentationen för [spatiala åtgärder](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Tidszontjänst

Med tids zons tjänsten kan du fråga aktuell, historisk och framtida tids zons information. Du kan använda antingen latitud-och longitud-par eller ett [IANA-ID](https://www.iana.org/). Tids zons tjänsten tillåter också:

* Konvertera Microsoft Windows Time-Zone-ID till IANA-tidszoner.
* Hämtar en tids zons förskjutning till UTC.
* Hämtar aktuell tid i en vald tidszon. 

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

Traffic service är en uppsättning webb tjänster som utvecklare kan använda för webb program eller mobilappar som kräver trafik information. Tjänsten tillhandahåller två typer av data:

* Trafikflöde: real tids observerade hastigheter och res tider för alla nyckel vägar i nätverket.
* Trafik incidenter: en uppdaterad vy över trafiken sylt och incidenter runt vägtrafik nätverket.

![Exempel på en karta med trafik information](media/about-azure-maps/intro_traffic.png)

Mer information finns i dokumentationen om [Traffic API](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Tjänst-till-plats-tjänst

Använd tjänsten IP to Location för att förhandsgranska den hämtade lands koden med två bokstäver för en IP-adress. Den här tjänsten kan hjälpa dig att förbättra användar upplevelsen genom att tillhandahålla anpassat program innehåll baserat på geografisk plats.

Mer information om IP-till-plats-tjänsten i REST API finns i [Azure Maps API-dokumentation för geolokalisering](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programmeringsmodell

Azure Maps har skapats för mobilitet och kan hjälpa dig att utveckla plattforms oberoende program. Den använder en programmerings modell som är språk oberoende och har stöd för JSON-utdata via [REST-API: er](https://docs.microsoft.com/rest/api/maps/).

Azure Maps erbjuder också en praktisk [JavaScript-mappning](https://docs.microsoft.com/javascript/api/azure-maps-control) med en enkel programmerings modell. Utvecklingen är snabb och enkel för både webb-och mobil program.

## <a name="usage"></a>Användning

Att komma åt Azure Maps-tjänsterna är en fråga om att gå till [Azure Portal](https://portal.azure.com) och skapa ett Azure Maps-konto.

I Azure Maps används ett schema för nyckelbaserad autentisering. Ditt konto har två nycklar som redan har skapats för dig, Använd båda nycklarna. Börja integrera de här plats funktionerna i programmet och gör förfrågningar till de Azure Maps tjänsterna.

## <a name="supported-regions"></a>Regioner som stöds

Azure Maps-API: er är för närvarande tillgängliga i alla länder och regioner, förutom följande:

* Kina
* Sydkorea

Kontrol lera att platsen för din aktuella IP-adress finns i ett land som stöds.

## <a name="next-steps"></a>Nästa steg

Prova en exempel app som demonstrerar Azure Maps:

> [!div class="nextstepaction"]
> [Snabb start: skapa en webbapp](quick-demo-map-app.md)

Håll dig uppdaterad om Azure Maps: 

> [!div class="nextstepaction"]
> [Azure Maps blogg](https://azure.microsoft.com/blog/topics/azure-maps/)
