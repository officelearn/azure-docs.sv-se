---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224449"
---
I följande tabell visas de gränser som är associerade med de olika tjänstnivåerna S1, S2, S3 och F1. Information om kostnaden för varje *enhet* på varje nivå finns i [Azure IoT Hub-priser](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resurs | S1 Standard | S2 Standard | S3 Standard | F1 Kostnadsfri |
| --- | --- | --- | --- | --- |
| Meddelanden per dag |400,000 |6,000,000 |300,000,000 |8,000 |
| Maximalt antal enheter |200 |200 |10 |1 |

> [!NOTE]
> Om du räknar med att använda fler än 200 enheter med en S1- eller S2-nivånav eller 10 enheter med en S3-nivåhubb kontaktar du Microsoft Support.
> 
> 

I följande tabell visas de gränser som gäller för IoT Hub-resurser.

| Resurs | Gräns |
| --- | --- |
| Maximalt antal betalda IoT-hubbar per Azure-prenumeration |100 |
| Maximalt antal kostnadsfria IoT-hubbar per Azure-prenumeration |1 |
| Maximalt antal tecken i ett enhets-ID | 128 |
| Maximalt antal enhetsidentiteter<br/> som returneras i ett enskilt anrop |1,000 |
| Maximal kvarhållning av IoT Hub-meddelanden för enhet-till-moln-meddelanden |7 dagar |
| Maximal storlek för enhet-till-moln-meddelande |256 kB |
| Maximal storlek för enhet-till-moln-batch |AMQP och HTTP: 256 KB för hela batchen <br/>MQTT: 256 KB för varje meddelande |
| Maximalt antal meddelanden för enhet-till-moln-batch |500 |
| Maximal storlek för moln-till-enhet-meddelande |64 kB |
| Maximalt TTL-värde för moln-till-enhet-meddelanden |2 dagar |
| Maximalt antal leveranser för moln-till-enhet- <br/> meddelanden |100 |
| Maximalt ködjup mellan molnet och enheten per enhet |50 |
| Maximalt antal leveranser för feedbackmeddelanden <br/> som svar på ett moln-till-enhet-meddelande |100 |
| Maximalt TTL-värde för meddelanden som <br/> svar på ett moln-till-enhet-meddelande |2 dagar |
| [Maximal storlek för enhetstvilling](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB för taggar avsnitt, och 32 KB för önskade och rapporterade egenskaper avsnitt varje |
| Maximal längd på enhetens dubbla strängnyckel | 1 kB |
| Maximal längd på enhetens dubbla strängvärde | 4 kB |
| [Maximalt djup för objekt i enhetstvilling](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maximal storlek på nyttolast för direkt metod | 128 kB |
| Maximal kvarhållning för jobbhistorik | 30 dagar |
| Maximalt antal samtidiga jobb | 10 (för S3), 5 (för S2), 1 (för S1) |
| Maximalt antal ytterligare slutpunkter | 10 (för S1, S2 och S3) |
| Maximalt antal regler för meddelandedirigering | 100 (för S1, S2 och S3) |
| Maximalt antal samtidiga anslutna enhetsströmmar | 50 (endast för S1, S2, S3 och F1) |
| Maximal dataöverföring för enhetsström | 300 MB per dag (endast för S1, S2, S3 och F1) |

> [!NOTE]
> Om du behöver mer än 100 betalda IoT-hubbar i en Azure-prenumeration kontaktar du Microsoft Support.

> [!NOTE]
> För närvarande är det totala antalet enheter plus moduler som kan registreras till en enda IoT-hubb begränsad till 1 000 000. Om du vill öka den här gränsen kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

IoT Hub begränsar begäranden när följande kvoter överskrids.

| Begränsning | Värde per hubb |
| --- | --- |
| Identitetsregisteråtgärder <br/> (skapa, hämta, lista, uppdatera och ta bort), <br/> enskilda eller massimport/massexport |83.33/sec/unit (5 000/min/unit) (för S3). <br/> 1,67/sek/enhet (100/min/enhet) (för S1 och S2). |
| Enhetsanslutningar |6 000/sek/enhet (för S3), 120/sek/enhet (för S2), 12/sec/unit (för S1). <br/>Minst 100 per sekund. |
| Sändningar enhet-till-moln |6 000/sek/enhet (för S3), 120/sek/enhet (för S2), 12/sec/unit (för S1). <br/>Minst 100 per sekund. |
| Sändningar moln-till-enhet | 83.33/sec/unit (5 000/min/unit) (för S3), 1,67/sek/enhet (100/min/enhet) (för S1 och S2). |
| Mottagningar moln-till-enhet |833.33/sec/unit (50,000/min/unit) (för S3), 16.67/sec/unit (1 000/min/unit) (för S1 och S2). |
| Filöverföringsåtgärder |83.33 filuppladdningsinitieringar/sek/enhet (5 000/min/enhet) (för S3), 1,67 filuppladdningsinitieringar/sek/enhet (100/min/enhet) (för S1 och S2). <br/> 10 000 SAS-urier kan vara ute för ett Azure Storage-konto samtidigt.<br/> 10 SAS URI:er/enheten kan vara ute vid ett och samma tillfälle. |
| Direkta metoder | 24 MB/sek/enhet (för S3), 480 KB/sek/enhet (för S2), 160 KB/sek/enhet (för S1).<br/> Baserat på 8 KB begränsningsmätare storlek. |
| Läsoperationer för enhetstvilling | 500/sec/unit (för S3), Högst 100/sek eller 10/sek/enhet (för S2), 100/sek (för S1) |
| Uppdateringar för enhetstvilling | 250/sec/unit (för S3), Högst 50/sek eller 5/sek/enhet (för S2), 50/sek (för S1) |
| Jobbåtgärder <br/> (skapa, uppdatera, lista och ta bort) | 83.33/sec/unit (5 000/min/unit) (för S3), 1,67/sek/enhet (100/min/enhet) (för S2), 1,67/sek/enhet (100/min/enhet) (för S1). |
| Jobb per enhetsåtgärd, dataflöde | 50/sek/enhet (för S3), max 10/sek eller 1/sek/enhet (för S2), 10/sek (för S1). |
| Initieringshastighet för enhetsström | 5 nya strömmar/sek (endast för S1, S2, S3 och F1). |
