---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011823"
---
> [!div class="op_single_selector"]
> * [Enhet: Node.js-tjänst: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Enhet: C#Tjänsten: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Enhet: Java-tjänst: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Enhet: Python Service: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Backend-appar kan använda Azure IoT Hub primitiver som [enhetstvillingen] [ lnk-devtwin] och [direkta metoder][lnk-c2dmethod], för att starta och övervaka enhet hanteringsåtgärder på enheter. Den här kursen visar hur en app på serversidan och en enhetsapp kan fungera tillsammans för att initiera och övervaka en fjärransluten enhet omstart med hjälp av IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använd en direkt metod för att initiera åtgärder för hantering av enhet (till exempel omstart, fabriksåterställning och uppdatering av inbyggd programvara) från en backend-app i molnet. Enheten ansvarar för att:

* Hantering av metodbegäran som skickats från IoT Hub.
* Initierar enhetsspecifika åtgärd på enheten.
* Tillhandahåller statusuppdateringar via *rapporterade egenskaper* till IoT Hub.

Du kan använda en backend-app i molnet för att köra enhetstvillingfrågor för att rapportera om förloppet för åtgärder för hantering av din enhet.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
