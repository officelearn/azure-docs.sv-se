---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050465"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd, som metadata, konfigurationer och villkor. IoT Hub beständiga en enhetstvilling för varje enhet som ansluter till den.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använd enhetstvillingar för att:

* Lagra enhetens metadata från lösningens baksida.

* Rapportera aktuell tillståndsinformation, till exempel tillgängliga funktioner och villkor, till exempel den anslutningsmetod som används, från enhetsappen.

* Synkronisera tillståndet för tidskrävande arbetsflöden, till exempel uppdateringar av inbyggd programvara och konfiguration, mellan en enhetsapp och en backend-app.

* Fråga enhetens metadata, konfiguration eller tillstånd.

Enhetstvillingar är utformade för synkronisering och för att fråga enhetskonfigurationer och villkor. Mer information om när enhetstvillingar ska användas finns i [Förstå enhetstvillingar](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Enhetstvillingar lagras i en IoT-hubb och innehåller följande element:

* **Taggar**. Enhetsmetadata som endast är tillgängliga för lösningens baksida.

* **Önskade egenskaper**. JSON-objekt kan ändras med lösningens baksida och kan observeras av enhetsappen.

* **Rapporterade egenskaper**. JSON-objekt kan ändras av enhetsappen och kan läsas av lösningens baksida.

Taggar och egenskaper får inte innehålla matriser, men objekt kan kapslas.

Följande bild visar enhetstvillingorganisation:

![Enhetstvillingbild som visar funktioner](./media/iot-hub-selector-twin-get-started/twin.png)

Dessutom kan lösningens baksida fråga enhetstvillingar baserat på alla ovanstående data.
Mer information om enhetstvillingar finns i [Förstå enhetstvillingar](../articles/iot-hub/iot-hub-devguide-device-twins.md). Mer information om frågor finns i [IoT Hub-frågespråk](../articles/iot-hub/iot-hub-devguide-query-language.md).


I den här självstudiekursen lär du dig att:

* Skapa en backend-app som lägger till taggar i en enhetstvilling och en simulerad enhetsapp som rapporterar dess anslutningskanal som en rapporterad egenskap på enhetstvillingen.

* Fråga enheter från backend-appen med hjälp av filter på taggar och egenskaper som tidigare skapats.
