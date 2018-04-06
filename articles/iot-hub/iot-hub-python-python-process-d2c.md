---
title: Vidarebefordra meddelanden med Azure IoT Hub (Python) | Microsoft Docs
description: Så här bearbeta meddelanden för Azure IoT Hub-enhet till moln genom att använda regler för Routning och anpassade slutpunkter för att skicka meddelanden till andra backend-tjänster.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: c6182f878076bae27b18d0556a77c8d95677e9c1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Vidarebefordra meddelanden med IoT-hubben (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Den här kursen bygger på den [Kom igång med IoT-hubb] kursen.  Självstudier:

* Visar hur du använder regler för routning för att skicka meddelanden från enhet till moln i ett enkelt och konfigurationsbaserade sätt.
* Visar hur du isolera interaktiva meddelanden som kräver omedelbar åtgärd från lösningens serverdel för vidare bearbetning.  En enhet kan till exempel skicka larmmeddelandet som utlöser lägga till ett ärende i ett CRM-system.  Däremot-datapunkt meddelanden, till exempel temperatur telemetri, mata in en analytics-motorn.

I slutet av den här kursen kan köra du tre Python konsolappar:

* **SimulatedDevice.py**, en modifierad version av app som skapats i den [Kom igång med IoT-hubb] självstudiekursen skickar datapunkt meddelanden från enhet till moln alla andra och interaktiva enhet till moln meddelanden per slumpmässiga intervall. Den här appen använder protokollet MQTT kommunicerar med IoT-hubben.
* **ReadCriticalQueue.py** tar bort viktiga meddelanden från Service Bus-kö som är kopplade till IoT-hubben.

> [!NOTE]
> IoT-hubben har SDK stöd för många vilka plattformar och språk, inklusive C, Java och JavaScript. Instruktioner om hur du ersätter enheten i den här självstudiekursen med en fysisk enhet och ansluta enheter till en IoT-hubb finns i [Azure IoT Developer Center].

För att kunna genomföra den här kursen behöver du följande:

* En fullständig fungerande version av den [Kom igång med IoT-hubb] kursen.
* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* [Node.js 4.0 eller senare][lnk-node-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Detta krävs för att installera [IoT Hub Explorer-verktyget][lnk-iot-hub-explorer].
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Vi rekommenderar också läsa mer om [Azure Storage] och [Azure Service Bus].


## <a name="send-interactive-messages-from-a-device-app"></a>Skicka interaktiva meddelanden från en enhetsapp
I det här avsnittet kan du ändra appen för enheter som du skapade i den [Kom igång med IoT-hubb] kursen att ibland skicka meddelanden som kräver omedelbar bearbetning.

1. Använd en textredigerare för att öppna den **SimulatedDevice.py** fil. Den här filen innehåller koden för den **SimulatedDevice** app som du skapade i den [Kom igång med IoT-hubb] kursen.

2. Ersätt den **iothub_client_telemetry_sample_run** funktionen med följande kod:

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    Den här metoden lägger slumpmässigt till egenskapen `"level": "critical"` och `"level": "storage"` på meddelanden som skickas av enheten, som simulerar ett meddelande som kräver omedelbar åtgärd som programmet backend- eller som behöver lagras permanent. Programmet stöder vidarebefordra meddelanden utifrån meddelandetexten.
   
   > [!NOTE]
   > Du kan använda meddelandeegenskaper skicka meddelanden för olika scenarier, inklusive kall sökväg bearbetning, förutom varm sökväg exemplet som visas här.

1. Spara och stäng filen **SimulatedDevice.py**.

    > [!NOTE]
    > För enkelhetens skull implementerar inte den här självstudiekursen princip försök igen. I produktionskod, bör du implementera en återförsöksprincip som exponentiell backoff enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Lägg till köer i IoT hub och flöde meddelandena till dem.

I det här avsnittet skapar både en Service Bus-kö och ett lagringskonto, Anslut dem till din IoT-hubb och konfigurera din IoT-hubb för att skicka meddelanden till kön baserat på förekomsten av en egenskap på meddelandet och alla meddelanden till lagringskontot. Mer information om hur du bearbeta meddelanden från Service Bus-köer finns [Kom igång med köer] [ lnk-sb-queues-node] och hur du hanterar lagring finns [komma igång med Azure Storage] [Azure Storage].

1. Skapa en Service Bus-kö som beskrivs i [Kom igång med köer][lnk-sb-queues-node]. Anteckna namnet på namnområdet och kön.

    > [!NOTE]
    > Service Bus-köer och ämnen som används som IoT-hubbslutpunkter inte får ha **sessioner** eller **dubblettidentifiering** aktiverat. Om något av dessa alternativ är aktiverade ändpunkt som **inte åtkomlig** i Azure-portalen.

1. Öppna din IoT-hubb i Azure-portalen och klicka på **slutpunkter**.

    ![Slutpunkter i IoT-hubb][30]

1. I den **slutpunkter** bladet, klickar du på **Lägg till** längst upp för att lägga till kön till din IoT-hubb. Namnet på slutpunkten **CriticalQueue** och markera med hjälp av listrutorna **Service Bus-kö**, Service Bus-namnrymd som kön finns och namnet på din kö. När du är klar klickar du på **OK** längst ned.  

    ![Lägga till en slutpunkt][31]

1. Klicka på **vägar** i din IoT-hubb. Klicka på **Lägg till** längst upp på bladet för att skapa en regel för vidarebefordran som skickar meddelanden till kön du just lagt till. 

    ![Lägga till en väg][34]

    Ange ett namn och välj **meddelanden** som datakällan. Välj **CriticalQueue** som en anpassad slutpunkt som routning regel slutpunkt och ange `level="critical"` som frågesträngen. Klicka på **spara** längst ned.

    ![Väg-information][32]

    Kontrollera återställningsplats vägen är inställd på **på**. Den här inställningen är standardkonfigurationen för en IoT-hubb.

    ![Fallback-väg][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Valfritt) Läsa från kön slutpunkten

I det här avsnittet skapar du en Python-konsolapp som läser kritiska meddelanden från IoT Service Bus. Se ytterligare information i [Kom igång med köer][lnk-sb-queues-node]. 

1. Öppna en kommandotolk och installera Azure IoT Hub Device SDK för Python enligt följande. Stäng kommandotolken efter installationen.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > För problem med att installera den **azure servicebus** paketet eller ytterligare alternativ för installation finns den [Python azure servicebus-paketet][lnk-python-service-bus].

1. Skapa en fil med namnet **ReadCriticalQueue.py**. Öppna filen i valfri Python-redigerare/IDE (till exempel IDLE).

1. Lägg till följande kod för att importera moduler som krävs från enheten SDK:

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Lägg till följande kod och Ersätt platshållarna med anslutningsdata för service bus:

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Lägg till följande kod för att ansluta till och läsa service bus: 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Slutligen lägger du till huvudfunktionen. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Spara och Stäng den **ReadCriticalQueue.py** fil. Nu är det dags att köra programmen.


## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

1. Öppna en kommandotolk och installera IoT Hub Explorer. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Kör följande kommando i kommandotolken för att börja övervaka ”enhet-till-molnet”-meddelanden från enheten. Använd din IoT Hubs anslutningssträng i platshållaren efter `--login`.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Öppna en ny kommandotolk och gå till den katalog som innehåller filen **SimulatedDevice.py**.

1. Kör filen **SimulatedDevice.py** som regelbundet skickar telemetridata till din IoT Hub. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Att köra den **ReadCriticalQueue** program i en kommandotolk eller shell navigerar du till **ReadCriticalQueue.py** filen och kör följande kommando:

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. Se enhetsmeddelanden på kommandotolken som kör IoT Hub Explorer från det föregående avsnittet. Se den `critical` meddelanden i den **ReadCriticalQueue** program.

    ![Meddelanden från enheten till molnet (Python)][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Valfritt) Lägg till lagringsbehållaren i IoT-hubb och flöde meddelanden till den

I det här avsnittet, skapa ett lagringskonto, ansluta till din IoT-hubb och konfigurera din IoT-hubb för att skicka meddelanden till kontot utifrån förekomsten av en egenskap i meddelandet. Mer information om hur du hanterar lagring finns [komma igång med Azure Storage][Azure Storage].

 > [!NOTE]
   > IoT-hubb konton som har skapats den _F1 kostnadsfri_ nivå är begränsade till en **Endpoint**. Om du inte är begränsad till en **Endpoint**, du kan konfigurera den **StorageContainer** förutom den **CriticalQueue** och kör både simulatneously.

1. Skapa ett lagringskonto som beskrivs i [Azure Storage-dokumentation][lnk-storage]. Anteckna namnet på kontot.

2. Öppna din IoT-hubb i Azure-portalen och klicka på **slutpunkter**.

3. I den **slutpunkter** bladet väljer den **CriticalQueue** slutpunkt och klicka på **ta bort**. Klicka på **Ja**, och klicka sedan på **Lägg till**. Namnet på slutpunkten **StorageContainer** och markera med hjälp av listrutorna **Azure Storage-behållare**, och skapa en **lagringskonto** och en **lagring behållaren**.  Anteckna namnen.  När du är klar klickar du på **OK** längst ned. 

 > [!NOTE]
   > IoT-hubb konton som har skapats den _F1 kostnadsfri_ nivå är begränsade till en **Endpoint**. Om du inte är begränsad till en **Endpoint**, du behöver inte ta bort den **CriticalQueue**.

4. Klicka på **vägar** i din IoT-hubb. Klicka på **Lägg till** längst upp på bladet för att skapa en regel för vidarebefordran som skickar meddelanden till kön du just lagt till. Välj **meddelanden** som datakällan. Ange `level="storage"` som villkor och välj **StorageContainer** som en anpassad slutpunkt som routning regeln slutpunkt. Klicka på **spara** längst ned.  

    Kontrollera återställningsplats vägen är inställd på **på**. Den här inställningen är standardkonfigurationen för en IoT-hubb.

1. Kontrollera att tillämpningsprogrammet tidigare **SimulatedDevice.py** fortfarande körs. 

1. I Azure-portalen går du till ditt lagringskonto under **Blob-tjänsten**, klicka på **Bläddra blobbar...** .  Välj din behållare, navigera till och klicka JSON-filen och på **hämta** att visa data.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen beskrivs hur du ska skicka meddelanden från enhet till moln med hjälp av meddelandet routningsfunktionen för IoT-hubb.

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Suite][lnk-suite].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

Läs mer om meddelanderoutning i IoT-hubb i [skicka och ta emot meddelanden med IoT-hubben][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Kom igång med IoT-hubb]: iot-hub-python-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot

[hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus