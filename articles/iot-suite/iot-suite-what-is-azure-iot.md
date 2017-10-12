---
title: "Azure-lösningar för Sakernas Internet (IoT Suite) | Microsoft Docs"
description: "En översikt över IoT på Azure, inklusive en exempellösningsarkitektur och hur den relaterar till Azure IoT Suite och de förkonfigurerade lösningarna."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 320190488bb4c7b8192421f9dd50a5264f558584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT Suite
Microsoft Azure IoT Suite är en lösning i företagsklass som hjälper dig att snabbt komma igång med hjälp av en samling utökningsbara, förkonfigurerade lösningar. Med dessa lösningar kan du hantera vanliga IoT-scenarier som [fjärrövervakning][lnk-preconfigured-solutions], [förebyggande underhåll][lnk-predictive-maintenance] och [ansluten fabrik][lnk-connected-factory]. Dessa lösningar är implementeringar av IoT-lösningsarkitekturen som beskrivs i den här artikeln.

De förkonfigurerade lösningarna är färdiga kompletta lösningar, som bland annat innehåller:

- Simulerade enheter som hjälper dig att komma igång.
- Förkonfigurerade Azure-tjänster som [Azure IoT Hub][Azure IoT Hub], [Azure Event Hubs][Azure Event Hubs], [Azure Stream Analytics][Azure Stream Analytics], [Azure Machine Learning][Azure Machine Learning] och [Azure Storage][Azure storage].
- Lösningsspecifika hanteringskonsoler.

De förkonfigurerade lösningarna innehåller beprövad, produktionsklar kod som du kan anpassa och utöka för att implementera dina egna specifika IoT-scenarier.

Du kanske också är intresserad av tjänsten [Azure IoT Hub][Azure IoT Hub] som används i många av de förkonfigurerade lösningarna. [Azure IoT Hub][Azure IoT Hub] tillhandahåller den säkra och tillförlitliga dubbelriktade kommunikationen mellan enheter och molnet som används i arkitekturen för de förkonfigurerade lösningarna.

## <a name="next-steps"></a>Nästa steg
Utforska dessa resurser för att lära dig mer om IoT Suite och de förkonfigurerade lösningarna:

* [Vad är Azure IoT Suite?][lnk-whatissuite]
* [Vad är förkonfigurerade lösningar i Azure IoT Suite?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md