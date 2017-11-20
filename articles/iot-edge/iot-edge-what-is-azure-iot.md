---
title: "Azure-lösningar för Sakernas Internet (IoT Edge) | Microsoft Docs"
description: "Översikt över ett exempel på IoT-lösningsarkitektur och hur den relaterar till enheter, Azure IoT Hub-tjänsten, Azure IoT-enhetens SDK:er, Azure IoT-tjänstens SDK:er och övriga Azure-tjänster."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Nästa steg

Azure IoT Edge är en Azure-tjänst som möjliggör analys och databehandling på gränsen. Med IoT Edge kan du köra dina enheter med behållarbaserad kod som innehåller antingen logik som hämtas direkt från Azure-tjänsterna du redan använder eller din egen lösningsspecifika kod. Det gör det möjligt för dina enheter att:

* Fungera som gatewayenheter, sammanställa och bearbeta data från flera lövenheter.
* Utföra avvikelseidentifiering och reagera på förändringar i miljön utan att behöva vänta på instruktioner från molnet.
* Minimera kostnader för bandbredd och lagring genom att förbearbeta data och skicka resultaten. 

IoT Edge innehåller också ett molngränssnitt som möjliggör fjärrhantering av enheter. Du kan distribuera kod, övervaka status och uppdatera den utan att behöva fysisk åtkomst till dina enheter. Du kan via fjärranslutning hantera enskilda enheter eller skapa distributioner som påverkar stora mängder enheter som du definierar. Mer information finns i [Understand IoT Edge deployments for single devices or at scale][lnk-deployment] (Förstå IoT Edge-distributioner för enskilda enheter eller enheter i stor skala).

Läs om komponenter som aktiverar IoT Edge i [How Azure IoT Edge works][lnk-overview] (Så här fungerar Azure IoT Edge).

Om du inte har använt Azure IoT Hub tidigare kan det vara bra att börja med en [Översikt över tjänsten Azure IoT Hub][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
