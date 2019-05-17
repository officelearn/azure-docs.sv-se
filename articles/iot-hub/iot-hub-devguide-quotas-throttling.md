---
title: Förstå Azure IoT Hub-kvoter och begränsningar | Microsoft Docs
description: Utvecklarguide – beskrivning av kvoter som gäller för IoT Hub och det förväntade beteendet.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2019
ms.openlocfilehash: ea9bea8b314d00db87ad7addacc49a976e0da08e
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550475"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referens – IoT Hub-kvoter och begränsningar

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar

Varje Azure-prenumeration kan ha högst 50 IoT-hubbar och högst 1 kostnadsfri hub.

Varje IoT-hubb etableras med ett visst antal enheter på en specifik nivå. Avgör maximal daglig kvot på meddelanden som du kan skicka nivå och antal enheter. Meddelandestorlek som används för att beräkna den dagliga kvoten är 0,5 KB för en kostnadsfri nivå-hubb och 4KB för alla andra nivåer. Mer information finns i [prisinformation för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

På nivån avgör också de begränsningar gränser som IoT Hub tillämpar på alla åtgärder.

## <a name="operation-throttles"></a>Åtgärden begränsningar

Åtgärden begränsningar är priset begränsningar som tillämpas i minuters intervall och är avsedda att förhindra missbruk. De också är föremål för [Trafikstyrning](#traffic-shaping).

I följande tabell visas de tvingande begränsningar. Värden finns i en enskild hubb.

| Begränsning | Kostnadsfri, B1 och S1 | B2 och S2 | B3 och S3 | 
| -------- | ------- | ------- | ------- |
| [Identitetsregisteråtgärder](#identity-registry-operations-throttle) (skapa, hämta, lista, uppdatera och ta bort) | 1.67/sec/Unit (100 per minut per enhet) | 1.67/sec/Unit (100 per minut per enhet) | 83.33/sec/Unit (5 000 per minut per enhet) |
| [Nya anslutningar på enheten](#device-connections-throttle) (den här begränsningen gäller för mängden _nya anslutningar_, inte det totala antalet anslutningar) | Högre av 100 per sekund eller 12 per sekund och enhet <br/> Till exempel två S1-enheter är 2\*12 = 24 nya anslutningar per sekund, men du har minst 100 nya anslutningar per sekund på dina enheter. Med nio S1-enheter har du 108 nya anslutningar per sekund (9\*12) mellan dina enheter. | 120 nya anslutningar per sekund och enhet | 6 000 nya anslutningar per sekund och enhet |
| Sändningar enhet-till-moln | Högre av 100 per sekund eller 12 per sekund och enhet <br/> Till exempel två S1-enheter är 2\*12 = 24/sek, men du har minst 100 per sekund på dina enheter. Med nio S1-enheter har du 108/sek (9\*12) mellan dina enheter. | 120 per sekund och enhet | 6 000 per sekund och enhet |
| Moln-till-enhet skickar<sup>1</sup> | 1.67/sec/Unit (100 per minut per enhet) | 1.67/sec/Unit (100 per minut per enhet) | 83.33/sec/Unit (5 000 per minut per enhet) |
| Moln-till-enhet tar emot<sup>1</sup> <br/> (endast när enheten använder HTTPS)| 16.67/sec/Unit (1 000 per minut per enhet) | 16.67/sec/Unit (1 000 per minut per enhet) | 833.33/sec/Unit (50 000 per minut per enhet) |
| Filuppladdning | 1.67 filen ladda upp meddelanden per sekund och enhet (100 per minut per enhet) | 1.67 filen ladda upp meddelanden per sekund och enhet (100 per minut per enhet) | 83.33 filen ladda upp meddelanden per sekund och enhet (5 000 per minut per enhet) |
| Direkta metoder<sup>1</sup> | 160KB per sekund och enhet<sup>2</sup> | 480KB per sekund och enhet<sup>2</sup> | 24MB per sekund och enhet<sup>2</sup> | 
| Frågor | 20 per minut per enhet | 20 per minut per enhet | 1 000 per minut per enhet |
| Twin (enhets- och modulen) läsningar<sup>1</sup> | 100 per sekund | Högre av 100 per sekund eller 10 per sekund och enhet | 500 per sekund och enhet |
| Twin uppdateringar (enhets- och modulen)<sup>1</sup> | 50 per sekund | Högre av 50 per sekund eller 5 per sekund och enhet | 250 per sekund och enhet |
| Jobb operations<sup>1</sup> <br/> (skapa, uppdatera, visa, ta bort) | 1.67/sec/Unit (100 per minut per enhet) | 1.67/sec/Unit (100 per minut per enhet) | 83.33/sec/Unit (5 000 per minut per enhet) |
| Jobb åtgärder<sup>1</sup> <br/> (uppdatera twin, anropa direkt metod) | 10 per sekund | Högre av 10 per sekund eller 1 per sekund och enhet | 50 per sekund och enhet |
| Konfigurationer och edge-distributioner<sup>1</sup> <br/> (skapa, uppdatera, visa, ta bort) | 0.33/sec/Unit (20 per minut per enhet) | 0.33/sec/Unit (20 per minut per enhet) | 0.33/sec/Unit (20 per minut per enhet) |
| Enheten initiation dataströmshastighet<sup>1</sup> | 5 nya dataströmmar/sek | 5 nya dataströmmar/sek | 5 nya dataströmmar/sek |
| Maximalt antal samtidigt ansluten enhet strömmar<sup>1</sup> | 50 | 50 | 50 |
| Maximal enhet stream dataöverföring<sup>1</sup> (aggregera volym per dag) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>den här funktionen är inte tillgänglig i basic-nivån för IoT Hub. Mer information finns i [hur du väljer rätt IoT-hubb](iot-hub-scaling.md). <br/><sup>2</sup>begränsning mätaren storleken är 4 KB.

### <a name="traffic-shaping"></a>Trafikstyrning

För att hantera burst-trafik, accepterar begäranden över begränsningen i IoT Hub under en begränsad tid. De första några av dessa begäranden bearbetas omedelbart. Men om antalet begäranden fortsätter bryter du mot begränsning, IoT Hub startar placerar begäranden i en kö och bearbetas priset som gräns. Detta kallas *Trafikstyrning*. Storleken på den här kön är dessutom begränsad. Om överträdelsen begränsning kvarstår så småningom kön fylls och IoT Hub börjar avvisar begäranden med `429 ThrottlingException`. 

Exempelvis kan använda du en simulerad enhet för att skicka 200 enhet till moln-meddelanden per sekund till din IoT Hub S1 (som har en gräns på 100 per sekund D2C skickar). För den första minuten eller två behandlas meddelandena omedelbart. Men eftersom enheten fortsätter att skicka fler meddelanden än begränsningen, IoT Hub börjar att endast bearbeta 100 meddelanden per sekund och placerar resten i en kö. Du märker ökad latens. Till slut kommer du få `429 ThrottlingException` som kön tar slut och ”antal begränsning fel” i [IoT-hubbens mått](iot-hub-metrics.md) startar ökar.

### <a name="identity-registry-operations-throttle"></a>Begränsa identitetsregisteråtgärder

Enheten identitetsregisteråtgärder är avsedda för körning av enheter och etablera scenarier. Läsa eller uppdatera ett stort antal enhetsidentiteter stöds via [importera och exportera jobb](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Begränsa enhetsanslutningar

Den *enhetsanslutningar* begränsning styr den hastighet som den nya enhetsanslutningar kan upprättas med en IoT-hubb. Den *enhetsanslutningar* reglerar inte begränsa det maximala antalet samtidigt anslutna enheter. Den *enhetsanslutningar* rate begränsning beror på hur många enheter som har etablerats för IoT-hubben.

Till exempel om du köper en enkel S1-enhet kan få du en begränsning av 100 anslutningar per sekund. Därför, om du vill ansluta 100, 000 enheter, det tar minst 1 000 sekunder (cirka 16 minuter). Du kan dock ha så många simultant kopplade enheter som du har enheter som är registrerade i din identitetsregister.


## <a name="other-limits"></a>Andra gränser

IoT Hub tillämpar andra begränsningar:

| Operation | Begränsning |
| --------- | ----- |
| Enheter | Det maximala antalet enheter som du kan ansluta till en enda IoT-hubb är 1 000 000. Det enda sättet att fler IP-adresser är att kontakta [Microsoft Support](https://azure.microsoft.com/support/options/).| 
| Ladda upp filen URI: er | 10 000 SAS URI: er kan vara ute för ett lagringskonto i taget. <br/> 10 SAS URI:er/enheten kan vara ute vid ett och samma tillfälle. |
| Jobb<sup>1</sup> | Maximalt antal samtidiga jobb är 1 (för kostnadsfri och S1), 5 (för S2) och 10 (för S3). Dock max samtidiga [enheten import/export-jobb](iot-hub-bulk-identity-mgmt.md) är 1 för alla nivåer. <br/>Jobbhistorik sparas upp till 30 dagar. |
| Ytterligare slutpunkter | Betalda SKU har-hubbar 10 ytterligare slutpunkter. Kostnadsfria SKU-hubs kan ha en ytterligare slutpunkt. |
| Regler för meddelandedirigering | Betalda SKU har-hubbar 100 routningsregler. Kostnadsfria SKU-hubs kan ha fem routningsregler. |
| Enhet-till-moln-meddelanden | Maximal meddelandestorlek på 256 KB |
| Moln-till-enhet-meddelanden<sup>1</sup> | Maximal meddelandestorlek 64 KB. Högsta antalet väntande meddelanden för leverans är 50. |
| Direkt metod<sup>1</sup> | Maximal direkt metod Nyttolaststorlek är 128 KB. |
| Automatisk enhetskonfigurationer<sup>1</sup> | 100 konfigurationer per hubb för betalda SKU: N. 20 konfigurationer per kostnadsfria SKU-hubb. |
| Automatisk Edge-distributioner<sup>1</sup> | 20 moduler per distribution. 100 distributioner per hubb för betalda SKU: N. 20 distributioner per kostnadsfria SKU-hubb. |
| Twins<sup>1</sup> | Maximal storlek per twin avsnittet (taggar, önskade egenskaper, rapporterade egenskaper) är 8 KB |

<sup>1</sup>den här funktionen är inte tillgänglig i basic-nivån för IoT Hub. Mer information finns i [hur du väljer rätt IoT-hubb](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Öka kvoten eller begränsa gränsen

Vid en given tidpunkt som du kan öka kvoter eller begränsa begränsningar av [öka antalet etablerade enheter i en IoT-hubb](iot-hub-upgrade.md).

## <a name="latency"></a>Svarstid

IoT Hub strävar efter att ge låg latens för alla åtgärder. Men på grund av nätverkets tillstånd och andra oförutsägbara faktorer kan inte garantera en viss fördröjning. När du utformar din lösning, bör du:

* Undvik att göra några antaganden om den maximala svarstiden för alla åtgärder som IoT Hub.
* Etablera din IoT-hubb i Azure-regionen närmast dina enheter.
* Överväg att använda Azure IoT Edge för att utföra latenskänsliga åtgärder på enheten eller på en gateway nära enheten.

Flera enheter för IoT Hub påverkar begränsningarna enligt beskrivningen ovan, men ger inte några ytterligare fördröjning fördelar eller garantier.

Om du ser oväntade ökningar av svarstiden för åtgärden, kontakta [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

En detaljerad beskrivning av IoT Hub begränsning, finns i bloggposten [IoT Hub begränsning och du](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Andra referensavsnitten i det här utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
