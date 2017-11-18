---
title: Azure IoT-hubb skalning | Microsoft Docs
description: "Så här skalar du din IoT-hubb för att stödja din förväntade genomströmningen. Innehåller en sammanfattning av dataflödet som stöds för varje nivå och alternativ för delning."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00043293eb57768f0117e912bb67f02d088934f3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="scale-your-iot-hub-solution"></a>Skala din lösning för IoT-hubb
Azure IoT-hubb har stöd för upp till en miljon samtidigt anslutna enheter. Mer information finns i [IoT-hubb priser][lnk-pricing]. Varje IoT Hub-enhet kan ett visst antal dagliga meddelanden.

Överväg att viss användningen av IoT-hubb för att skala din lösning korrekt. I synnerhet Tänk krävs belastning genomströmning för följande typer av åtgärder:

* Meddelanden från enheten till molnet
* Meddelanden moln till enhet
* Identitetsregisteråtgärder

Förutom informationen genomströmning Se [IoT-hubb kvoter och begränsningar] [ IoT Hub quotas and throttles] och därefter utforma din lösning.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Enhet till moln och moln till enhet genomströmningen
Det bästa sättet att ändra storlek på en IoT-hubb-lösning är att utvärdera trafiken på grundval av per enhet.

Meddelanden från enhet till moln följer dessa riktlinjer för varaktigt dataflöde.

| Nivå | Varaktigt dataflöde | Varaktiga överföringshastighet |
| --- | --- | --- |
| S1 |Upp till 1 111 KB per minut per enhet<br/>(1,5 GB/dag/unit) |Medelvärde för 278 meddelanden per minut per enhet<br/>(400 000 meddelanden/dag per enhet) |
| S2 |Upp till 16 MB per minut per enhet<br/>(22,8 GB/dag/unit) |Medelvärde för 4,167 meddelanden per minut per enhet<br/>(6 miljoner meddelanden/dag per enhet) |
| S3 |Upp till 814 MB per minut per enhet<br/>(1144.4 GB/dag/unit) |Medelvärde för 208,333 meddelanden per minut per enhet<br/>(300 miljoner meddelanden/dag per enhet) |

## <a name="identity-registry-operation-throughput"></a>Identitet registret åtgärden genomflöde
IoT-hubb identitet registret åtgärder är inte ska vara körning åtgärder, som de främst är relaterade till enhetsetableringen.

Specifika burst prestanda siffror finns [IoT-hubb kvoter och begränsningar][IoT Hub quotas and throttles].

## <a name="sharding"></a>Horisontell partitionering
När en enda IoT-hubb kan skalas för miljoner enheter, kräver ibland din lösning specifika prestandaegenskaper som en enda IoT-hubb inte kan garantera. I så fall bör du partitionera dina enheter till flera IoT-hubbar. Flera IoT-hubbar Utjämna trafiken belastning och hämta nödvändiga genomströmning eller åtgärden priser som krävs.

## <a name="next-steps"></a>Nästa steg
Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
