---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: eef073f5f4d1eb39fd5ccd8dafacd7074158fa37
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667852"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub sparar en enhet för varje enhet som ansluter till den.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använd enheten:

* Lagra metadata för enheten från Server delen av lösningen.

* Rapportera aktuell statusinformation, till exempel tillgängliga funktioner och villkor (till exempel den anslutnings metod som används) från din enhets app.

* Synkronisera tillstånd för långvariga arbets flöden (till exempel inbyggd program vara och konfigurations uppdateringar) mellan en enhets app och en backend-app.

* Fråga om metadata, konfiguration eller tillstånd för enheten.

Enhets grupper är utformade för synkronisering och för att ställa frågor om enhets konfiguration och villkor. Mer information om när du ska använda enhets uppdelade enheter finns i [förstå enhets dubbla](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Enhetens dubbla är lagrade i en IoT-hubb och innehåller:

* *taggar*, metadata för enheten kan bara nås av lösningens Server del;

* *önskade egenskaper*, JSON-objekt som kan ändras av lösningens Server del och som kan observeras av enhetens app; särskilt

* *rapporterade egenskaper*, JSON-objekt som kan ändras av Device app och kan läsas av lösningens Server del. Taggar och egenskaper får inte innehålla matriser, men objekt kan kapslas.

![Enhetens dubbla bild som visar funktioner](./media/iot-hub-selector-twin-get-started/twin.png)

Lösningens Server del kan dessutom fråga enheten med varandra baserat på alla dessa data.
Läs mer om enhets-och enhets [referenser för att](../articles/iot-hub/iot-hub-devguide-device-twins.md) få mer information om enheten, och till [IoT Hub frågespråk](../articles/iot-hub/iot-hub-devguide-query-language.md) för frågor.


I den här självstudiekursen lär du dig att:

* Skapa en backend-app som lägger till *taggar* till en enhet och en simulerad enhets app som rapporterar anslutnings kanalen som en rapporterad *egenskap* på enheten.

* Fråga enheter från din backend-app med hjälp av filter på de taggar och egenskaper som skapats tidigare.
