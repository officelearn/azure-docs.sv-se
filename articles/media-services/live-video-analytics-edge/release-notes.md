---
title: Video analys i real tid för IoT Edge viktig information – Azure
description: Det här avsnittet innehåller viktig information om real tids analys av IoT Edge-versioner, förbättringar, fel korrigeringar och kända problem.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091787"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Viktig information om live video analys i IoT Edge

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` i din RSS-feed läsare.

Den här artikeln innehåller information om:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner

## <a name="july-13-2020"></a>13 juli 2020

Den här versions tag gen för uppdateringen från juli 2020 för modulen är:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 1 (Real-Video-Analytics: 1). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.

### <a name="new-features"></a>Nya funktioner
* Nu kan du skapa diagram-topologier som har en nod för till gångs mottagare och en noden filmottagare intill en nod i en signal grind processor. Se [det här](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) för ett exempel.

### <a name="bug-fixes"></a>Felkorrigeringar
* Förbättringar av validering av önskade egenskaper

## <a name="june-1-2020"></a>Den 1 juni 2020

Den här versionen är den första offentliga för hands versionen av video analys på IoT Edge. Versions tag gen är

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funktioner som stöds
* Analysera direktuppspelade video strömmar med AI-moduler som du väljer och spela in video på gräns enheten eller i molnet
* Använd på Linux AMD64-operativ system som [stöds](../../iot-edge/support.md) av IoT Edge
* Distribuera och konfigurera modulen via IoT Hub med Azure Portal eller Visual Studio Code
* Hantera [diagram-topologier och diagram instanser](media-graph-concept.md#media-graph-topologies-and-instances) via fjärr anslutning eller lokalt genom följande direkta metod anrop

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>Nästa steg

[Översikt](overview.md)
