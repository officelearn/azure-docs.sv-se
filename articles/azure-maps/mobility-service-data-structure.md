---
title: Mobility Service datastrukturer i Azure Maps | Microsoft Docs
description: Datastrukturer i Azure Maps Mobilitetstjänsten
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735563"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Datastrukturer i Azure Maps Mobilitetstjänsten

Den här artikeln introducerar konceptet för appar i Metro-område i [Mobilitetstjänsten för Azure Maps](https://aka.ms/AzureMapsMobilityService) och vissa av fälten returneras via tjänsterna, när det efterfrågas offentliga överföringen stoppas och linjer. Vi rekommenderar att du går igenom den här artikeln innan du börjar med Mobility Service API: er. Vi går igenom de här fälten nedan.

## <a name="metro-area"></a>Metro området

Mobility Service data delas upp i metro områdena som stöds. Metro områden efter inte stad gränser, ett metro område kan innehålla flera städer, till exempel tätbefolkade stad och dess omgivande städer; och land/region kan vara ett metro område. 

Den `metroID` är ett metro område-ID som kan användas för att anropa den [hämta appar i Metro området Info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo) till typer för överföring av begäranden som stöds och ytterligare information för metro område, till exempel överföring myndigheter och aktiva aviseringar. Du kan använda Azure-Maps hämta appar i Metro-API för att begära metro områdena som stöds och metroIDs. Metro området ID: N kan ändras.

**metroID:** 522 **namn:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stoppa ID: N

Överföringen stoppas kan refereras till av två typer av ID, den [Allmänt överföring Feed-specifikationen (GFTS)](https://gtfs.org/) ID (kallas stopKey) och Azure Maps stoppa ID (kallas stopId). När det gäller stoppas över tid, rekommenderas för att använda Azure Maps stop-ID, eftersom detta ID är mer stabil och troligen kommer inte att förändras så länge som den fysiska stoppa finns. Stop-ID: T för GTFS uppdateras oftare, exempelvis om GTFS providern måste du ändra det eller ny GTFS version släpps, även om den fysiska stoppa hade ingen ändring.

Om du vill starta, kan du begära i närheten överföring slutar med hjälp av [hämta i närheten överföring API](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Radgrupper och rader

Mobilitetstjänsten använder en parallell-datamodell för rader och radgrupper bättre utan ändringar som har ärvts från [GTFS](https://gtfs.org/) vägar, resor och datamodellen.


### <a name="line-groups"></a>Radgrupper

En grupp är en entitet, vilka grupper samman alla rader som logiskt ingår i samma grupp. En grupp innehåller vanligtvis två rader, en går från punkt A till B, samt andra returneras från punkt B till A, båda tillhör samma offentlig Transport myndighet och har samma radnummer. Det kan dock finnas fall där en grupp har fler än två rader eller bara en enda rad i den.


### <a name="lines"></a>rader

Som beskrivs ovan består varje radgrupp av en uppsättning rader. Ofta varje rad beskriver en riktning och varje rad hanteringsgrupp består av två rader. Men det finns fall i vilka fler rader utgör en grupp, till exempel det är en linje som ibland detours via en viss stadsdel och ibland inte och drivs i båda fallen under samma radnumret och det finns andra fall där en rad g rupp består av en enskild rad, till exempel en cirkulär rad med en enda riktning.

Om du vill börja, kan du begära radgrupper med hjälp av den [hämta API för överföring rad](https://aka.ms/AzureMapsMobilityTransitLine) och senare detaljnivån för rader.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du begär överföring data med hjälp av Mobilitetstjänsten:

> [!div class="nextstepaction"]
> [Hur du begär data för överföring](how-to-request-transit-data.md)

Lär dig hur du begär data i realtid med hjälp av Mobilitetstjänsten:

> [!div class="nextstepaction"]
> [Hur du begär data i realtid](how-to-request-real-time-data.md)

Utforska Azure Maps Mobility Service API-dokumentation

> [!div class="nextstepaction"]
> [Mobility Service API-dokumentation](https://aka.ms/AzureMapsMobilityService)
