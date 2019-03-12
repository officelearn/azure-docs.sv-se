---
title: Kom igång med Azure IoT Hub identitets- och modulen modultvilling (Python) | Microsoft Docs
description: Lär dig mer om att skapa modulen identitet och uppdatera modultvilling med IoT SDK för Python.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 904598573b79640bf6547bb172702b3a1b200a49
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531773"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Kom igång med IoT Hub identitets- och modulen modultvilling med hjälp av Python-serverdel och Python-enhets-

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentiteten och enhetstvillingen gör att serverdelsprogrammet kan konfigurera en enhet och ger synlighet för enhetstillståndet tillhandahåller en modulidentitet och en modultvilling dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.
>

I slutet av den här självstudien har du två Python-appar:

* **CreateIdentities**, som skapar en enhetsidentitet, en modulidentitet och en associerad säkerhetsnyckel för att ansluta enheten och modulklienterna.
* **UpdateModuleTwinReportedProperties**, som skickar uppdaterade rapporterade egenskaper för modultvillingen till din IoT Hub.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.
>

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)
* En IoT-hubb.
* Installera senast [Python SDK](https://github.com/Azure/azure-iot-sdk-python).

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Skapa en enhetsidentitet och en modul-identitet i IoT Hub

I det här avsnittet skapar du en Python-app som skapar en enhetsidentitet och en modul-identitet i identitetsregistret i IoT hub. Enheter och moduler kan inte ansluta till IoT Hub utan en post i identitetsregistret. Mer information finns i avsnittet om identitetsregistret i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

Lägg till följande kod i Python-fil:

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
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

Den här appen skapar en enhetsidentitet med ID **myFirstDevice** och en modul identitet med ID **myFirstModule** under enhet **myFirstDevice**. (Om modul-ID:t redan finns i identitetsregistret, hämtar koden bara den befintliga modulinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade modulappen för att ansluta till din IoT Hub.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Uppdatera modultvillingen med hjälp av Python-enhets-SDK

I det här avsnittet skapar du en Python-appen på din simulerade enhet som uppdaterar modultvillingen rapporterade egenskaper.

1. **Hämta modulens anslutningssträng** – nu om du loggar in på [Azure-portalen][lnk-portal]. Gå till din IoT Hub och klicka på IoT-enheter. Hitta myFirstDevice, öppna den och du ser myFirstModule har skapats. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

  ![Information om Azure-portalmodulen][15]

1. **Skapa UpdateModuleTwinReportedProperties app** Lägg till följande `using` uttryck överst i den **Program.cs** fil:

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

## <a name="get-updates-on-the-device-side"></a>Hämta uppdateringar på enheten

Förutom koden ovan kan du lägga till nedan kodblock att hämta uppdateringen twin visas på din enhet.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```


## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Connecting your device][lnk-device-management] (Komma igång med enhetshantering)
* [Komma igång med IoT Edge][lnk-iot-edge]


<!-- Images. -->
[15]:./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
