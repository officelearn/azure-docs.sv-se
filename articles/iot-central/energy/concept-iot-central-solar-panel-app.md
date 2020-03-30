---
title: Arkitektoniska koncept i Azure IoT Central - Energi | Microsoft-dokument
description: Den här artikeln introducerar viktiga begrepp som rör arkitekturen i Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77018014"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central – apparkitektur för solpaneler




Den här artikeln innehåller en översikt över mallarkitekturen för övervakning av webbpanelen. Diagrammet nedan visar en vanlig arkitektur för solpanelapp på Azure med hjälp av IoT Central-plattformen.

> [!div class="mx-imgBorder"]
> ![smart mätararkitektur](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Den här arkitekturen består av följande komponenter. Vissa program kanske inte kräver alla komponenter som anges här.

## <a name="solar-panels-and-connectivity"></a>Solpaneler och anslutning 

Solpaneler är en av de betydande källorna till förnybar energi. Beroende på solpaneltypen och ställa in kan du ansluta den antingen med gateways eller andra mellanliggande enheter och proprietära system. Du kan behöva skapa IoT Central enhetsbrygga för att ansluta enheter som inte kan anslutas direkt. IoT Central device bridge är en lösning med öppen källkod och du hittar den fullständiga informationen [här](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 



## <a name="iot-central-platform"></a>IoT Central plattform
Azure IoT Central är en plattform som förenklar uppbyggnaden av din IoT-lösning och hjälper till att minska bördan och kostnaderna för IoT-hantering, drift och utveckling. Med IoT Central kan du enkelt ansluta, övervaka och hantera dina IoT-tillgångar (Internet of Things) i stor skala. När du har anslutit solpanelerna till IoT Central använder appmallen inbyggda funktioner som enhetsmodeller, kommandon och instrumentpaneler. Appmallen använder också IoT Central-lagring för varma sökvägsscenarier som övervakning, analys, regler och visualisering i realtid.


## <a name="extensibility-options-to-build-with-iot-central"></a>Utökningsmöjligheter att bygga med IoT Central
IoT Central-plattformen innehåller två utökningsalternativ: CDE (Continuous Data Export) och API:er. Kunderna och partnerna kan välja mellan dessa alternativ baserat på att anpassa sina lösningar för specifika behov. Till exempel konfigurerade en av våra partner CDE med Azure Data Lake Storage (ADLS). De använder ADLS för långsiktig datalagring och andra scenarier för lagring av kall sökvägar, till exempel batchbearbetning, granskning och rapportering. 

## <a name="next-steps"></a>Nästa steg

* Nu när du har lärt dig om arkitekturen, [skapa solpanel app gratis](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Mer information om IoT Central finns i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/)