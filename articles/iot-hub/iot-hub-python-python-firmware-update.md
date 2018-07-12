---
title: Enheten firmware-uppdatering med Azure IoT Hub (Python) | Microsoft Docs
description: Hur du använder enhetshantering i Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Du kan använda Azure IoT SDK för Python för att implementera en simulerad enhetsapp och en service-app som utlöser uppdateringen av inbyggd programvara.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: kgremban
ms.openlocfilehash: d2ebdf54e595c2f02464c0c2446a6e5f5feefb9c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38482028"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Använd enhetshantering för att initiera en enhetens inbyggda programvara uppdatera (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudier, såg du hur du använder den [enhetstvillingen] [ lnk-devtwin] och [direkta metoder ] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här självstudien använder samma IoT Hub-primitiver och ger vägledning och visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering.  Det här mönstret används i update-implementeringen av inbyggd programvara för Intel Edison enheten exemplet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Python-konsolapp som anropar metoden firmwareUpdate direkt i den simulerade enhetsappen via din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen för inbyggd programvara, laddar ned avbildningen för inbyggd programvara och slutligen gäller avbildningen för inbyggd programvara. Enheten använder de rapporterade egenskaperna under varje steg av uppdateringen för att informera om förloppet.

I slutet av den här självstudien har du två Python-konsolappar:

**dmpatterns_fwupdate_service.PY**, som anropar en direkt metod i den simulerade enhetsappen visar svaret och regelbundet (varje 500ms) visar det uppdaterade rapporterade egenskaper.

**dmpatterns_fwupdate_device.PY**, som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare, får en direkt metod firmwareUpdate, körs via en process som har flera tillstånd att simulera en firmware update inklusive: väntar på avbildningen ladda ned att ladda ned den nya avbildningen och slutligen avbildningen används.

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlösa en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en Python-konsolapp som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder en direkt metod för att initiera uppdateringen och använder enhetstvillingfrågor för att regelbundet hämta status för aktiva firmware-uppdatering.

1. I Kommandotolken, kör du följande kommando för att installera den **azure-iothub-service-client** paketet:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Använd en textredigerare i arbetskatalogen, skapa en **dmpatterns_getstarted_service.py** fil.

1. Lägg till följande import-instruktioner och variabler i början av den **dmpatterns_getstarted_service.py** fil. Ersätt `IoTHubConnectionString` och `deviceId` med dina värden som angavs tidigare:
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. Lägg till följande funktion som anropar direktmetoden och visa värdet för firmwareUpdate rapporterade egenskap. Lägg även till den `main` rutinen:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. Spara och Stäng den **dmpatterns_fwupdate_service.py** fil.


## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet får du:

* Skapa en Python-konsolapp som svarar på en direkt metod som anropas via molnet
* Utlösa en simulerad uppdatering av inbyggd programvara
* Använda rapporterade egenskaper för att aktivera enhetstvillingfrågor för att identifiera enheter och ta reda på när de senast slutfört en uppdatering av en inbyggd programvara

1. I Kommandotolken, kör du följande kommando för att installera den **azure-iothub-device-client** paketet:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Använd en textredigerare och skapa en **dmpatterns_fwupdate_device.py** fil.

1. Lägg till följande import-instruktioner och variabler i början av den **dmpatterns_fwupdate_device.py** fil. Ersätt `deviceConnectionString` med enhetens anslutningssträng från IoT hub:
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. Lägg till följande funktioner som används för att ange rapporterade egenskaper för uppdateringar och implementera den direkta metoden:
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Lägg till följande funktioner som simulerar nedladdning och användning av en avbildning för inbyggd programvara:
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. Lägg till följande funktion som initierar enhetstvillingen rapporterade egenskaper och vänta tills den direkta metoden anropas. Lägg även till den `main` rutinen:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff) vilket rekommenderas i MSDN-artikeln [hantering av tillfälliga fel](https://msdn.microsoft.com/library/hh675232.aspx).
> 


## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. Kör följande kommando för att börja lyssna efter omstart direkt metod i Kommandotolken.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. I en annan kommandotolk kör du följande kommando för att utlösa fjärromstart och fråga för enhetstvillingen för att hitta senaste omstart tid.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Du kan se svaret från enheten till den direkta metoden i konsolen. Anteckna ändringen av rapporterade egenskaper under hela uppdateringen av inbyggd programvara.

    ![programmets utdata][1]


## <a name="next-steps"></a>Nästa steg
I den här självstudien används en direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används de rapporterade egenskaperna för att följa förloppet för uppdatering av inbyggd programvara.

Läs hur du utökar din IoT-lösning och schema anropar på flera enheter i den [schema och sändningsjobb] [ lnk-tutorial-jobs] självstudien.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
