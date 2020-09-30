---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580552"
---
Med IoT-Plug and Play kan du bygga smarta enheter som annonserar sina funktioner i Azure IoT-program. IoT Plug and Play-enheter kräver ingen manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program.

En smart enhet kan implementeras direkt, använda [moduler](../articles/iot-hub/iot-hub-devguide-module-twins.md)eller använda [IoT Edge moduler](../articles/iot-edge/about-iot-edge.md).

Den här guiden beskriver de grundläggande stegen som krävs för att skapa en enhet, modul eller IoT Edge modul som följer [IoT plug and Play-konventionerna](../articles/iot-pnp/concepts-convention.md).

Följ dessa steg om du vill bygga en IoT Plug and Play-enhet, modul eller IoT Edge-modul:

1. Se till att enheten använder antingen MQTT-eller MQTT över WebSockets-protokollet för att ansluta till Azure IoT Hub.
1. Skapa en [DTDL-modell (Digital-definitions språk)](https://github.com/Azure/opendigitaltwins-dtdl) för att beskriva enheten. Mer information finns i [förstå komponenter i IoT plug and Play-modeller](../articles/iot-pnp/concepts-components.md).
1. Uppdatera din enhet eller modul för att tillkännage `model-id` som en del av enhets anslutningen.
1. Implementera telemetri, egenskaper och kommandon med [IoT plug and Play konventioner](../articles/iot-pnp/concepts-convention.md)

När din enhet eller modul har implementerats kan du använda [Azure IoT Explorer](../articles/iot-pnp/howto-use-iot-explorer.md) för att kontrol lera att enheten följer IoT plug and Play-konventionerna.
