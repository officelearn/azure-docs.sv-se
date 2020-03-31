---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558734"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Backend-appar kan använda Azure IoT Hub-primitiver, till exempel [enhetstvilling][lnk-devtwin] och [direkta metoder][lnk-c2dmethod], för att fjärrstarta och övervaka enhetshanteringsåtgärder på enheter. Den här självstudien visar hur en backend-app och en enhetsapp kan arbeta tillsammans för att initiera och övervaka en omstart av en fjärrenhet med IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använd en direkt metod för att initiera enhetshanteringsåtgärder (till exempel omstart, fabriksåterställning och uppdatering av inbyggd programvara) från en backend-app i molnet. Enheten ansvarar för:

* Hantera metodbegäran som skickas från IoT Hub.

* Initiera motsvarande enhetsspecifika åtgärd på enheten.

* Tillhandahålla statusuppdateringar via *rapporterade egenskaper* till IoT Hub.

Du kan använda en backend-app i molnet för att köra enhetstvillingfrågor för att rapportera om hur dina enhetshanteringsåtgärder fortskrider.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
