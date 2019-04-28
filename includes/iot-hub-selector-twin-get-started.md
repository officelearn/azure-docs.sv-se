---
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780528"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub lagrar en enhetstvilling för varje enhet som ansluter till den.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använda enhetstvillingar för att:

* Store enhetsmetadata från lösningens backend-servrar.
* Rapportera aktuell statusinformation, till exempel tillgänglig kapacitet och villkor (till exempel den anslutning metod som används) från din enhetsapp.
* Synkronisera tillståndet för långvariga arbetsflöden (till exempel uppdateringar av inbyggd programvara och konfiguration) mellan en enhetsapp och en backend-app.
* Fråga din enhetsmetadata, konfiguration eller tillstånd.

Enhetstvillingar är utformade för synkronisering och för att fråga enhetskonfigurationer och villkor. Mer information om när du ska använda enhetstvillingar finns i [förstå enhetstvillingar](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Enhetstvillingar lagras i en IoT-hubb och innehålla:

* *taggar*, enhetsmetadata som endast kan nås av lösningens serverdel;
* *önskade egenskaper*, JSON-objekt som kan ändras av lösningen tillbaka slutpunkt och övervakas av enhetsapp; och
* *rapporterade egenskaper*, JSON-objekt kan ändras av app för enheter och läsas av lösningens serverdel. Taggar och egenskaper får inte innehålla matriser, men kan vara kapslade objekt.

![Enheten twin bild som visar funktionerna](./media/iot-hub-selector-twin-get-started/twin.png)

Dessutom kan lösningens serverdel kan fråga efter enhetstvillingar baserat på alla informationen som beskrivs ovan.
Referera till [förstå enhetstvillingar](../articles/iot-hub/iot-hub-devguide-device-twins.md) för mer information om enhetstvillingar och den [IoT Hub-frågespråk](../articles/iot-hub/iot-hub-devguide-query-language.md) referens för frågor.


I den här självstudiekursen lär du dig att:

* Skapa en backend-app som lägger till *taggar* enhetstvillingen och en app för simulerade enheter som rapporterar sin anslutning kanal som en *rapporterade egenskap* i enhetstvillingen.
* Fråga enheter från backend-appen med hjälp av filter om taggar och egenskaper som skapade tidigare.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md