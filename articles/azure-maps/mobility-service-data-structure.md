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
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910725"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Data strukturer i Azure Maps Mobility Service

I den här artikeln beskrivs begreppet tunnelbane linje i [Azure Maps mobilitets tjänsten](https://aka.ms/AzureMapsMobilityService) och några av de vanliga fält som returneras via tjänsterna, när de efter frågas för offentliga överförings stopp och-rader. Vi rekommenderar att du går igenom den här artikeln innan du börjar med mobilitets tjänstens API: er. Vi diskuterar dessa gemensamma fält nedan.

## <a name="metro-area"></a>Tunnelbane yta

Mobilitets tjänstens data delas upp i Metro-områden som stöds. Metro-områden följer inte stads gränser, ett tunnelbane område kan innehålla flera städer, till exempel en tätt ifylld stad och dess omgivande städer. och ett land/region kan vara ett tunnelbane område. 

`metroID` är ett Metro-areans ID som kan användas för att anropa [API: t för att hämta information om Metro-områden](https://aka.ms/AzureMapsMobilityMetroAreaInfo) till begär ande överförings typer och ytterligare information om Metro-ytan, till exempel överförings myndigheter och aktiva aviseringar. Du kan använda Azure Maps Hämta Metro API för att begära de tunnelbane områden som stöds och metroIDs. Metro-områdes-ID: n kan komma att ändras.

**metroID:** 522 **Namn:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stopp-ID

Överförings stopp kan refereras till av två typer av ID: n, General GFTS-ID: t ( [transport feed Specification)](https://gtfs.org/) (kallas stopKey) och Azure Maps stopp-ID (kallas stopId). När du refererar till att stoppas med tiden rekommenderar vi att du använder Azure Maps stopp-ID eftersom detta ID är mer stabilt och inte kommer att ändras så länge det fysiska stoppet finns. Stopp-ID: t för GTFS uppdateras oftare, t. ex. om GTFS-providern behöver ändra den eller om den nya GTFS-versionen har frigjorts, även om det fysiska stoppet inte hade någon förändring.

För att starta kan du begära att närliggande överföring stoppas med hjälp av [Get närbelägen överförings-API](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Linje grupper och linjer

Mobilitets tjänsten använder en parallell data modell för linjer och linje grupper för att bättre hantera ändringar som ärvts från [GTFS](https://gtfs.org/) vägar och resor data modeller.


### <a name="line-groups"></a>Linje grupper

En linje grupp är en entitet som grupper samman alla rader som är logiskt indelade i samma grupp. Vanligt vis innehåller en linje grupp två rader, en som går från punkt A till B och den andra som återvänder från punkt B till A, som båda tillhör samma offentliga transport byrå och har samma rad nummer. Det kan dock finnas fall där en linje grupp har fler än två rader eller bara en enda rad i den.


### <a name="lines"></a>Rader

Som vi nämnt ovan består varje linje grupp av en uppsättning rader. Varje rad beskriver ofta en riktning och varje linje grupp består av två rader. Det finns dock fall där fler rader består av en linje grupp, till exempel om det finns en rad som ibland deklarerar genom en viss och ibland inte, och som körs i båda fallen under samma rad nummer och det finns andra fall där en rad g Rupp består av en enda rad, till exempel en cirkulär linje med en enda riktning.

För att börja kan du begära linje grupper med hjälp av [API för Get-överföring](https://aka.ms/AzureMapsMobilityTransitLine) och senare gå nedåt till rader.


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
