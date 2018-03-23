---
title: Översikt av Azure Location Based Services | Microsoft Docs
description: En introduktion till Azure Location Based Services (förhandsversion)
services: location-based-services
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 02/05/2018
ms.topic: overview
ms.service: location-based-services
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 91df49aa3f522ea765698a1d64ed2686606f71f3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>En introduktion till Azure Location Based Services (förhandsversion)
Azure Location Based Services är en portfölj med geospatiala tjänster som omfattar tjänst-API:er för kartor, sökning, ruttplanering, trafik och tidszoner. Med portföljen med Azure OneAPI-kompatibla tjänster kan du använda välbekanta utvecklingsverktyg för att snabbt utveckla och skala lösningar som integrerar platsinformation i dina Azure-lösningar. Azure Location Based Services ger utvecklare från alla branscher kraftfulla geospatiala funktioner med nya mappningsdata som krävs för att tillhandahålla geografisk kontext till webb- och mobilprogram. Azure Location Based Services är en uppsättning REST API:er som är kompatibla med Azure One API och som åtföljs av en webbaserad JavaScript-kontroll som gör utveckling över flera medier enkelt, flexibelt och portabelt. 

Följande video introducerar Azure Location Based Services.

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Azure Location Based Services består av fem primära tjänster för att understödja Azure-program som kräver geografisk kontext. Varje tjänst förklaras i detalj nedan.

**Render Service** – Render Service är utformad för utvecklare för att skapa webb- och mobilprogram runt mappning. Tjänsten använder antingen grafiska rasterbilder av hög kvalitet, tillgängliga i 19 zoomningsnivåer, eller helt anpassningsbara kartbilder i vektorformat.

![Azure Location Based Services Map.png](media/about-location-based-services/Introduction_Map.png)

**Route Service** – Route Service har byggts med robusta geometriberäkningar från verklig infrastruktur och flera transportlägesriktningar. Tjänsten gör det möjligt för utvecklare att beräkna riktningar i ett antal reslägen, till exempel bil, lastbil, cykel eller till fots; samt ett antal indata som trafikförhållanden, viktbegränsningar eller transport av farliga material.

![Azure Location Based Services Route.png](media/about-location-based-services/Introduction_Route.png)

**Search Service** – Search Service är avsedd för utvecklare för att söka efter adresser, platser, företag efter namn eller kategori och annan geografisk information. Search Service kan också [omvänt geokoda](https://en.wikipedia.org/wiki/Reverse_geocoding) adresser och gatukorsningar baserat på latitud/longitud. 

![Azure Location Based Services Search.png](media/about-location-based-services/Introduction_Search.png)

**Time Zone Service** – Med Time Zone Service kan du skicka frågor mot aktuell, historisk och framtida tidszonsinformation med hjälp av antingen latitud- och longitudpar eller ett [IANA-ID](http://www.iana.org/). Time Zone Service kan även användas för att konvertera Microsoft Windows tidszon-ID till IANA-tidszoner, hämta en tidszonsförskjutning mot UTC och hämta den aktuella tiden i respektive tidszon. Ett typiskt JSON-svar på en fråga till Time Zone Service ser ut så här:

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

**Traffic Service** – Traffic Service är en svit med webbtjänster som utformats för utvecklare för att skapa webb- och mobilprogram som kräver trafik. Erbjudandet innehåller följande:
1. Traffic Flow – tillhandahåller realtidsinformation om observerade hastigheter och resetider för alla viktiga vägnätet, 
2. Trafik incidents – tillhandahåller en precis bild om trafikstockningar och olyckor i vägnätet.

![Azure Location Based Services Traffic](media/about-location-based-services/Introduction_Traffic.png)

Azure Location Based Services har skapats för rörlighet och fungerar plattformsoberoende eftersom programmeringsmodellen är oberoende och har stöd för JSON-utdata via REST API: er. Dessutom erbjuder Azure LBS en praktisk JavaScript-kartkontroll med en enkel programmeringsmodell för snabb och enkel utveckling av både webb- och mobilprogram. 

I Azure Location Based Services används ett nyckelbaserat autentiseringsschema så man kommer åt tjänsterna genom att gå till [Azure-portalen](http://portal.azure.com) och skapa ett Azure Location Based Services-konto. Ditt konto har två nycklar som har genererats åt dig. Börja integrera de här platsfunktioner direkt i dina program med någon av dina nycklar i begäranden till tjänsten Azure Location Based Services.

## <a name="unsupported-regions"></a>Regioner som inte stöds
Vår API är för närvarande inte tillgänglig i länderna som nämns nedan. Kontrollera den aktuella IP-adressen och kontrollera att platsen för din IP-adress inte är något av länderna som inte stöds och som nämns nedan.

* Argentina
* Kina
* Indien
* Marocko
* Pakistan
* Sydkorea

## <a name="relationship-with-bing-maps"></a>Relationen med Bing-kartor
Azure Location Based Services som beskrivs i detta dokument skiljer sig från det som tillhandahålls av Bing-kartor.  Även om de har flera funktioner gemensamt så är de två tjänsterna olika och inte relaterade.  Produkterbjudandet eller vägkartan för Bing-kartor påverkar inte tillgängligheten för den här nya tjänsten i Azure, som hanteras separat.

Microsofts mål är att ge utvecklare ett val när det gäller erbjudanden om platstjänster.  Nedan visas en kort vägledning för utvecklare om vilken tjänst som ska användas för olika användningsscenarier och kundsituationer.  Tänk på att den här vägledningen för närvarande gäller för Azure LBS i den allmänt tillgängliga förhandsversionen, och den kommer att uppdateras när funktionen blir allmänt tillgänglig senare under 2018.

| Kundkriterier | Använd Azure Location Based Services när… | Använd Bing-kartor när… |
| ------------- | ------------- | ------------- |
| Utvecklingsmiljö | Bygga i eller använda andra Azure-tjänster | Med en moln- eller utvecklingsmiljö från tredje part |
| Utvecklingsfas  | Eftersom Azure LBS för närvarande är en allmänt tillgänglig förhandsversion har tjänsten optimerats för tidig testning och Proof of Concept-utveckling | Ett serviceavtal i företagsklass krävs för en produktionsmiljö |
| Prisalternativ | Preliminära prisalternativ för utvecklare räcker | Anpassad prissättning i företagsklass krävs |
| Miljö för användningsfall | Användning i fordon krävs | Användning i fordon krävs inte |
| Geografisk täckning | Indien, Kina, Japan och Sydkorea krävs inte | Karttäckning för Indien, Kina, Japan och Sydkorea krävs |
| Kartinnehåll | Standardytkartor räcker | Satellit-, flygfoto- och gatubilder krävs |
| Underliggande kartkälla | TomTom-kartdata föredras | HERE-kartdata föredras |

Registrera dig för ett [Azure Location Based Services-konto i dag](http://aka.ms/azurelbsportal).

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure Location Based Services (förhandsversion). Nästa steg är att testa en exempelapp med Location Based Services samt att skapa en slutpunkt till heltäckande scenario i ditt webbprogram.

> [!div class="nextstepaction"]
> [Starta en demo med interaktiv kartsökningen med Azure Location Based Services (förhandsversion)](quick-demo-map-app.md)
> [Sök efter orienteringspunkter i närheten med Azure Location Based Services](tutorial-search-location.md)
