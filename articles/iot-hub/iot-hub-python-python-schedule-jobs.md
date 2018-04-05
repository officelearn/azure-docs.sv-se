---
title: Schemalägga med Azure IoT Hub (Python) | Microsoft Docs
description: Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod på flera enheter. Azure IoT-SDK för Python används för att implementera de simulerade enheten apparna och service-appen för att köra jobbet.
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: 632e1788f614b0a57d631759d1638027e1d0118a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-python"></a>Schemat och sändning jobb (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT-hubb är en helt hanterad tjänst som gör en backend-app kan skapa och spåra jobb som schemalägger och uppdatera miljoner enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkt metoder

Begreppsmässigt ett jobb radbryts i någon av följande åtgärder och spårar förloppet för körning mot en uppsättning enheter, som definieras av en enhet dubbla fråga.  En backend-app kan till exempel använda ett jobb för att anropa en metod för omstart i 10 000 enheter som angetts av en enhet dubbla fråga och schemalagda vid ett senare tillfälle.  Programmet kan sedan följa förloppet som var och en av dessa enheter tar emot och kör metod för omstart.

Mer information om var och en av dessa funktioner i de här artiklarna:

* Enheten dubbla och egenskaper: [Kom igång med enheten twins] [ lnk-get-started-twin] och [Självstudier: hur du använder identiska enhetsegenskaper][lnk-twin-props]
* Dirigera metoder: [IoT-hubb Utvecklarhandbok - direkt metoder] [ lnk-dev-methods] och [Självstudier: Dirigera metoder][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapar en simulerad enhet Python-app som har en direkt metod, vilket gör att **lockDoor**, som kan anropas av lösningens serverdel.
* Skapa en Python-konsolapp som anropar den **lockDoor** direkt metod i appen simulerade enheten med ett jobb och uppdateringar de egenskaper med hjälp av en enhet jobbet.

I slutet av den här kursen har du två Python-appar:

**simDevice.py**, som ansluter till din IoT-hubb med enhetens identitet och tar emot en **lockDoor** direkt metod.

**scheduleJobService.py**, som anropar en direkt metod i appen simulerade enheten och uppdaterar den enheten dubbla önskade egenskaper med hjälp av ett jobb.

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

> [!NOTE]
> Den **Azure IoT-SDK för Python** stöder inte direkt **jobb** funktioner. Den här kursen finns i stället en alternativ lösning som använder asynkrona trådar och timers. Ytterligare uppdateringar finns i **tjänsten klient-SDK** funktionslista på den [Azure IoT-SDK för Python](https://github.com/Azure/azure-iot-sdk-python) sidan. 
> 
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet skapar du en Python-konsolapp som svarar på en direkt metod som anropas av moln, vilket utlöser en simulerad **lockDoor** metod.

1. Vid en kommandotolk, kör du följande kommando för att installera den **azure-iot-enhet-klient** paketet:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Med hjälp av en textredigerare, skapa en ny **simDevice.py** filen i arbetskatalogen.

1. Lägg till följande `import` instruktioner och variabler i början av den **simDevice.py** fil. Ersätt `deviceConnectionString` med anslutningssträngen på den enhet som du skapade ovan:
   
    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Lägg till följande funktion återanropet för att hantera den **lockDoor** metod:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Lägg till en annan funktion återanrop för att hantera enheten twins uppdateringar:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Lägg till följande kod för att registrera hanterare för den **lockDoor** metod. Även innehålla de `main` rutin:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

1. Spara och Stäng den **simDevice.py** fil.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägga jobb för direkta metoden och uppdatera egenskaperna för en enhet dubbla
I det här avsnittet skapar du en Python-konsolapp som initierar en fjärransluten **lockDoor** på en enhet med en direkt metod och uppdatera enheten-dubbla egenskaper.

1. Vid en kommandotolk, kör du följande kommando för att installera den **azure-iot-service-klient** paketet:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Med hjälp av en textredigerare, skapa en ny **scheduleJobService.py** filen i arbetskatalogen.

1. Lägg till följande `import` instruktioner och variabler i början av den **scheduleJobService.py** fil:
   
    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

1. Lägg till följande funktion som används för att söka efter enheter:
   
    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
    
        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)
    
        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

1. Lägg till följande metoder för att köra jobb som anropar den direkta metoden och enheten dubbla:
   
    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
    
            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
        
            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )
        
    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
    
            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
        
            print ( "" )
            print ( "Device twin updated." )
    ```

1. Lägg till följande kod för att schemalägga jobb och uppdatera jobbstatus. Även innehålla de `main` rutin:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()
        
            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )
        
            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()
        
            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )
        
            while True:
                print ( "" )
            
                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )
            
                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )
                
                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
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
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

1. Spara och Stäng den **scheduleJobService.py** fil.


## <a name="run-the-applications"></a>Köra programmen
Nu är det dags att köra programmen.

1. Kör följande kommando för att börja lyssna efter omstart direkta metoden Kommandotolken i arbetskatalogen:
   
    ```cmd/sh
    python simDevice.py
    ```

1. Kör följande kommando för att utlösa jobb för att låsa dörren och uppdatera dubbla i en annan kommandotolk i arbetskatalogen:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Du ser att enheten svar till den direkta metoden och enheten twins uppdateras i konsolen.

    ![utdata för enhet][1]

    ![Service-utdata][2]


## <a name="next-steps"></a>Nästa steg
I den här kursen används ett jobb för att schemalägga en direkt metod för att en enhet och enheten-dubbla egenskaper att uppdatera.

Om du vill fortsätta komma igång med IoT-hubb och device management mönster, till exempel remote via luften firmware-uppdatering, se:

[Självstudier: Hur du gör en firmware-uppdatering][lnk-fwupdate]

Om du vill fortsätta komma igång med IoT-hubb finns [komma igång med Azure IoT kant][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-python-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-python-python-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults

[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
