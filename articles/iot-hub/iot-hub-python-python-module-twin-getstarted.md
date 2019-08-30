---
title: Kom igång med Azure IoT Hub module identitet och modul, dubbla (python) | Microsoft Docs
description: 'Lär dig att skapa modul identitet och uppdatera modul dubbla med IoT SDK: er för python.'
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: b54b81a78072664ef47ace917fb3db85a5b1f297
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147610"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Kom igång med IoT Hub-modulens identitet och modul, dubbla (python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentiteten och enhetstvillingen gör att serverdelsprogrammet kan konfigurera en enhet och ger synlighet för enhetstillståndet tillhandahåller en modulidentitet och en modultvilling dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.
>

I slutet av den här självstudien har du två python-appar:

* **CreateIdentities**, som skapar en enhetsidentitet, en modulidentitet och en associerad säkerhetsnyckel för att ansluta enheten och modulklienterna.

* **UpdateModuleTwinReportedProperties**, som skickar uppdaterade rapporterade egenskaper för modultvillingen till din IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Skapa en enhets identitet och en modul identitet i IoT Hub

I det här avsnittet skapar du en python-app som skapar en enhets identitet och en modul identitet i identitets registret i din IoT-hubb. Enheter och moduler kan inte ansluta till IoT Hub utan en post i identitetsregistret. Mer information finns i avsnittet "identitets register" i [guiden för IoT Hub utvecklare](iot-hub-devguide-identity-registry.md). När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

Lägg till följande kod i python-filen:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(
        DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID +
          "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(
        DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID +
          "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("IoTHubRegistryManager sample stopped")
```

Den här appen skapar en enhets identitet med ID **t myfirstdevice** och en modul identitet med ID **MyFirstModule** under enhet **t myfirstdevice**. (Om modul-ID:t redan finns i identitetsregistret, hämtar koden bara den befintliga modulinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade modulappen för att ansluta till din IoT Hub.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [IoT Hub Developer Guide](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Uppdatera modul dubbla med python-enhets-SDK

I det här avsnittet skapar du en python-app på din simulerade enhet som uppdaterar modulens dubbla rapporterade egenskaper.

1. **Hämta din anslutnings sträng för modulen** – nu om du loggar in på [Azure Portal](https://portal.azure.com/). Gå till din IoT Hub och klicka på IoT-enheter. Hitta T myfirstdevice, öppna den och se att myFirstModule har skapats. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

   ![Information om Azure-portalmodulen](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Skapa UpdateModuleTwinReportedProperties-app**

   Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

Det är kodexemplet visar hur du hämtar modultvillingen och uppdaterar rapporterade egenskaper med AMQP-protokollet.

## <a name="get-updates-on-the-device-side"></a>Hämta uppdateringar på enhets Sidan

Förutom ovanstående kod kan du lägga till under kod blocket för att hämta det dubbla uppdaterings meddelandet på enheten.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""


def module_twin_callback(update_state, payload, user_context):
    print("")
    print("Twin callback called with:")
    print("updateStatus: %s" % update_state)
    print("context: %s" % user_context)
    print("payload: %s" % payload)


try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("module client sample stopped")
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Komma igång med enhets hantering](iot-hub-node-node-device-management-get-started.md)

* [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)