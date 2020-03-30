---
title: Felsökning av Azure IoT Hub-fel 403004 DeviceMaximumQueueDepexceed
description: Förstå hur du åtgärdar fel 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497491"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

I den här artikeln beskrivs orsakerna och lösningarna för **403004 DeviceMaximumQueueDepthExceed-fel.**

## <a name="symptoms"></a>Symtom

NÃ¤tt sÃ¤tt fÃ¤ndes fÃ¤ndes pÃ¤ sÃ¤nder en gÃ¤s Ã¤ngning av ett meddelande från molnet till enheten misslyckas begäran med felet **403004** eller **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Orsak

Den underliggande orsaken är att antalet meddelanden som gäller för enheten överskrider [kögränsen (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Den troligaste orsaken till att du stöter på den här gränsen är att du `ReceiveAsync`använder HTTPS för att ta emot meddelandet, vilket leder till kontinuerlig avsökning med , vilket resulterar i att IoT Hub stryper begäran.

## <a name="solution"></a>Lösning

Mönstret som stöds för meddelanden från molnet till enheten med HTTPS är periodvis anslutna enheter som söker efter meddelanden sällan (mindre än var 25:e minut). Om du vill minska risken för att du kör in i kögränsen växlar du till AMQP eller MQTT för meddelanden från molnet till enheten.

Du kan också förbättra logiken på enhetens sida för att snabbt slutföra, avvisa eller överge meddelanden i kö, förkorta tiden för att leva eller överväga att skicka färre meddelanden. Se [C2D-meddelandets TTL-värde](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Överväg slutligen att använda [API:et rensa kö](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) för att regelbundet rensa väntande meddelanden innan gränsen nås.