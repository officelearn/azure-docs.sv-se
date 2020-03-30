---
title: Schemalägga jobb med Azure IoT Hub (Python) | Microsoft-dokument
description: Så här schemalägger du ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du använder Azure IoT SDK:er för Python för att implementera de simulerade enhetsapparna och en tjänstapp för att köra jobbet.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527409"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Schemalägga och sända jobb (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som gör det möjligt för en backend-app för att skapa och spåra jobb som schemalägger och uppdaterar miljontals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkta metoder

Begreppsmässigt radbryts en av dessa åtgärder och spårar körningens förlopp mot en uppsättning enheter, som definieras av en enhetstvillingfråga.  En backend-app kan till exempel använda ett jobb för att anropa en omstartsmetod på 10 000 enheter, som anges av en enhetstvillingfråga och schemalagd vid en framtida tidpunkt.  Det programmet kan sedan spåra förloppet som var och en av dessa enheter ta emot och köra omstart metoden.

Läs mer om var och en av dessa funktioner i följande artiklar:

* Enhetstvilling och egenskaper: [Kom igång med enhetstvillingar](iot-hub-python-twin-getstarted.md) och [självstudiekurs: Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub developer guide - direkta metoder](iot-hub-devguide-direct-methods.md) och [handledning: direkta metoder](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Python-simulerad enhetsapp som har en direkt metod, vilket möjliggör **lockDoor**, som kan anropas av lösningens backend.

* Skapa en Python-konsolapp som anropar **lockDoor** direct-metoden i den simulerade enhetsappen med hjälp av ett jobb och uppdaterar önskade egenskaper med hjälp av ett enhetsjobb.

I slutet av den här självstudien har du två Python-appar:

**simDevice.py**, som ansluter till din IoT-hubb med enhetens identitet och tar emot en **lockDoor** direct-metod.

**scheduleJobService.py**, som anropar en direkt metod i den simulerade enhetsappen och uppdaterar enhetstvillingens önskade egenskaper med hjälp av ett jobb.

> [!NOTE]
> **Azure IoT SDK för Python** stöder inte direkt **jobbfunktioner.** Istället erbjuder den här självstudien en alternativ lösning som använder asynkrona trådar och timers. Ytterligare uppdateringar finns i funktionslistan **Service Client SDK** på sidan [Azure IoT SDK för Python.](https://github.com/Azure/azure-iot-sdk-python)
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Python-konsolapp som svarar på en direkt metod som anropas av molnet, vilket utlöser en simulerad **lockDoor-metod.**

1. Kör följande kommando för att installera **azure-iot-device-paketet i** kommandotolken:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Skapa en ny **simDevice.py-fil** i arbetskatalogen med hjälp av en textredigerare.

3. Lägg till `import` följande satser och variabler i början av **simDevice.py** filen. Ersätt `deviceConnectionString` med anslutningssträngen för enheten som du skapade ovan:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Lägg till följande funktionsåterställning för att hantera **lockDoor-metoden:**

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Lägg till ytterligare en funktionsåterställning för att hantera uppdateringar av enhetstvillingar:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Lägg till följande kod för att registrera hanteraren för **lockDoor-metoden.** Inkludera även `main` rutinen:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Spara och stäng **simDevice.py** filen.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för återförsök (till exempel en exponentiell backoff), som föreslås i artikeln [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en backend-tjänst som anropar en direkt metod på en enhet och uppdaterar enhetstvillingen. Tjänsten behöver **tjänstens anslutningsbehörighet** för att anropa en direkt metod på en enhet. Tjänsten behöver också **registret läsa** och register skrivbehörighet för att läsa och skriva identitetsregistret. **registry write** Det finns ingen standardprincip för delad åtkomst som bara innehåller dessa behörigheter, så du måste skapa en.

Så här skapar du en princip för delad åtkomst som beviljar **tjänstanslutning,** **registerläsning**och **registerskrivningsbehörighet** och för att hämta en anslutningssträng för den här principen:

1. Öppna din IoT-hubb i [Azure-portalen](https://portal.azure.com). Det enklaste sättet att komma till din IoT-hubb är att välja **Resursgrupper,** välj resursgruppen där IoT-hubben finns och välj sedan din IoT-hubb i listan över resurser.

2. Välj Principer för **delad åtkomst**i den vänstra rutan i IoT-hubben .

3. Välj **Lägg till**i den övre menyn ovanför listan över principer.

4. Ange ett beskrivande namn för principen i **fönstret Lägg till en princip** för delad åtkomst. till exempel: *serviceOchRegistryReadWrite*. Under **Behörigheter**väljer du **Tjänstanslutning** och **registerskrivning** (**Registerläsning** väljs automatiskt när du väljer **Registerskrivning**). Välj sedan **Skapa**.

    ![Visa hur du lägger till en ny princip för delad åtkomst](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. I fönstret **Principer för delad åtkomst** väljer du den nya principen i listan över principer.

6. Under **Delade åtkomstnycklar**väljer du **kopieringsikonen för anslutningssträngen – primärnyckeln** och sparar värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Mer information om principer och behörigheter för IoT Hub-delad åtkomst finns i [Åtkomstkontroll och behörigheter](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägga jobb för att anropa en direkt metod och uppdatera en enhetstvillings egenskaper

I det här avsnittet skapar du en Python-konsolapp som initierar ett **fjärrlåsDörr** på en enhet med en direkt metod och uppdaterar även enhetstvillingens önskade egenskaper.

1. Kör följande kommando för att installera **azure-iot-hub-paketet** i kommandotolken:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Skapa en ny **scheduleJobService.py-fil** i arbetskatalogen med hjälp av en textredigerare.

3. Lägg till `import` följande satser och variabler i början av **scheduleJobService.py** filen. Ersätt `{IoTHubConnectionString}` platshållaren med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string). Ersätt `{deviceId}` platshållaren med det enhets-ID som du registrerade i [Registrera en ny enhet i IoT-hubben:](#register-a-new-device-in-the-iot-hub)

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Lägg till följande funktion som används för att fråga efter enheter:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Lägg till följande metoder för att köra de jobb som anropar direktmetoden och enhetstvillingen:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Lägg till följande kod för att schemalägga jobb och uppdatera jobbstatus. Inkludera även `main` rutinen:

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

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
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

7. Spara och **scheduleJobService.py** stäng scheduleJobService.py-filen.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando i kommandotolken i arbetskatalogen för att börja lyssna efter metoden starta om direkt:

    ```cmd/sh
    python simDevice.py
    ```

2. Vid en annan kommandotolk i arbetskatalogen kör du följande kommando för att utlösa jobben för att låsa dörren och uppdatera tvillingen:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Enhetssvaren på uppdateringen av direktmetoden och enhetstvillingarna i konsolen.

    ![Exempel på IoT Hub-jobb 1 – enhetsutdata](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub Job-exempel 2- enhetsutdata](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetstvillingens egenskaper.

Information om hur du fortsätter att komma igång med IoT Hub och enhetshanteringsmönster som fjärröver uppdateringen av den inbyggda programvaran finns i [Så här gör du en uppdatering av den inbyggda programvaran](tutorial-firmware-update.md).