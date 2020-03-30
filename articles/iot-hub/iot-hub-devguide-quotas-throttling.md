---
title: Förstå Azure IoT Hub-kvoter och begränsning | Microsoft-dokument
description: Utvecklarguide – beskrivning av de kvoter som gäller för IoT Hub och det förväntade begränsningsbeteendet.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284699"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referens - IoT Hub-kvoter och begränsning

I den här artikeln beskrivs kvoterna för en IoT Hub och information som hjälper dig att förstå hur begränsning fungerar.

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar

Varje Azure-prenumeration kan ha högst 50 IoT-hubbar och högst 1 ledigt nav.

Varje IoT-hubb etableras med ett visst antal enheter på en specifik nivå. Nivån och antalet enheter bestämmer den maximala dagliga kvoten för meddelanden som du kan skicka. Meddelandestorleken som används för att beräkna den dagliga kvoten är 0,5 KB för en kostnadsfri nivåhubb och 4KB för alla andra nivåer. Mer information finns i [Azure IoT Hub Pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

Nivån bestämmer också de begränsningsgränser som IoT Hub tillämpar på alla åtgärder.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

Under den offentliga förhandsversionen skickar IoT Plug and Play-enheter separata meddelanden per gränssnitt, vilket kan öka antalet meddelanden som räknas in i meddelandekvoten.

## <a name="operation-throttles"></a>Driftspjäll

Driftsbegränsningar är hastighetsbegränsningar som tillämpas i minutintervall och är avsedda att förhindra missbruk. De är också föremål för [trafik forma](#traffic-shaping).

I följande tabell visas de framtvingade gasreglagena. Värden refererar till ett enskilt nav.

| Begränsning | Gratis, B1 och S1 | B2 och S2 | B3 och S3 | 
| -------- | ------- | ------- | ------- |
| [Identitetsregisteråtgärder](#identity-registry-operations-throttle) (skapa, hämta, lista, uppdatera, ta bort) | 1.67/sec/unit (100/min/enhet) | 1.67/sec/unit (100/min/enhet) | 83.33/sec/unit (5 000/min/enhet) |
| [Nya enhetsanslutningar](#device-connections-throttle) (den här gränsen gäller för hastigheten på _nya anslutningar_, inte det totala antalet anslutningar) | Högre än 100/sek eller 12/sek/enhet <br/> Till exempel är två S1-enheter 2\*12 = 24 nya anslutningar/sek, men du har minst 100 nya anslutningar/sek över dina enheter. Med nio S1-enheter har du 108 nya anslutningar/sek (9\*12) över dina enheter. | 120 nya anslutningar/sek/enhet | 6 000 nya anslutningar/sek/enhet |
| Sändningar enhet-till-moln | Högre av 100 skicka operationer/sek eller 12 skicka operationer/sek/enhet <br/> Till exempel är två S1-enheter 2\*12 = 24/sek, men du har minst 100 skicka operationer/sek över dina enheter. Med nio S1-enheter har du 108 skicka\*operationer/sek (9 12) över dina enheter. | 120 skicka operationer/sek/enhet | 6 000 skickar operationer/sek/enhet |
| Cloud-to-device skickar<sup>1</sup> | 1.67 skicka drift/sek/enhet (100 meddelanden/min/enhet) | 1.67 skicka drift/sek/enhet (100 skicka operationer/min/enhet) | 83.33 skicka drift/sek/enhet (5 000 skickar drift/min/enhet) |
| Cloud-to-device tar emot<sup>1</sup> <br/> (endast när enheten använder HTTPS)| 16.67 ta emot operationer/sek/enhet (1 000 erhåller drift/min/enhet) | 16.67 ta emot operationer/sek/enhet (1 000 erhåller drift/min/enhet) | 833.33 ta emot drift/sek/enhet (50 000 erhåller drift/min/enhet) |
| Ladda upp filer | 1,67 filuppladdning initieringar/sek/enhet (100/min/enhet) | 1,67 filuppladdning initieringar/sek/enhet (100/min/enhet) | 83.33 filuppladdningsinitieringar/sek/enhet (5 000/min/enhet) |
| Direkta metoder<sup>1</sup> | 160KB/sek/enhet<sup>2</sup> | 480KB/sek/enhet<sup>2</sup> | 24 MB/sek/enhet<sup>2</sup> | 
| Frågor | 20/min/enhet | 20/min/enhet | 1 000/min/enhet |
| Twin (enhet och modul) läser<sup>1</sup> | 100/sek | Högre än 100/sek eller 10/sek/enhet | 500/sek/enhet |
| Dubbla uppdateringar (enhet och modul)<sup>1</sup> | 50/sek | Högre än 50/sek eller 5/sek/enhet | 250/sek/enhet |
| <sup>Jobboperationer 1</sup> <br/> (skapa, uppdatera, visa, ta bort) | 1.67/sec/unit (100/min/enhet) | 1.67/sec/unit (100/min/enhet) | 83.33/sec/unit (5 000/min/enhet) |
| Jobb enhetsoperationer<sup>1</sup> <br/> (uppdatera tvilling, anropa direkt metod) | 10/sek | Högre än 10/sek eller 1/sek/enhet | 50/sek/enhet |
| Konfigurationer och kantdistributioner<sup>1</sup> <br/> (skapa, uppdatera, visa, ta bort) | 0,33/sek/enhet (20/min/enhet) | 0,33/sek/enhet (20/min/enhet) | 0,33/sek/enhet (20/min/enhet) |
| Initieringshastighet för enhetsström<sup>1</sup> | 5 nya strömmar/sek | 5 nya strömmar/sek | 5 nya strömmar/sek |
| Maximalt antal samtidiga anslutna enhetsströmmar<sup>1</sup> | 50 | 50 | 50 |
| Maximal dataöverföring av enhetsström<sup>1</sup> (sammanlagd volym per dag) | 300 MB | 300 MB | 300 MB |

<sup>1.</sup> Den här funktionen är inte tillgänglig på den grundläggande nivån för IoT Hub. Mer information finns i [Så här väljer du rätt IoT Hub](iot-hub-scaling.md). <br/><sup>2.</sup> Begränsningsmätarens storlek är 4 KB.

### <a name="throttling-details"></a>Begränsa information

* Mätarstorleken avgör i vilka steg begränsningsgränsen förbrukas. Om direktsamtalets nyttolast är mellan 0 och 4 kB räknas den som 4 kB. Du kan ringa upp till 40 samtal per sekund per enhet innan du når gränsen på 160 KB/sek/enhet.

   På samma sätt, om din nyttolast är mellan 4 KB och 8 KB, varje samtal står för 8 KB och du kan göra upp till 20 samtal per sekund per enhet innan du träffar maxgränsen.

   Slutligen, om din nyttolaststorlek är mellan 156KB och 160 KB, kan du bara ringa 1 samtal per sekund per enhet i navet innan du når gränsen på 160 KB/sek/enhet.

*  För *jobbenhetsåtgärder (uppdatera tvilling, anropa direkt metod)* för nivå S2 gäller 50/sec/unit endast när du anropar metoder med jobb. Om du anropar direktmetoder direkt gäller den ursprungliga begränsningsgränsen på 24 MB/sek/enhet (för S2).

*  **Kvot** är det sammanlagda antalet meddelanden som du kan skicka i hubben *per dag*. Du hittar hubbens kvotgräns under kolumnen **Totalt antal meddelanden/dag** på [prissidan för IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Dina moln-till-enhet och enhet-till-moln-begränsningar bestämmer den maximala *hastigheten* med vilken du kan skicka meddelanden – antal meddelanden oavsett 4 KB-segment. Varje meddelande kan vara upp till 256 KB vilket är den [maximala meddelandestorleken](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Det är en bra idé att begränsa dina samtal så att du inte träffar / överskrider begränsningsgränserna. Om du når gränsen svarar IoT Hub med felkod 429 och klienten ska backa och försöka igen. Dessa gränser är per hubb (eller i vissa fall per nav/enhet). Mer information finns i [Hantera anslutning och tillförlitliga meddelande-/återförsöksmönster](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Trafikformning

För att hantera burst-trafik accepterar IoT Hub begäranden ovanför gasreglaget under en begränsad tid. De första av dessa begäranden behandlas omedelbart. Men om antalet begäranden fortsätter att bryta mot begränsningen börjar IoT Hub placera begäranden i en kö och bearbetas med gränsvärdet. Denna effekt kallas *trafikformning*. Dessutom är köns storlek begränsad. Om begränsningsfelet fortsätter fylls kön så småningom och IoT `429 ThrottlingException`Hub börjar avvisa begäranden med .

Du kan till exempel använda en simulerad enhet för att skicka 200 meddelanden från enhet till moln per sekund till din S1 IoT Hub (som har en gräns på 100/sek D2C-meddelanden). För den första minuten eller två bearbetas meddelandena omedelbart. Men eftersom enheten fortsätter att skicka fler meddelanden än begränsningsgränsen börjar IoT Hub att bara bearbeta 100 meddelanden per sekund och placerar resten i en kö. Du börjar märka ökad latens. Så småningom börjar `429 ThrottlingException` du komma när kön fylls upp och "antalet begränsningsfel" i [IoT Hub mått](iot-hub-metrics.md) börjar öka.

### <a name="identity-registry-operations-throttle"></a>Begränsning av identitetsregisteråtgärder

Registeråtgärder för enhetsidentitet är avsedda för körning i scenarier för enhetshantering och etablering. Läsa eller uppdatera ett stort antal enhetsidentiteter stöds genom [import- och exportjobb](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Begränsning av enhetsanslutningar

Begränsningen *för enhetsanslutningar* styr hur långt nya enhetsanslutningar kan upprättas med en IoT-hubb. *Enhetsanslutningsbegränsningen* styr inte det maximala antalet enheter som är anslutna samtidigt. Begränsningen *för enhetsanslutningar* beror på antalet enheter som har etablerats för IoT-hubben.

Om du till exempel köper en S1-enhet får du en begränsning på 100 anslutningar per sekund. För att ansluta 100 000 enheter tar det därför minst 1 000 sekunder (cirka 16 minuter). Du kan dock ha lika många samtidigt anslutna enheter som du har enheter registrerade i identitetsregistret.

## <a name="other-limits"></a>Andra gränser

IoT Hub tillämpar andra driftsgränser:

| Åtgärd | Gräns |
| --------- | ----- |
| Enheter | Det totala antalet enheter plus moduler som kan registreras till en enda IoT-hubb är begränsad till 1 000 000. Det enda sättet att öka den här gränsen är att kontakta [Microsoft Support](https://azure.microsoft.com/support/options/).|
| Filuppladdningar | 10 samtidiga filuppladdningar per enhet. |
| Jobb<sup>1</sup> | Maximala samtidiga jobb är 1 (gratis och S1), 5 (för S2) och 10 (för S3). Max samtidiga [enhetsimport-/exportjobb](iot-hub-bulk-identity-mgmt.md) är dock 1 för alla nivåer. <br/>Jobbhistoriken behålls i upp till 30 dagar. |
| Ytterligare slutpunkter | Betalda SKU-hubbar kan ha ytterligare 10 slutpunkter. Kostnadsfria SKU-hubbar kan ha ytterligare en slutpunkt. |
| Frågor om meddelanderoutning | Betalda SKU-hubbar kan ha 100 routningsfrågor. Kostnadsfria SKU-hubbar kan ha fem routningsfrågor. |
| Meddelandeberikanden | Betalda SKU-hubbar kan ha upp till 10 meddelandeberikade. Gratis SKU-hubbar kan ha upp till 2 meddelandeberikade.|
| Meddelanden från enhet till moln | Maximal meddelandestorlek 256 KB |
| Meddelanden från molnet till enheten<sup>1</sup> | Maximal meddelandestorlek 64 KB. Maximalt väntande meddelanden för leverans är 50 per enhet. |
| Direkt metod<sup>1</sup> | Maximal direkt metod nyttolaststorlek är 128 KB. |
| Automatisk enhets- och modulkonfigurationer<sup>1</sup> | 100 konfigurationer per betald SKU-hubb. 20 konfigurationer per gratis SKU-hubb. |
| Automatiska distributioner i ioT Edge<sup>1</sup> | 50 moduler per distribution. 100 distributioner (inklusive distributioner i lager) per betald SKU-hubb. 10 distributioner per gratis SKU-hubb. |
| Tvillingar<sup>1</sup> | Maximal storlek på önskade egenskaper och rapporterade egenskaper avsnitt är 32 KB vardera. Den maximala storleken på taggarna är 8 kB. |
| Principer för delad åtkomst | Maximalt antal principer för delad åtkomst är 16 |

<sup>1.</sup> Den här funktionen är inte tillgänglig på den grundläggande nivån för IoT Hub. Mer information finns i [Så här väljer du rätt IoT Hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Öka kvot- eller begränsningsgränsen

När som helst kan du öka kvoter eller begränsningsgränser genom [att öka antalet etablerade enheter i en IoT-hubb](iot-hub-upgrade.md).

## <a name="latency"></a>Svarstid

IoT Hub strävar efter att ge låg latens för alla åtgärder. På grund av nätverksförhållanden och andra oförutsägbara faktorer kan det dock inte garantera en viss svarstid. När du utformar din lösning bör du:

* Undvik att göra några antaganden om den maximala svarstiden för en IoT Hub-åtgärd.
* Etablera din IoT-hubb i Azure-regionen närmast dina enheter.
* Överväg att använda Azure IoT Edge för att utföra latenskänsliga åtgärder på enheten eller på en gateway nära enheten.

Flera IoT Hub-enheter påverkar begränsningen som beskrivits tidigare, men ger inga ytterligare svarstidsfördelar eller garantier.

Om du ser oväntade ökningar av driftsvarstiden kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

En djupgående diskussion om begränsning av IoT Hub-begränsning finns i blogginlägget [IoT Hub-begränsning och du](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Andra referensavsnitt i den här utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
