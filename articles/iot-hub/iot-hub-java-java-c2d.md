---
title: Moln till enhet meddelanden med Azure IoT Hub (Java) | Microsoft Docs
description: Hur du skickar meddelanden moln till enhet till en enhet från en Azure IoT-hubb med hjälp av Azure IoT-SDK för Java. Du kan ändra en simulerad enhetsapp för att ta emot meddelanden moln till enhet och ändra en backend-app för att skicka meddelanden moln till enhet.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 410a156d60aa9b17da9c36e043082c291eea4849
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808119"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Skicka meddelanden moln till enhet med IoT-hubb (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en helt hanterad tjänst som hjälper till att aktivera tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning tillbaka avslutas. Den [Kom igång med IoT-hubb] kursen visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och code en simulerad enhetsapp som skickar meddelanden från enhet till moln.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här kursen bygger på [Kom igång med IoT-hubb]. Den visar hur till:

* Skicka meddelanden moln till enhet på en enhet till IoT-hubb från din lösningens serverdel.
* Ta emot meddelanden moln till enhet på en enhet.
* Begära leverans bekräftelse från din lösningens serverdel (*feedback*) för meddelanden som skickas till en enhet från IoT-hubb.

Du hittar mer information om moln till enhet meddelanden i den [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].

I slutet av den här kursen kan köra du två Java-konsolappar:

* **simulerade enheten**, en modifierad version av app som skapats i [Kom igång med IoT-hubb], som ansluter till din IoT-hubb och tar emot meddelanden moln till enhet.
* **Skicka c2d meddelanden**, som skickar ett moln till enhet-meddelande till appen simulerade enheten via IoT-hubb och tar emot dess leverans bekräftelse.

> [!NOTE]
> IoT-hubben har SDK stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK för Azure IoT-enhet. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiekursen kod och vanligtvis Azure IoT Hub finns i [Azure IoT Developer Center].

För att kunna genomföra den här kursen behöver du följande:

* En fullständig fungerande version av den [Kom igång med IoT-hubb](iot-hub-java-java-getstarted.md) eller [processen IoT-hubb meddelanden från enhet till moln](tutorial-routing.md) kursen.
* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i appen simulerade enheten

I det här avsnittet kan du ändra den simulerade enhetsapp som du skapade i [Kom igång med IoT-hubb] ta emot meddelanden moln till enhet från IoT-hubben.

1. Öppna filen simulated-device\src\main\java\com\mycompany\app\App.java i en textredigerare.

2. Lägg till följande **MessageCallback** klass som en kapslad klass inuti den **App** klass. Den **köra** -metoden har anropats när enheten tar emot ett meddelande från IoT-hubb. I det här exemplet meddelar enheten alltid IoT-hubben att det har slutförts meddelandet:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Ändra den **huvudsakliga** metoden för att skapa en **AppMessageCallback** -instans och anrop i **setMessageCallback** metoden innan det öppnar klienten på följande sätt:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Om du använder HTTPS i stället för MQTT eller AMQP som transport, den **DeviceClient** instans söker efter meddelanden från IoT-hubb sällan (mindre än var 25: e minut). Mer information om skillnaderna mellan MQTT, AMQP och HTTPS-stöd och IoT-hubb begränsning finns på [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].

4. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande moln till enhet

I det här avsnittet skapar du en Java-konsolapp som skickar moln till enhet meddelanden i appen simulerade enheten. Du behöver enhets-ID på den enhet som du lade till i den [Kom igång med IoT-hubb] kursen. Du måste också anslutningssträngen IoT-hubb för din hubb hittar du i den [Azure Portal].

1. Skapa ett Maven-projekt som kallas **skicka c2d meddelanden** med följande kommando vid en kommandotolk. Observera att det här kommandot är ett långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigera till mappen Skicka c2d meddelanden i en kommandotolk.

3. Använd en textredigerare och öppna filen pom.xml i mappen Skicka c2d meddelanden och Lägg till följande beroende på den **beroenden** nod. Lägga till beroendet kan du använda den **iothub-java-service-klient** paketet i ditt program för att kommunicera med din IoT-hubb-tjänst:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning][lnk-maven-service-search].

4. Spara och stäng filen pom.xml.

5. Använd en textredigerare och öppna filen send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Lägg till följande klassnivå variabler till den **App** class, ersätter **{yourhubconnectionstring}** och **{yourdeviceid}** med värden din anges tidigare:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Ersätt den **huvudsakliga** metoden med följande kod. Den här koden ansluter till din IoT-hubb skickar ett meddelande till din enhet och väntar sedan en bekräftelse att enheten emot och bearbetas meddelandet:
   
    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Den här självstudiekursen implementerar inte några återförsöksprincip sätt. I produktionskod, bör du implementera försök principer (till exempel exponentiell backoff), enligt förslaget i MSDN-artikel [Hantering av tillfälligt fel].


9. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando börjar skicka telemetri för din IoT-hubb och lyssnar efter meddelanden från din hubb moln till enhet i en kommandotolk i mappen simulerade enhet:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Kör appen simulerade enheten][img-simulated-device]

2. Kör följande kommando för att skicka meddelandet moln till enhet och väntar på bekräftelse en feedback i en kommandotolk i mappen Skicka c2d meddelanden:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kör kommandot för att skicka meddelandet moln till enhet][img-send-command]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen beskrivs hur du skickar och tar emot meddelanden moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Fjärrövervaknings solution accelerator].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [Utvecklarhandbok för IoT-hubb].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Kom igång med IoT-hubb]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Utvecklarhandbok för IoT-hubb]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[Hantering av tillfälligt fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Azure IoT Fjärrövervaknings solution accelerator]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22