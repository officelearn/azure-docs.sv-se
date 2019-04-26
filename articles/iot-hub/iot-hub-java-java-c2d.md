---
title: Meddelanden från molnet till enheten med Azure IoT Hub (Java) | Microsoft Docs
description: Hur du skickar meddelanden från moln till enhet till en enhet från Azure IoT hub med Azure IoT SDK för Java. Du kan ändra en simulerad enhetsapp för att ta emot meddelanden från moln till enhet och ändra en backend-app för att skicka meddelanden från moln-till-enhet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 9032a6903833ba819e09fd1ca11cd6b43d5485cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399495"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Skicka meddelanden från moln till enhet med IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en helt hanterad tjänst som hjälper dig att aktivera pålitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen. Den [skicka telemetri från en enhet till en Hub (Java)](quickstart-send-telemetry-java.md) kursen visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och koda en simulerad enhetsapp som skickar meddelanden från enheten till molnet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [skickar telemetri från en enhet till IoT Hub (Java)](quickstart-send-telemetry-java.md). Den visar hur du gör följande:

* Skicka meddelanden från moln till enhet på en enhet via IoT Hub från lösningens backend-servrar.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från lösningens backend-servrar, begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information på [meddelanden från molnet till enheten i IoT Hub developer guide](iot-hub-devguide-messaging.md).

I slutet av den här kursen kan du köra två Java-konsolappar:

* **simulated-device**, en modifierad version av appen som skapats i [skicka telemetri från en enhet till en Hub (Java)](quickstart-send-telemetry-java.md), som ansluter till din IoT hub och tar emot meddelanden från molnet till enheten.

* **Skicka-c2d-meddelanden**, som skickar ett moln-till-enhet-meddelande till den simulerade enhetsappen via IoT Hub och sedan ta emot dess leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner om hur du ansluter enheten till den här självstudien kod och vanligen på Azure IoT Hub finns i den [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).

För att kunna genomföra den här kursen behöver du följande:

* En fullständig fungerande version av den [skicka telemetri från en enhet till en Hub (Java)](quickstart-send-telemetry-java.md) eller [konfigurera meddelanderoutning med IoT Hub](tutorial-routing.md) självstudien.

* Senaste [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Ett aktivt Azure-konto. Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen

I det här avsnittet ska du ändra den simulerade enhetsappen som du skapade i [skicka telemetri från en enhet till en Hub (Java)](quickstart-send-telemetry-java.md) att ta emot meddelanden från moln till enhet från IoT hub.

1. Öppna filen simulated-device\src\main\java\com\mycompany\app\App.java i en textredigerare.

2. Lägg till följande **MessageCallback** klass som en kapslad klass inuti den **App** klass. Den **köra** metoden anropas när enheten tar emot ett meddelande från IoT Hub. I det här exemplet meddelar enheten alltid IoT-hubben att den har slutförts meddelandet:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Ändra den **huvudsakliga** metod för att skapa en **AppMessageCallback** instans och anropa den **setMessageCallback** metoden innan det öppnar klienten på följande sätt:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Om du använder HTTPS i stället för MQTT eller AMQP som transport, den **DeviceClient** instans söker efter meddelanden från IoT Hub sällan (mindre än var 25: e minut). Mer information om skillnaderna mellan MQTT-, AMQP- och HTTPS-stöd och IoT Hub-begränsning finns i den [meddelanden i IoT Hub developer guiden](iot-hub-devguide-messaging.md).

4. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Skicka ett moln-till-enhet-meddelande

I det här avsnittet skapar du en Java-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID för enheten som du lade till i den [skicka telemetri från en enhet till en Hub (Java)](quickstart-send-telemetry-java.md) Snabbstart. Du måste också IoT Hub-anslutningssträngen för hubben som du hittar i den [Azure-portalen](https://portal.azure.com).

1. Skapa ett Maven-projekt som heter **skicka-c2d-meddelanden** med följande kommando i Kommandotolken. Observera att det här kommandot är ett enda långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigera till mappen Skicka-c2d-meddelanden i en kommandotolk.

3. Använd en textredigerare och öppna filen pom.xml i mappen Skicka-c2d-meddelanden och Lägg till följande beroende till den **beroenden** noden. Om du lägger till beroendet kan du använda den **iothub-java-service-client** paketet i ditt program kan kommunicera med IoT hub-tjänsten:

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

5. Använd en textredigerare och öppna filen send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Lägg till de följande variablerna på klassnivå till den **App** klassen ersätta **{yourhubconnectionstring}** och **{yourdeviceid}** med värden noterade tidigare:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Ersätt den **huvudsakliga** metoden med följande kod. Den här koden ansluter till din IoT-hubb, skickar ett meddelande till din enhet och väntar sedan på en bekräftelse att enheten tas emot och bearbetas meddelandet:
   
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
    > Den här självstudien implementerar inte någon återförsöksprincip sätt. I produktionskoden bör du implementera principer för omförsök (till exempel exponentiell backoff) vilket rekommenderas i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).


9. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando för att börja skicka telemetri till din IoT-hubb och att lyssna efter meddelanden från molnet till enheten skickas från din hubb i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Kör den simulerade enhetsappen](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Kör följande kommando för att skicka ett moln-till-enhet-meddelande och vänta tills en feedback-bekräftelse i Kommandotolken i mappen Skicka-c2d-meddelanden:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kör kommando för att skicka meddelandet moln till enhet](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Nästa steg

I de här självstudierna lärde du dig att skicka och ta emot meddelanden från moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt som använder IoT Hub finns i [Azure IoT-Lösningsacceleratorer](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns det [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).