---
title: Identitet och modultvilling för Azure IoT Hub -modul (Python)
description: Lär dig hur du skapar modulidentitet och uppdateringsmodultvilling med IoT SDK:er för Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756923"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Komma igång med IoT Hub-modulidentitet och modultvilling (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar Azure IoT Hub-enhetsidentiteter och enhetstvillingar, men ger finare granularitet. Azure IoT Hub-enhetsidentiteter och enhetstvillingar möjliggör ett backend-program för att konfigurera en enhet och ger synlighet på enhetens villkor, men modulidentiteter och modultvillingar tillhandahåller dessa funktioner för enskilda komponenter på en enhet. På kompatibla enheter med flera komponenter, till exempel operativsystembaserade enheter eller enheter för inbyggd programvara, möjliggör de isolerad konfiguration och villkor för varje komponent.
>

I slutet av den här självstudien har du tre Python-appar:

* **CreateModule**, som skapar en enhetsidentitet, en modulidentitet och tillhörande säkerhetsnycklar för att ansluta enheten och modulklienter.

* **UpdateModuleTwinDesiredProperties**, som skickar uppdaterade modul dubbla önskade egenskaper till din IoT Hub.

* **ReceiveModuleTwinDesiredPropertiesPatch**, som tar emot modulen dubbla önskade egenskaper patch på din enhet.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en backend-tjänst som lägger till en enhet i identitetsregistret och sedan lägger till en modul på den enheten. Den här tjänsten kräver **registerskrivningsbehörighet** (som även inkluderar **registerläsning**). Du kan också skapa en tjänst som lägger till önskade egenskaper i modultvillingen för den nyskapade modulen. Den här tjänsten behöver **tjänstens anslutningsbehörighet.** Även om det finns standardprinciper för delad åtkomst som ger dessa behörigheter individuellt, skapar du i det här avsnittet en anpassad princip för delad åtkomst som innehåller båda dessa behörigheter.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Skapa en enhetsidentitet och en modulidentitet i IoT Hub

I det här avsnittet skapar du en Python-tjänstapp som skapar en enhetsidentitet och en modulidentitet i identitetsregistret i IoT-hubben. En enhet eller modul kan inte ansluta till IoT-hubb om den inte har en post i identitetsregistret. Mer information finns [i Förstå identitetsregistret i IoT-hubben](iot-hub-devguide-identity-registry.md). När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

1. Kör följande kommando för att installera **azure-iot-hub-paketet** i kommandotolken:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. I kommandotolken kör du följande kommando för att installera **msrest-paketet.** Du behöver det här paketet för att fånga **httpoperationerror** undantag.

    ```cmd/sh
    pip install msrest
    ```

1. Skapa en fil med namnet **CreateModule.py** i arbetskatalogen med hjälp av en textredigerare.

1. Lägg till följande kod i Python-filen. Ersätt *YourIoTHubConnectionString* med anslutningssträngen som du kopierade i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Kör följande kommando i kommandotolken:

    ```cmd/sh
    python CreateModule.py
    ```

Denna app skapar en enhetsidentitet med ID **myFirstDevice** och en modul identitet med ID **myFirstModule** under enheten **myFirstDevice**. (Om enhets- eller modul-ID:t redan finns i identitetsregistret hämtar koden helt enkelt den befintliga enhets- eller modulinformationen.) Appen visar ID och primärnyckel för varje identitet.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns [i Förstå identitetsregistret i IoT-hubben](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Uppdatera modultvillingen med Python-tjänsten SDK

I det här avsnittet skapar du en Python-tjänstapp som uppdaterar modulen twin önskade egenskaper.

1. Kör följande kommando för att installera **azure-iot-hub-paketet** i kommandotolken. Du kan hoppa över det här steget om du har installerat **azure-iot-hub-paketet** i föregående avsnitt.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Skapa en fil med namnet **UpdateModuleTwinDesiredProperties.py** i arbetskatalogen med hjälp av en textredigerare.

1. Lägg till följande kod i Python-filen. Ersätt *YourIoTHubConnectionString* med anslutningssträngen som du kopierade i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Få uppdateringar på enhetens sida

I det här avsnittet skapar du en Python-app för att få modulen twin önskade egenskaper uppdatering på din enhet.

1. Hämta din modulanslutningssträng. I [Azure-portalen](https://portal.azure.com/)navigerar du till din IoT-hubb och väljer **IoT-enheter** i den vänstra rutan. Välj **myFirstDevice** från listan över enheter och öppna den. Under **Modulidentiteter**väljer du **myFirstModule**. Kopiera modulens anslutningssträng. Du behöver det i ett följande steg.

   ![Information om Azure-portalmodulen](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Kör följande kommando för att installera **azure-iot-device-paketet i** kommandotolken:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Skapa en fil med namnet **ReceiveModuleTwinDesiredPropertiesPatch.py** i arbetskatalogen med hjälp av en textredigerare.

1. Lägg till följande kod i Python-filen. Ersätt *DinModuleConnectionString* med den modulanslutningssträng som du kopierade i steg 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Kör apparna

I det här avsnittet kör du appen **ReceiveModuleTwinDesiredPropertiesPatch-enhet** och kör sedan serviceappen **UpdateModuleTwinDesiredProperties** för att uppdatera önskade egenskaper för din modul.

1. Öppna en kommandotolk och kör enhetsappen:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Inledande utdata för enhetsapp](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Öppna en separat kommandotolk och kör tjänstappen:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Observera att egenskapen **TelemetryInterval** visas i den uppdaterade modultvillingen i tjänstapputdata:

   ![Utdata för tjänstappar](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Samma egenskap visas i den önskade egenskapskorrigeringen som tas emot i enhetsapputdata:

   ![Enhetsapputdata visar önskad egenskapskorrigering](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Connecting your device](iot-hub-node-node-device-management-get-started.md) (Komma igång med enhetshantering)

* [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)