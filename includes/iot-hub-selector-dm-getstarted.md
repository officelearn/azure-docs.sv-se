---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 13eddced155eab6dedfbce77330e7a178ecfb3cb
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166011"
---
> [!div class="op_single_selector"]
> * [Device: Node.js Service: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Enhet: C# Service:C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Enhet: Java-tjänst: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Device: Python-tjänsten: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Backend-appar kan använda Azure IoT Hub primitiver som [enhetstvillingen] [ lnk-devtwin] och [direkta metoder][lnk-c2dmethod], för att starta och övervaka enhet hanteringsåtgärder på enheter. Den här kursen visar hur en app på serversidan och en enhetsapp kan fungera tillsammans för att initiera och övervaka en fjärransluten enhet omstart med hjälp av IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använd en direkt metod för att initiera åtgärder för hantering av enhet (till exempel omstart, fabriksåterställning och uppdatering av inbyggd programvara) från en backend-app i molnet. Enheten ansvarar för att:

* Hantering av metodbegäran som skickats från IoT Hub.
* Initierar enhetsspecifika åtgärd på enheten.
* Tillhandahåller statusuppdateringar via *rapporterade egenskaper* till IoT Hub.

Du kan använda en backend-app i molnet för att köra enhetstvillingfrågor för att rapportera om förloppet för åtgärder för hantering av din enhet.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
