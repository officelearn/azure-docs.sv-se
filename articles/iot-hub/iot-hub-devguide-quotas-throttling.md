---
title: Förstå Azure IoT Hub-kvoter och begränsning | Microsoft Docs
description: Utvecklarhandbok - beskrivning av kvoter som gäller för IoT-hubb och bandbreddsbegränsning förväntat beteende.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 37d1397b0601e09b12c0c05ff0adc6a916d66d70
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referens - IoT-hubb kvoter och begränsning

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar
Varje Azure-prenumeration kan ha högst 10 IoT-hubbar och högst 1 ledigt hubb.

Varje IoT-hubben har etablerats med ett visst antal enheter i en specifik nivå. Mer information finns i [priser för Azure IoT-hubb][lnk-pricing]. Fastställa dagliga maxkvoten av meddelanden som du kan skicka nivå och antalet enheter.

Nivån avgör också bandbreddsbegränsning gränserna som IoT-hubb tillämpar på alla åtgärder.

## <a name="operation-throttles"></a>Åtgärden begränsningar
Åtgärden begränsningar har begränsningar för hastighet som används i minuters intervall och är avsedda att förhindra missbruk. IoT-hubben försöker att undvika att fel när det är möjligt, men startar returnerar undantag om begränsningen överskrids för länge.

Samtidigt, kan du öka kvoter eller begränsning gränser genom att öka antalet etablerade enheter i en IoT-hubb.

I följande tabell visas de tvingande begränsas. Värden finns i en enskild hubb.

| Begränsning | Kostnadsfri B1 och S1 | B2 och S2 | B3 och S3 | 
| -------- | ------- | ------- | ------- |
| Identitet registret åtgärder (skapa, hämta, visa, uppdatera, ta bort) | 1.67/sec/Unit (unit-100/min) | 1.67/sec/Unit (unit-100/min) | 83.33/sec/Unit (unit-5000/min) |
| Enhetsanslutningar | Högre 100 per sekund eller enhet-12/sek <br/> Till exempel två S1 enheter är 2\*12 = 24/sek, men du har minst 100 per sekund på dina enheter. Med nio S1 enheter, har du 108 per sekund (9\*12) över dina enheter. | 120/sek/enhet | 6000/sek/enhet |
| Sändningar enhet-till-moln | Högre 100 per sekund eller enhet-12/sek <br/> Till exempel två S1 enheter är 2\*12 = 24/sek, men du har minst 100 per sekund på dina enheter. Med nio S1 enheter, har du 108 per sekund (9\*12) över dina enheter. | 120/sek/enhet | 6000/sek/enhet |
| Moln till enhet skickar<sup>1</sup> | 1.67/sec/Unit (unit-100/min) | 1.67/sec/Unit (unit-100/min) | 83.33/sec/Unit (unit-5000/min) |
| Moln till enhet tar emot<sup>1</sup> <br/> (endast när enheten använder HTTPS)| 16.67/sec/Unit (1000/min/unit) | 16.67/sec/Unit (1000/min/unit) | 833.33/sec/Unit (unit-50000/min) |
| Filuppladdning | 1.67 filen överför meddelanden/sek/enhet (unit/100/min) | 1.67 filen överför meddelanden/sek/enhet (unit/100/min) | 83.33 filen överför meddelanden/sek/enhet (unit-5000/min) |
| Dirigera metoder<sup>1</sup> | Enhet-160KB/sek<sup>2</sup> | 480KB/sek/enhet<sup>2</sup> | 24MB/sek/enhet<sup>2</sup> | 
| Dubbla (enheten och modulen) läser<sup>1</sup> | 10 per sekund | Högre 10 per sekund eller 1/sek/enhet | 50/sek/enhet |
| Dubbla uppdateringar (enheten och modulen)<sup>1</sup> | 10 per sekund | Högre 10 per sekund eller 1/sek/enhet | 50/sek/enhet |
| Jobb operations<sup>1</sup> <br/> (skapa, uppdatera, visa, ta bort) | 1.67/sec/Unit (unit-100/min) | 1.67/sec/Unit (unit-100/min) | 83.33/sec/Unit (unit-5000/min) |
| Jobb per enhet åtgärden genomströmning<sup>1</sup> | 10 per sekund | Högre 10 per sekund eller 1/sek/enhet | 50/sek/enhet |

<sup>1</sup>den här funktionen är inte tillgänglig i den grundläggande nivån av IoT-hubb. Mer information finns i [hur du väljer rätt IoT-hubben](iot-hub-scaling.md). <br/><sup>2</sup>begränsning mätaren storlek är 8 KB.

Den *enhetsanslutningar* begränsning styr den hastighet som den nya enhetsanslutningar kan upprättas med en IoT-hubb. Den *enhetsanslutningar* begränsning inte styr det maximala antalet samtidigt anslutna enheter. Begränsningen beror på antalet enheter som tillhandahålls för IoT-hubb.

Till exempel om du köper en S1 enhet kan få du en begränsning av 100 anslutningar per sekund. Det kan därför tar minst 1 000 sekunder (cirka 16 minuter) för att ansluta 100 000 enheter. Du kan dock ha så många samtidigt anslutna enheter som du har enheter som har registrerats i identitetsregistret.

En detaljerad beskrivning av IoT-hubb begränsning beteende, finns i bloggposten [IoT-hubb begränsning och du][lnk-throttle-blog].

> [!IMPORTANT]
> Identitet registret operations är avsedda för körning i hantering av enheter och etablerar scenarier. Läsning eller uppdatering av ett stort antal enheter identiteter stöds via [importera och exportera jobben][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Övriga begränsningar

IoT-hubb tillämpar andra begränsningar:

| Åtgärd | Gräns |
| --------- | ----- |
| Ladda upp filen URI: er | 10000 SAS URI: er kan vara ut för ett lagringskonto i taget. <br/> 10 SAS URI:er/enheten kan vara ute vid ett och samma tillfälle. |
| Jobb<sup>1</sup> | Jobbhistorik sparas upp till 30 dagar <br/> Maximal samtidiga jobb är 1 (för lediga) och S1 5 (för S2), 10 (S3). |
| Ytterligare slutpunkter | Betald SKU har NAV 10 ytterligare slutpunkter. Ledigt SKU-hubbar kanske en ytterligare slutpunkt. |
| Regler för routning av meddelanden | Betald SKU har NAV 100 routningsregler. Ledigt SKU-hubbar kan ha fem regler för routning. |
| Meddelanden enhet till moln | Maximal meddelandestorlek på 256 KB |
| Moln till enhet messaging<sup>1</sup> | Maximal meddelandestorlek 64 KB. Maximalt antal väntande meddelanden för leverans är 50. |
| Direkt metod<sup>1</sup> | Maximal direkta metoden nyttolastens storlek är 128 KB. |

<sup>1</sup>den här funktionen är inte tillgänglig i den grundläggande nivån av IoT-hubb. Mer information finns i [hur du väljer rätt IoT-hubben](iot-hub-scaling.md).

> [!NOTE]
> Det maximala antalet enheter som du kan ansluta till en enda IoT-hubben är för närvarande 500 000. Om du vill höja denna gräns Kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Svarstid
IoT-hubb strävar efter att tillhandahålla låg latens för alla åtgärder. På grund av nätverksproblem och andra oväntade faktorer kan inte den garantera en maximal fördröjning. När du utformar din lösning bör du:

* Undvik att göra några antaganden om den maximala svarstiden för alla IoT-hubb-åtgärder.
* Etablera din IoT-hubb i Azure-region närmast dina enheter.
* Överväg att använda Azure IoT kant utföra känslig för fördröjningar åtgärder på enheten eller på en gateway nära enheten.

Flera IoT Hub-enheter påverkar begränsning som beskrivits ovan, men ger inte några ytterligare fördröjning fördelar eller garantier.
Om du ser oväntat ökar i åtgärden latens Kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg
Andra referensavsnitten i den här IoT-hubb Utvecklarhandbok inkluderar:

* [IoT-hubbslutpunkter][lnk-devguide-endpoints]
* [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning][lnk-devguide-query]
* [IoT-hubb MQTT stöd][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
