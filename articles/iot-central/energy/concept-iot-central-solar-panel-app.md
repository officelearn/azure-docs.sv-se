---
title: Arkitektur koncept i Azure IoT Central-Energy | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör arkitekturen i Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77018014"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>App-arkitektur för Azure IoT Central-solpanel




Den här artikeln innehåller en översikt över mallen för att övervaka appar i sol panelen. Diagrammet nedan visar en arkitektur för solpanel-appen på Azure som använder IoT Central plattform.

> [!div class="mx-imgBorder"]
> ![arkitektur för smart mätare](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Den här arkitekturen består av följande komponenter. Vissa program kanske inte kräver alla komponenter som anges här.

## <a name="solar-panels-and-connectivity"></a>Sol paneler och anslutningar 

Sol-paneler är en av de betydande källorna till förnybar energi. Beroende på typ av sol panel och konfiguration kan du ansluta den antingen med hjälp av gatewayer eller andra mellanliggande enheter och patentskyddade system. Du kan behöva skapa IoT Central enhets brygga för att ansluta enheter som inte kan anslutas direkt. IoT Central Device Bridge är en lösning med öppen källkod och du hittar den fullständiga informationen [här](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 



## <a name="iot-central-platform"></a>IoT Central plattform
Azure IoT Central är en plattform som fören klar skapandet av IoT-lösningen och hjälper till att minska belastningen och kostnaderna för IoT-hantering, åtgärder och utveckling. Med IoT Central kan du enkelt ansluta, övervaka och hantera dina Sakernas Internet (IoT)-till gångar i stor skala. När du har anslutit dina solpaneler till IoT Central, använder app-mallen inbyggda funktioner, till exempel enhets modeller, kommandon och instrument paneler. App-mallen använder också IoT Central lagring för varma sökvägar, till exempel data övervakning, analys, regler och visualisering i nära real tid.


## <a name="extensibility-options-to-build-with-iot-central"></a>Utöknings alternativ för att bygga med IoT Central
IoT Centrals plattformen innehåller två alternativ för utöknings barhet: kontinuerlig data export (CDE) och API: er. Kunder och partner kan välja mellan de här alternativen för att anpassa sina lösningar efter speciella behov. Till exempel är en av våra partner konfigurerade CDE med Azure Data Lake Storage (ADLS). De använder ADLS för långsiktig datakvarhållning och andra lagrings scenarier för kall vägar, t. ex. batchbearbetning, granskning och rapportering. 

## <a name="next-steps"></a>Nästa steg

* Nu när du har lärt dig om arkitekturen kan du [skapa en solpanel-app utan kostnad](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Mer information om IoT Central finns i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/)