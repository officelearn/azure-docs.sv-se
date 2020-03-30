---
title: Arkitektoniska koncept i Azure IoT Central - Energi | Microsoft-dokument
description: Den här artikeln introducerar viktiga begrepp som relaterar arkitekturen för Azure IoT Central energy app mall
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77024321"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central - arkitektur för appar med smarta mätare



Den här artikeln innehåller en översikt över arkitekturen för appmallarkitektur för smarta mätarövervakningsprogram. Diagrammet nedan visar en vanlig arkitektur för smart mätarapp på Azure med IoT Central-plattformen.

> [!div class="mx-imgBorder"]
> ![smart mätararkitektur](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Den här arkitekturen består av följande komponenter. Vissa lösningar kanske inte kräver alla komponenter som anges här.

## <a name="smart-meters-and-connectivity"></a>Smarta mätare och anslutning 

En smart mätare är en av de viktigaste enheterna bland alla energitillgångar. Den registrerar och kommunicerar energiförbrukningsdata till verktyg för övervakning och andra användningsfall, till exempel fakturering och efterfrågesvar. Baserat på mätartypen kan den ansluta till IoT Central antingen med hjälp av gateways eller andra mellanliggande enheter eller system, sådana kantenheter och head-end-system. Skapa IoT Central enhetsbrygga för att ansluta enheter som inte kan anslutas direkt. IoT Central device bridge är en lösning med öppen källkod och du hittar den fullständiga informationen [här](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 


## <a name="iot-central-platform"></a>IoT Central plattform

Azure IoT Central är en plattform som förenklar uppbyggnaden av din IoT-lösning och hjälper till att minska bördan och kostnaderna för IoT-hantering, drift och utveckling. Med IoT Central kan du enkelt ansluta, övervaka och hantera dina IoT-tillgångar (Internet of Things) i stor skala. När du har anslutit dina smarta mätare till IoT Central använder appmallen inbyggda funktioner som enhetsmodeller, kommandon och instrumentpaneler. Appmallen använder också IoT Central-lagring för varma sökvägsscenarier som övervakning, analys, regler och visualisering i realtid. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Utökningsmöjligheter att bygga med IoT Central
IoT Central-plattformen innehåller två utökningsalternativ: CDE (Continuous Data Export) och API:er. Kunderna och partnerna kan välja mellan dessa alternativ baserat på att anpassa sina lösningar för specifika behov. Till exempel konfigurerade en av våra partner CDE med Azure Data Lake Storage (ADLS). De använder ADLS för långsiktig datalagring och andra scenarier för lagring av kall sökvägar, till exempel batchbearbetning, granskning och rapportering. 

## <a name="next-steps"></a>Nästa steg

* Nu när du har lärt dig om arkitekturen [kan du skapa appen smarta mätare gratis](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Mer information om IoT Central finns i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/)
