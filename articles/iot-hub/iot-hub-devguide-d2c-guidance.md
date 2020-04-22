---
title: Azure IoT Hub-alternativ för enhet till moln | Microsoft-dokument
description: Utvecklarguide – vägledning om när du ska använda meddelanden från enhet till moln, rapporterade egenskaper eller filöverföring för kommunikation mellan molnet och enheten.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b984ee7ed662bf089e7bcb6fc7e948fb61ed1209
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733217"
---
# <a name="device-to-cloud-communications-guidance"></a>Kommunikationsvägledning för enhet till moln

När du skickar information från enhetsappen till lösningens baksida exponerar IoT Hub tre alternativ:

* [Enhet-till-moln-meddelanden](iot-hub-devguide-messages-d2c.md) för tidsserietelemetri och aviseringar.

* [Enhetstvillingens rapporterade egenskaper](iot-hub-devguide-device-twins.md) för rapportering av enhetstillståndsinformation, till exempel tillgängliga funktioner, villkor eller tillståndet för tidskrävande arbetsflöden. Till exempel konfigurations- och programuppdateringar.

* [Filuppladdningar](iot-hub-devguide-file-upload.md) för mediefiler och stora telemetribatchar som laddas upp av periodvis anslutna enheter eller komprimeras för att spara bandbredd.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Här är en detaljerad jämförelse av de olika alternativen för kommunikation mellan enheter och moln.

|  | Meddelanden från enheten till molnet | Enhetstvillingens rapporterade egenskaper | Filuppladdningar |
| ---- | ------- | ---------- | ---- |
| Scenario | Telemetritidsserier och varningar. Till exempel skickade 256 KB-sensordatabatchar var femte minut. | Tillgängliga funktioner och villkor. Det aktuella enhetsanslutningsläget, till exempel mobilnät eller WiFi. Synkronisera tidskrävande arbetsflöden, till exempel konfiguration och programuppdateringar. | Mediefiler. Stora (vanligtvis komprimerade) telemetribatchar. |
| Lagring och hämtning | Lagras tillfälligt av IoT Hub, upp till 7 dagar. Endast sekventiell läsning. | Lagras av IoT Hub i enhetstvillingen. Kan hämtas med [frågespråket IoT Hub](iot-hub-devguide-query-language.md). | Lagras i Azure Storage-konto som tillhandahålls av användaren. |
| Storlek | Upp till 256 KB-meddelanden. | Maximal rapporterad egenskapsstorlek är 32 KB. | Maximal filstorlek som stöds av Azure Blob Storage. |
| Frequency | Hög. Mer information finns i [IoT Hub-gränser](iot-hub-devguide-quotas-throttling.md). | Medel. Mer information finns i [IoT Hub-gränser](iot-hub-devguide-quotas-throttling.md). | Låg. Mer information finns i [IoT Hub-gränser](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Finns på alla protokoll. | Tillgänglig med MQTT eller AMQP. | Tillgänglig när du använder något protokoll, men kräver HTTPS på enheten. |

Ett program kan behöva skicka information både som en telemetritidsserie eller varning och göra den tillgänglig i enhetstvillingen. I det här fallet kan du välja något av följande alternativ:

* Enhetsappen skickar ett meddelande från enhet till moln och rapporterar en egenskapsändring.
* Lösningens serverdel kan lagra informationen i enhetstvillingens taggar när den tar emot meddelandet.

Eftersom meddelanden mellan enheter och moln möjliggör ett mycket högre dataflöde än enhetstvillinguppdateringar är det ibland önskvärt att undvika att uppdatera enhetstvillingen för varje meddelande mellan enheter och moln.
