---
title: Datastrukturer för Mobilitetstjänsten i Azure Maps| Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om vanliga fält och datastrukturer som returneras via Microsoft Azure Maps Mobility Services.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4dfc6793bba473c4046863937baa292dde7bf421
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478700"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Datastrukturer i Azure Maps Mobility Service

Den här artikeln introducerar konceptet Metro Area i [Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService). Vi diskuterar några av de vanliga fält som returneras när den här tjänsten efterfrågas för stopp och linjer för kollektivtrafik. Vi rekommenderar att du läser den här artikeln innan du utvecklar med MOBILITETSTJÄNST API:er.

## <a name="metro-area"></a>Storstadsområde

Mobilitetstjänstdata grupperas efter metroområden som stöds. Storstadsområden följer inte stadens gränser. Ett storstadsområde kan innehålla flera städer, tätbefolkade städer och omgivande städer. I själva verket kan ett land / region vara ett storstadsområde. 

Det `metroID` är ett storstadsområde id som kan användas för att ringa [Get Metro Area Info API.](https://aka.ms/AzureMapsMobilityMetroAreaInfo) Använd Azure Maps "Get Metro"-API för att begära transittyper, transitbyråer, aktiva aviseringar och ytterligare information för den valda tunnelbanan. Du kan också begära metroområden och metro-ID som stöds. Id-3-4-4-4-4-4-4-4-4-4

**metroID:** 522 **Namn:** Seattle-Tacoma-Bellevue

![Seattle-metro-området](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stoppa ID:er

Transitstopp kan refereras till av två typer av ID:n, [GFTS-ID (General Transit Feed Specification)](http://gtfs.org/) och Azure Maps stop ID. GFTS-ID kallas stopKey och Azure Maps stop ID kallas stopID. När du ofta refererar till transitstopp uppmanas du att använda stopp-ID:n för Azure Maps. stopID är stabilare och sannolikt att förbli densamma så länge det fysiska stoppet finns. GTFS-stopp-ID uppdateras oftare. GTFS-stopp-ID kan till exempel uppdateras per GTFS-providerbegäran eller när en ny GTFS-version släpps. Även om det fysiska stoppet inte hade någon förändring kan GTFS-stopp-ID ändras.

Till att börja med kan du begära transitstopp i närheten med [Get Närliggande transit-API](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Radgrupper och rader

Mobilitetstjänsten använder en parallell datamodell för linjer och linjegrupper. Den här modellen används för att bättre hantera ändringar som ärvts från [GTFS-rutter](http://gtfs.org/) och trips-data.


### <a name="line-groups"></a>Radgrupper

En radgrupp är en entitet som grupperar alla rader som logiskt sett ingår i samma grupp. Vanligtvis innehåller en radgrupp två rader, en från punkt A till B och den andra som returnerar från punkt B till A. Båda linjerna skulle tillhöra samma kollektivtrafikbyrå och ha samma radnummer. Det kan dock finnas fall där en radgrupp har fler än två rader eller bara en enda rad i den.


### <a name="lines"></a>Rader

Som diskuterats ovan består varje radgrupp av en uppsättning rader. Varje radgrupp består av två rader och varje rad beskriver en riktning.  Det finns dock fall där fler rader utgör en radgrupp. Till exempel finns det en linje som ibland omvägar genom ett visst grannskap och ibland inte. I båda fallen fungerar den under samma radnummer. Även en linjegrupp kan bestå av en enda rad. En cirkulär linje med en enda riktning är en linggrupp med en rad.

Till att börja med kan du begära radgrupper med hjälp av [API:et Hämta transitlinje](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Nästa steg

Läs om hur du begär transitdata med Mobilitetstjänsten:

> [!div class="nextstepaction"]
> [Så här begär du transitdata](how-to-request-transit-data.md)

Lär dig hur du begär realtidsdata med Mobilitetstjänsten:

> [!div class="nextstepaction"]
> [Så här begär du realtidsdata](how-to-request-real-time-data.md)

Utforska API-dokumentationen för Azure Maps Mobility Service

> [!div class="nextstepaction"]
> [API-dokumentation för Mobilitetstjänster](https://aka.ms/AzureMapsMobilityService)
