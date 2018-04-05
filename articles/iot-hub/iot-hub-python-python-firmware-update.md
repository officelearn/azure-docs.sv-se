---
title: Enheten firmware-uppdatering med Azure IoT Hub (Python) | Microsoft Docs
description: Hur du använder hantering av enheter på Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Azure IoT-SDK för Python används för att implementera en simulerad enhetsapp och en service-appen som utlöser firmware-uppdatering.
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: 31a7ba88997f54c5000b1018fc96abf8120dd232
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Använd enhetshantering att initiera en enhetens inbyggda programvara uppdatera (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudiekursen du sett hur du använder den [enheten dubbla] [ lnk-devtwin] och [direkt metoder] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här kursen använder samma IoT-hubb primitiver och vägledning och visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering.  Det här mönstret används i uppdatering av inbyggd hanteringsprogramvara för Intel modern enhet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Python-konsolapp som anropar metoden firmwareUpdate direkt i appen simulerade enheten via din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen inbyggd programvara, laddar ned avbildningen av inbyggd programvara och slutligen använder inbyggd avbildningen. Under varje steg i uppdateringen använder enheten rapporterade egenskaperna för att rapportera förlopp.

I slutet av den här kursen har du två Python konsolappar:

**dmpatterns_fwupdate_service.PY**, som anropar en direkt metod i appen simulerade enheten visar svaret och regelbundet (varje 500ms) visar den uppdaterade rapporterade egenskaper.

**dmpatterns_fwupdate_device.PY**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare, tar emot en firmwareUpdate direkt metod, körs via en process som flera tillstånd för att simulera en firmware-uppdatering inklusive: väntar på avbildningen hämta hämta den nya avbildningen och slutligen avbildningen används.

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en Python-konsolapp som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder direkta metoden för att initiera uppdateringen och använder enheten dubbla frågor för att hämta status för aktiva firmware-uppdatering med jämna mellanrum.

1. Vid en kommandotolk, kör du följande kommando för att installera den **azure-iothub-service-klient** paketet:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Använd en textredigerare, i arbetskatalogen för att skapa en **dmpatterns_getstarted_service.py** fil.

1. Lägg till följande importsatser och variabler i början av den **dmpatterns_getstarted_service.py** fil. Ersätt `IoTHubConnectionString` och `deviceId` med de värden som nämndes tidigare:
   
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

1. Lägg till följande funktion att anropa metoden direkt och visa värdet för firmwareUpdate rapporterade egenskapen. Också lägga till den `main` rutin:
   
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

* Skapa en Python-konsolapp som svarar på en direkt metod som anropas av molnet
* Utlösa en simulerad uppdatering av inbyggd programvara
* Använda rapporterade egenskaper för att aktivera enhetstvillingfrågor för att identifiera enheter och ta reda på när de senast slutfört en uppdatering av en inbyggd programvara

1. Vid en kommandotolk, kör du följande kommando för att installera den **azure-iothub-enhet-klient** paketet:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Med hjälp av en textredigerare, skapa en **dmpatterns_fwupdate_device.py** fil.

1. Lägg till följande importsatser och variabler i början av den **dmpatterns_fwupdate_device.py** fil. Ersätt `deviceConnectionString` med anslutningssträngen enheten från din IoT-hubb:
   
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

1. Lägg till följande funktioner som används för att ange rapporterade egenskaper uppdateringar och genomföra den direkta metoden:
   
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

8. Lägg till följande funktion som initierar enheten-dubbla rapporterade egenskaper och vänta tills den direkta metoden anropas. Också lägga till den `main` rutin:
   
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
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskod, bör du implementera försök principer (till exempel en exponentiell backoff) enligt förslaget i MSDN-artikel [hantering av tillfälliga fel](https://msdn.microsoft.com/library/hh675232.aspx).
> 


## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken kör följande kommando för att börja lyssna efter omstart direkta metoden.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. I en annan kommandotolk, kör du följande kommando för att utlösa remote omstart och fråga för enheten dubbla att söka efter senaste omstart tid.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Svaret från enheten till den direkta metoden i konsolen visas. Anteckna ändringen i rapporterade egenskaper i hela firmware-uppdatering.

    ![Programmets utdata.][1]


## <a name="next-steps"></a>Nästa steg
I den här kursen används direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används egenskaperna rapporterade för att följa förloppet för firmware-uppdatering.

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

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
