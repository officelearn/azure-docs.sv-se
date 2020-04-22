---
title: Moln-till-enhet-meddelanden med Azure IoT Hub (Java) | Microsoft-dokument
description: Så här skickar du meddelanden från molnet till enheten till en enhet från en Azure IoT-hubb med Azure IoT-SDK:er för Java. Du ändrar en simulerad enhetsapp för att ta emot meddelanden från molnet till enheten och ändrar en backend-app för att skicka meddelanden från molnet till enheten.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e16d0ed264f32746c11d89e88ea1e67f9383b773
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732523"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Skicka meddelanden från molnet till enheten med IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsbakdel. [Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md) visar hur du skapar en IoT-hubb, etablerar en enhetsidentitet i den och kodar en simulerad enhetsapp som skickar meddelanden från enhet till moln.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md). Det visar hur du gör följande:

* Från din lösningsbaksida skickar du meddelanden från molnet till enheten till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från din lösningsbaksida, begär leveransbekräftelse *(feedback)* för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om [meddelanden från molnet till enheten i utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två Java-konsolappar:

* **simulerad enhet**, en modifierad version av appen som skapats i Skicka [telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **send-c2d-meddelanden**, som skickar ett meddelande från molnet till enheten till den simulerade enhetsappen via IoT Hub, och sedan får sin leveransbekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och -språk (inklusive C, Java, Python och Javascript) via Azure IoT-enhetSDK:er. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiens kod och i allmänhet till Azure IoT Hub finns i [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Krav

* En komplett fungerande version av [send telemetri från en enhet till en snabbstart för IoT-hubb](quickstart-send-telemetry-java.md) eller guiden [Konfigurera meddelanderoutning med IoT](tutorial-routing.md) Hub-självstudiekurs.

* [Java SE Utveckling Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Se till att du väljer **Java 8** under **Långsiktigt stöd** för att komma till nedladdningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen

I det här avsnittet ändrar du den simulerade enhetsappen som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md) för att ta emot meddelanden från molnet till enheten från IoT-hubben.

1. Öppna filen simulated-device\src\main\java\com\mycompany\app\App.java i en textredigerare.

2. Lägg till följande **MessageCallback-klass** som en kapslad klass i **klassen App.** **Körningsmetoden** anropas när enheten tar emot ett meddelande från IoT Hub. I det här exemplet meddelar enheten alltid IoT-hubben att meddelandet har slutförts:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Ändra **huvudmetoden** för att skapa en **AppMessageCallback-instans** och anropa **metoden setMessageCallback** innan klienten öppnas på följande sätt:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Om du använder HTTPS i stället för MQTT eller AMQP som transport söker **DeviceClient-instansen** sällan efter meddelanden från IoT Hub (mindre än var 25:e minut). Mer information om skillnaderna mellan stöd för MQTT, AMQP och HTTPS och begränsning av IoT Hub finns i [meddelandeavsnittet i utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).

4. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en serverd-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md). Om du vill skicka meddelanden från molnet till enheten behöver tjänsten behörigheten **för tjänsten ansluta.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från molnet till enheten

I det här avsnittet skapar du en Java-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID:t för den enhet som du lade till i [skicka telemetrin från en enhet till en snabbstart för IoT-hubb.](quickstart-send-telemetry-java.md) Du behöver också anslutningssträngen för IoT-hubben som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

1. Skapa ett Maven-projekt som kallas **send-c2d-meddelanden** med följande kommando i kommandotolken. Observera att det här kommandot är ett enda, långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. I kommandotolken navigerar du till den nya mappen skicka-c2d-meddelanden.

3. Öppna filen pom.xml i mappen send-c2d-messages med hjälp av en textredigerare och lägg till följande beroende i **beroendenoden.** Genom att lägga till beroendet kan du använda **iothub-java-service-klientpaketet** i ditt program för att kommunicera med din IoT-hubbtjänst:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Spara och stäng filen pom.xml.

5. Öppna filen send-c2d-messages\src\main\java\com\mycompany\app\App.java med hjälp av en textredigerare.

6. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Lägg till följande variabler på klassnivå i **klassen App** och ersätt **{yourhubconnectionstring}** och **{yourdeviceid}** med de värden som du noterade tidigare:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Ersätt **huvudmetoden** med följande kod. Den här koden ansluter till din IoT-hubb, skickar ett meddelande till enheten och väntar sedan på en bekräftelse på att enheten tog emot och bearbetade meddelandet:

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
    > För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktionskoden bör du implementera principer för återförsök (till exempel exponentiell backoff), som föreslås i artikeln [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

9. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. I en kommandotolk i mappen simulerad enhet kör du följande kommando för att börja skicka telemetri till IoT-hubben och lyssna efter meddelanden från molnet till enheten som skickas från hubben:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Köra den simulerade enhetsappen](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Vid en kommandotolk i mappen send-c2d-messages kör du följande kommando för att skicka ett meddelande från molnet till enheten och vänta på en feedbackbekräftelse:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kör kommandot för att skicka meddelandet från molnet till enheten](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skicka och ta emot meddelanden från molnet till enheten.

Information om hur du ser exempel på kompletta heltäckande lösningar som använder IoT Hub finns i [Azure IoT Solution Accelerators](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
