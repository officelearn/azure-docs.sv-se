---
title: Mobilitets tjänstens data strukturer i Azure Maps | Microsoft Azure Maps
description: I den här artikeln får du lära dig om gemensamma fält och data strukturer som returneras via Microsoft Azure Maps Mobility Services.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ceecdcc508e5b43c8775b6a88f9b4e4f0eb23c77
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989016"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Data strukturer i Azure Maps Mobility Service

I den här artikeln beskrivs begreppet tunnelbane linje i [Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService). Vi diskuterar några vanliga fält som returneras när den här tjänsten efter frågas för offentliga överförings stopp och-rader. Vi rekommenderar att du läser den här artikeln innan du utvecklar med mobilitets tjänstens API: er.

## <a name="metro-area"></a>Tunnelbane yta

Mobilitets tjänstens data grupperas efter stödda Metro-områden. Metro-områden följer inte stads gränser. Ett tunnelbane område kan innehålla flera städer, tätt ifylld stad och omgivande städer. Ett land/region kan i själva verket vara ett tunnelbane område. 

`metroID` är ett Metro-områdens ID som kan användas för att anropa API: t för att [Hämta information om Metro-ytan](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Använd Azure Maps "Get Metro"-API för att begära överförings typer, transit myndigheter, aktiva aviseringar och ytterligare information för den valda tunnelbane linje. Du kan också begära de tunnelbane områden och metroIDs som stöds. Metro-områdes-ID: n kan komma att ändras.

**metroID:** 522 **Namn:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stopp-ID

Överförings stopp kan refereras till av två typer av ID: n, General GFTS-ID: t [(transport feed Specification)](https://gtfs.org/) och Azure Maps stopp-ID. GFTS-ID kallas för stopKey och Azure Maps stopp-ID kallas stopID. När du ofta hänvisar till överförings stopp rekommenderar vi att du använder Azure Maps stopp-ID. stopID är mer stabil och förmodligen kvar så länge det fysiska stoppet finns. Stopp-ID: t för GTFS uppdateras oftare. Till exempel kan GTFS Stop ID uppdateras per GTFS Provider-begäran eller när en ny GTFS-version släpps. Även om det fysiska stoppet inte hade någon ändring, kan stopp-ID: t för GTFS ändras.

För att starta kan du begära att närliggande överföring slutar att använda [Get närbelägen överförings-API](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Linje grupper och linjer

Mobilitets tjänsten använder en parallell data modell för linjer och linje grupper. Den här modellen används för att bättre hantera ändringar som ärvts från [GTFS](https://gtfs.org/) -vägar och TRIPs-data.


### <a name="line-groups"></a>Linje grupper

En linje grupp är en entitet som grupper samman alla rader som är logiskt indelade i samma grupp. Vanligt vis innehåller en linje grupp två rader, en från punkt A till B, och den andra som returnerar från punkt B till A. Båda raderna tillhör samma offentliga transport byrå och har samma rad nummer. Det kan dock finnas fall där en linje grupp har fler än två rader eller bara en enda rad i den.


### <a name="lines"></a>Operationsföljdsrader

Som vi nämnt ovan består varje linje grupp av en uppsättning rader. Varje linje grupp består av två rader och varje rad beskriver en riktning.  Det finns dock fall där fler rader skapar en linje grupp. Det finns till exempel en rad som ibland deklarerar genom en viss plats och ibland inte. I båda fallen fungerar den under samma rad nummer. Dessutom kan en linje grupp bestå av en enda rad. En cirkulär linje med en enda riktning är en ling grupp med en rad.

För att börja kan du begära linje grupper med hjälp av [API: t get transport line](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Nästa steg

Lär dig hur du begär överförings data med mobilitets tjänsten:

> [!div class="nextstepaction"]
> [Så här begär du överförings data](how-to-request-transit-data.md)

Lär dig hur du begär data i real tid med mobilitets tjänsten:

> [!div class="nextstepaction"]
> [Så här begär du real tids data](how-to-request-real-time-data.md)

Utforska dokumentationen för Azure Maps Mobility Service API

> [!div class="nextstepaction"]
> [API-dokumentation för Mobility Service](https://aka.ms/AzureMapsMobilityService)
