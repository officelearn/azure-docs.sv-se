---
title: Alternativ för Azure IoT Hub-enhet till moln | Microsoft Docs
description: Utvecklarguide – riktlinjer för när du ska använda meddelanden från enheten till molnet, rapporterade egenskaper eller ladda upp filer för meddelanden från moln till enhet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885524"
---
# <a name="device-to-cloud-communications-guidance"></a>Enhet-till-molnet kommunikation

Skicka information från app för enheter till lösningens backend-server, IoT Hub exponerar när tre alternativ:

* [Meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md) för time series telemetri och aviseringar.

* [Enhetstvillingen är rapporterade egenskaper](iot-hub-devguide-device-twins.md) för rapportering information om enhetstillstånd som tillgängliga funktioner, villkor eller tillståndet för långvariga arbetsflöden. Konfiguration och programuppdateringar.

* [Filen uppladdningar](iot-hub-devguide-file-upload.md) för medier filer och stora telemetri batchar har laddats upp av periodvis anslutna enheter eller komprimeras för att spara bandbredd.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Här är en detaljerad jämförelse av de olika kommunikationsalternativen för enhet-till-molnet.

|  | Meddelanden från enheten till molnet | Enhetstvillingens rapporterade egenskaper | Filöverföringar |
| ---- | ------- | ---------- | ---- |
| Scenario | Tidsserier för telemetri och aviseringar. Exempel: 256 KB sensor databatchar skickas var femte minut. | Tillgänglig kapacitet och villkor. Till exempel aktuell enhet anslutningsläget, till exempel mobilnät eller Wi-Fi. Synkronisera långvariga arbetsflöden, till exempel konfiguration och programuppdateringar. | Mediefiler. Stor (vanligtvis komprimerade) telemetri batchar. |
| Lagring och hämtning | Lagras temporärt i IoT Hub, upp till 7 dagar. Sekventiell läsning. | Lagras av IoT Hub i enhetstvillingen. Hämtningsbar med hjälp av den [IoT Hub-frågespråk](iot-hub-devguide-query-language.md). | Lagras i Azure Storage-konto för användaren. |
| Storlek | Upp till 256 KB meddelanden. | Maximal rapporterade egenskaper är 8 KB. | Maximal filstorlek som stöds av Azure Blob Storage. |
| Frekvens | Hög. Mer information finns i [IoT Hub begränsar](iot-hub-devguide-quotas-throttling.md). | Medel. Mer information finns i [IoT Hub begränsar](iot-hub-devguide-quotas-throttling.md). | Låg. Mer information finns i [IoT Hub begränsar](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Tillgänglig på alla protokoll. | Tillgängligt med hjälp av MQTT eller AMQP. | Tillgängliga när du använder alla protokoll, men kräver HTTPS på enheten. |

Ett program kan behöva skicka information om både som en telemetri tidsserier eller varning och gör den tillgänglig i enhetstvillingen. I det här scenariot kan du välja något av följande alternativ:

* Enhetsappen skickar ett meddelande på enheten till molnet och rapporterar en egenskap som har ändrats.
* Lösningens backend-server kan lagra informationen i den enhetstvilling-taggar när den tar emot meddelandet.

Eftersom meddelanden från enheten till molnet aktiverar en mycket högre genomströmning än uppdateringar för enhetstvilling kan vara det önskvärt att undvika att uppdatera enhetstvillingen för varje enhet till moln-meddelande.