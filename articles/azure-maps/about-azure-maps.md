---
title: Översikt över Azure Maps | Microsoft Docs
description: En introduktion till Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: e84580c4023be87ebfc1988c631af0b76e213987
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="an-introduction-to-azure-maps"></a>En introduktion till Azure Maps
Azure Maps är en portfölj med geospatiala tjänster som omfattar tjänst-API:er för kartor, sökning, ruttplanering, trafik och tidszoner. Med portföljen med Azure OneAPI-kompatibla tjänster kan du använda välbekanta verktyg för att snabbt utveckla och skala lösningar som integrerar platsinformation i dina Azure-lösningar. Azure Maps ger utvecklare från alla branscher kraftfulla geospatiala funktioner med nya mappningsdata som krävs för att tillhandahålla geografisk kontext till webb- och mobilprogram. Azure Maps är en uppsättning REST API:er som åtföljs av en webbaserad JavaScript-kontroll som gör utveckling över flera medier enkelt, flexibelt och portabelt. 

Följande video introducerar Azure Maps:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Azure Maps består av fem primära tjänster som stöder Azure-program som kräver geografisk kontext. Varje tjänst förklaras i detalj.

**Render Service** är utformad för utvecklare som skapar webb- och mobilprogram runt mappning. Tjänsten använder antingen grafiska rasterbilder av hög kvalitet, tillgängliga i 19 zoomningsnivåer, eller helt anpassningsbara kartbilder i vektorformat.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

**Route Service** innehåller robusta geometriberäkningar från verklig infrastruktur och flera transportlägesriktningar. Tjänsten gör det möjligt för utvecklare att beräkna riktningar efter ett antal färdmedel, till exempel bil, lastbil, cykel eller gående. Tjänsten kan också inkludera indata som trafikförhållanden, viktbegränsningar eller transport av farligt material.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

**Search Service** är utformad för utvecklare för att söka efter adresser, platser, företag efter namn eller kategori och annan geografisk information. Search Service kan också [omvänt geokoda](https://en.wikipedia.org/wiki/Reverse_geocoding) adresser och gatukorsningar baserat på latitud/longitud. 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

**Time Zone Service** tillåter användare att skicka frågor mot aktuell, historisk och framtida tidszonsinformation med hjälp av antingen latitud- och longitudpar eller ett [IANA-ID](http://www.iana.org/). Time Zone Service kan även användas för att konvertera Microsoft Windows tidszon-ID till IANA-tidszoner, hämta en tidszonsförskjutning mot UTC och hämta den aktuella tiden i respektive tidszon. Ett typiskt JSON-svar på en fråga till Time Zone Service ser ut så här:

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

**Traffic Service** är en svit med webbtjänster som utformats för utvecklare som skapar webb- och mobilprogram som kräver trafik. Tjänsten tillhandahåller två typer av data:
* Traffic flow – realtidsinformation om observerade hastigheter och resetider för alla viktiga vägar i nätverket. 
* Traffic incidents – en precis bild över trafikstockningar och olyckor i vägnätet.

![Azure Maps-trafik](media/about-azure-maps/Introduction_Traffic.png)

Azure Maps har skapats för rörlighet och fungerar plattformsoberoende, eftersom programmeringsmodellen är oberoende och stöder JSON-utdata via REST API:er. Dessutom erbjuder Azure Maps en praktisk JavaScript-kartkontroll med en enkel programmeringsmodell för snabb och enkel utveckling av både webb- och mobilprogram. 

Azure Maps använder ett nyckelbaserat autentiseringsschema. Åtkomst till tjänsterna sker via [Azure-portalen](http://portal.azure.com), där man kan skapa ett Azure Maps-konto. Ditt konto har två nycklar som har genererats åt dig. Börja integrera de här platsfunktioner direkt i dina program med någon av dina nycklar i begärandena till Azure Maps-tjänsten.

## <a name="unsupported-regions"></a>Regioner som inte stöds
Azure Maps API är för närvarande inte tillgänglig alla länder. Kontrollera din aktuella IP-adress och dess plats. Länderna som nämns nedan stöds inte:

* Argentina
* Kina
* Indien
* Marocko
* Pakistan
* Sydkorea

## <a name="relationship-with-bing-maps"></a>Relationen med Bing-kartor
Kartorna som beskrivs i detta dokument skiljer sig från de som tillhandahålls av Bing-kartor. Även om de har flera funktioner gemensamt så är de två tjänsterna olika och inte relaterade. Den här Azure-tjänsten påverkar inte erbjudanden eller översikten som gäller Bing-kartor.

Microsofts mål är att ge utvecklare ett val när det gäller erbjudanden om platstjänster. Följande tabell innehåller riktlinjer för utvecklare för att hjälpa välja tjänst: 

| Scenario | Använd Azure Maps när… | Använd Bing-kartor när… |
| ------------- | ------------- | ------------- |
| Utvecklingsmiljö | du skapar i eller koordinerar med andra Azure-tjänster | använder en moln- eller utvecklingsmiljö från tredje part |
| Utvecklingsfas  | Eftersom Azure Maps för närvarande finns som en allmänt tillgänglig förhandsversion, har tjänsten optimerats för tidig testning och Proof of Concept-utveckling | Ett serviceavtal i företagsklass krävs för en produktionsmiljö |
| Prisalternativ | Preliminära prisalternativ för utvecklare räcker | Anpassad prissättning i företagsklass krävs |
| Miljö för användningsfall | Användning i fordon krävs | Användning i fordon krävs inte |
| Geografisk täckning | Indien, Kina, Japan och Sydkorea krävs inte | Karttäckning för Indien, Kina, Japan och Sydkorea krävs |
| Kartinnehåll | Standardytkartor räcker | Satellit-, flygfoto- och gatubilder krävs |
| Underliggande kartkälla | TomTom-kartdata föredras | HERE-kartdata föredras |

Registrera dig för ett [Azure Maps-konto redan idag](http://aka.ms/azurelbsportal).

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure Maps. Nästa steg är att testa en exempelapp som använder tjänsten.

> [!div class="nextstepaction"]
> [Starta en demokarta för interaktiv sökning](quick-demo-map-app.md)

