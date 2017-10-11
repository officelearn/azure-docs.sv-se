---
title: Azure IoT Hub-enhet till moln meddelanden (Java) | Microsoft Docs
description: "Så här bearbeta meddelanden från IoT-hubb enhet till moln genom att använda regler för Routning och anpassade slutpunkter för att skicka meddelanden till andra backend-tjänster."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: d1aca8f39e305105d4ec9f63fbe7bee95487e294
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>Bearbeta meddelanden från enhet till moln IoT-hubb (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT Hub är en helt hanterad tjänst som gör tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning för serverdel. Andra självstudier ([Kom igång med IoT-hubb] och [meddelanden moln till enhet med IoT-hubben][lnk-c2d]) visar hur du använder grundläggande enheten till molnet och moln till enhet meddelandefunktioner för IoT-hubb.

Den här kursen bygger på koden som visas i den [Kom igång med IoT-hubb] kursen, och visar hur du använder meddelanderoutning vill bearbeta meddelanden från enhet till moln i ett skalbart sätt. Kursen visar hur du kan bearbeta meddelanden som kräver omedelbara åtgärder från lösningens serverdel. En enhet kan till exempel skicka larmmeddelandet som utlöser lägga till ett ärende i ett CRM-system. Däremot feed bara datapunkt meddelanden till en analytics-motor. Temperatur telemetri från en enhet som ska lagras för senare analys är till exempel en datapunkt meddelande.

I slutet av den här kursen kan köra du tre Java-konsolappar:

* **simulerade enheten**, en modifierad version av app som skapats i den [Kom igång med IoT-hubb] självstudiekursen skickar meddelanden från enhet till moln datapunkt varje sekund och interaktiva enhet till moln meddelanden var 10: e sekund . Den här appen använder AMQP-protokollet för att kommunicera med IoT-hubb.
* **Läs-d2c-meddelanden** visar telemetri som skickats av din enhetsapp.
* **Läs kritisk kö** tar bort viktiga meddelanden från Service Bus-kö som är kopplade till IoT-hubben.

> [!NOTE]
> IoT-hubben har SDK stöd för många vilka plattformar och språk, inklusive C, Java och JavaScript. Instruktioner om hur du ersätter enheten i den här självstudiekursen med en fysisk enhet och ansluta enheter till en IoT-hubb finns i [Azure IoT Developer Center].

För att kunna genomföra den här kursen behöver du följande:

* En fullständig fungerande version av den [Kom igång med IoT-hubb] kursen.
* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto] [lnk-kostnadsfria-utvärderingsversion] på bara några minuter.)

Du bör ha vissa grundläggande kunskaper om [Azure Storage] och [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Skicka interaktiva meddelanden från en enhetsapp
I det här avsnittet kan du ändra appen för enheter som du skapade i den [Kom igång med IoT-hubb] kursen att ibland skicka meddelanden som kräver omedelbar bearbetning.

1. Använd en textredigerare för att öppna filen simulated-device\src\main\java\com\mycompany\app\App.java. Den här filen innehåller koden för den **simulerade enheten** app som du skapade i den [Kom igång med IoT-hubb] kursen.

2. Ersätt den **MessageSender** klassen med följande kod:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Den här metoden lägger slumpmässigt till egenskapen `"level": "critical"` på meddelanden som skickas av enheten, som simulerar ett meddelande som kräver omedelbara åtgärder med programmet serverdel. Programmet Överför denna information i egenskaperna för meddelandet i stället för i meddelandetexten, så att IoT-hubb kan vidarebefordra meddelandet till rätt meddelandets mål.
   
   > [!NOTE]
   > Du kan använda meddelandeegenskaper skicka meddelanden för olika scenarier, inklusive kall sökväg bearbetning, förutom varm sökväg exemplet som visas här.

2. Spara och stäng filen simulated-device\src\main\java\com\mycompany\app\App.java.

    > [!NOTE]
    > För enkelhetens skull implementerar inte den här självstudiekursen princip försök igen. I produktionskod, bör du implementera en återförsöksprincip som exponentiell backoff enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].

3. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Lägg till en kö i IoT-hubb och flöde meddelanden till den

I det här avsnittet skapar du en Service Bus-kö, ansluta till din IoT-hubb och konfigurera din IoT-hubb för att skicka meddelanden till kön baserat på förekomsten av en egenskap i meddelandet. Mer information om hur du bearbeta meddelanden från Service Bus-köer finns [Kom igång med köer][lnk-sb-queues-java].

1. Skapa en Service Bus-kö som beskrivs i [Kom igång med köer][lnk-sb-queues-java]. Anteckna namnet på namnområdet och kön.

2. Öppna din IoT-hubb i Azure-portalen och klicka på **slutpunkter**.

    ![Slutpunkter i IoT-hubb][30]

3. I den **slutpunkter** bladet, klickar du på **Lägg till** längst upp för att lägga till kön till din IoT-hubb. Namnet på slutpunkten **CriticalQueue** och markera med hjälp av listrutorna **Service Bus-kö**, Service Bus-namnrymd som kön finns och namnet på din kö. När du är klar klickar du på **spara** längst ned.

    ![Lägga till en slutpunkt][31]

4. Klicka på **vägar** i din IoT-hubb. Klicka på **Lägg till** längst upp på bladet för att skapa en regel för vidarebefordran som skickar meddelanden till kön du just lagt till. Välj **DeviceTelemetry** som datakällan. Ange `level="critical"` som villkor och välj den kö som du just har lagt till som en anpassad slutpunkt som routning regeln slutpunkt. När du är klar klickar du på **spara** längst ned.

    ![Lägga till en väg][32]

    Kontrollera återställningsplats vägen är inställd på **på**. Den här inställningen är standardkonfigurationen för en IoT-hubb.

    ![Fallback-väg][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Valfritt) Läsa från kön slutpunkten

Du kan du läsa meddelanden från kön slutpunkten genom att följa instruktionerna i [Kom igång med köer][lnk-sb-queues-java]. Namnge appen **Läs kritisk kö**.

## <a name="run-the-applications"></a>Köra programmen

Nu är du redo att köra tre program.

1. Att köra den **lästa d2c meddelanden** program i en kommandotolk eller shell navigera till mappen Läs d2c och kör följande kommando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Kör Läs-d2c-meddelanden][readd2c]

2. Att köra den **Läs kritisk kö** program i en kommandotolk eller shell navigera till mappen Läs kritiska kön och kör följande kommando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Kör Läs kritiska meddelanden][readqueue]

3. Att köra den **simulerade enheten** i en kommandotolk eller shell appen, navigera till mappen simulerade enheten och kör följande kommando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Kör simulerade enheten][simulateddevice]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen beskrivs hur du ska skicka meddelanden från enhet till moln med hjälp av meddelandet routningsfunktionen för IoT-hubb.

Den [hur du skickar meddelanden moln till enhet med IoT-hubben] [ lnk-c2d] visar hur du kan skicka meddelanden till dina enheter från din lösningens serverdel.

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Suite][lnk-suite].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

Läs mer om meddelanderoutning i IoT-hubb i [skicka och ta emot meddelanden med IoT-hubben][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Kom igång med IoT-hubb]: iot-hub-java-java-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Hantering av tillfälligt fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
