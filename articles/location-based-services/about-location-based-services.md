---
title: "Översikt över Azure-plats-baserade tjänster | Microsoft Docs"
description: "En introduktion till Azure baserat platstjänster (förhandsgranskning)"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c8ebce06a72bcaf769a11ec954702463d7489aa0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>En introduktion till Azure baserat platstjänster (förhandsgranskning)
Azure baserad platstjänster är en uppsättning geospatiala tjänster som omfattar service API: er för Maps, sökning, routning, trafik och tidszoner. Portfölj av Azure OneAPI kompatibla tjänster kan du använda bekant utvecklingsverktyg för att snabbt utveckla och skala lösningar som integrerar platsinformation i dina Azure lösningar. Azure baserad platstjänster ger utvecklare från alla branscher kraftfulla geospatiala funktioner innehåller ny mappning data tvingande att erbjuda geografiska kontext till webb- och mobilprogram. Azure baserad platstjänster är en uppsättning REST API: er tillsammans med en webbaserad JavaScript-kontroll för att utveckla super enkel, flexibel och bärbara över flera medier kompatibla Azure ett API. 

Azure baserad platstjänster består av fem primära tjänster till bättra på Azure-program som kräver geografiska kontext. Varje tjänst förklaras i detalj nedan.

**Återge Service** – återge tjänsten är avsedd för utvecklare att skapa webb- och mobilprogram runt mappning. Tjänsten använder hög kvalitet grafiska rasterbilder, tillgänglig i 19 zoomningsnivåer eller helt anpassningsbar vector kartbilder för format.

![Azure-plats-baserade tjänster Map.png](media/about-location-based-services/Introduction_Map.png)

**Vidarebefordra Service** – Route-tjänsten har byggts med robust verkliga infrastruktur geometri beräkningar och flera transport läge riktningar. Tjänsten gör det möjligt för utvecklare att beräkna riktningar i ett antal resa lägen, till exempel bil lastbil, cykel eller gå; samt ett antal indata som villkor för trafik, vikt begränsningar eller farliga material transport.

![Azure-plats-baserade tjänster Route.png](media/about-location-based-services/Introduction_Route.png)

**Söktjänsten** – Search-tjänsten är avsedd för utvecklare att söka efter adresser, platser, listor business efter namn eller kategori och andra geografisk information. Söktjänsten kan också [omvänd geocode](https://en.wikipedia.org/wiki/Reverse_geocoding) adresser och mellan streets baserat på en latitud/longitud. 

![Azure-plats-baserade tjänster Search.png](media/about-location-based-services/Introduction_Search.png)

**Tidszon Service** – tidszon tjänsten kan du fråga aktuella historiska och framtida tidszonsinformation med hjälp av antingen latitud longitud par eller en [IANA ID](http://www.iana.org/). Tjänsten tidszon kan även användas för konvertering av Microsoft Windows tidszon-ID: N till IANA tidszoner, hämtar en förskjutning av tidszonen till UTC och hämtar den aktuella tiden i respektive tidszon. Ett typiskt JSON-svar för en fråga till tjänsten tidszon ser ut som följande:

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

**Trafik Service** – trafik tjänsten är en uppsättning webbtjänster som utformats för utvecklare att skapa webb- och mobila program som kräver trafik. Erbjudandet är uppdelat i följande:
1. Infrastrukturtrafiken rör – ger realtidsskydd observerade hastigheter och reser tider för alla viktiga vägar i nätverket. och 
2. Trafik incidenter - ger en korrekt bild om trafik Sylter och incidenter runt väg-nätverk.

![Azure-plats-baserad trafik via webbtjänster](media/about-location-based-services/Introduction_Traffic.png)

Azure baserad platstjänster har skapats för mobility och sätta plattformsoberoende program eftersom programmeringsmiljö är oberoende och har stöd för JSON-utdata via REST API: er. Dessutom erbjuder Azure kg en lämplig JavaScript-Kartkontrollen med en enkel programmeringsmodell för snabb enkelt utvecklingen av både webb- och mobilprogram. 

Azure baserad platstjänster använder ett schema för nyckel-baserad autentisering, så att komma åt tjänsterna är en fråga om att gå till den [Azure-portalen](http://portal.azure.com) och skapa ett Azure plats Services-konto. Ditt konto har två nycklar förgenererade för dig. Starta integrera dessa funktioner på plats direkt i dina program med någon av dina nycklar i begäranden till tjänsten Azure baserad platstjänster.

Registrera dig för en [Azure baserad platstjänster konto idag!](http://aka.ms/azurelbsportal)

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure plats baserat Services (förhandsversion). Nästa steg är att testa en exempelapp med plats baserade tjänster, samt skapa en slutpunkt till slutpunkt-scenario i ditt webbprogram.

> [!div class="nextstepaction"]
> [Starta demo interaktiv karta sökningen med Azure baserad platstjänster (förhandsgranskning)](quick-demo-map-app.md)
> [Sök Närliggande intressant med hjälp av Azure baserad platstjänster](tutorial-search-location.md)