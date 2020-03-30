---
title: Ladda upp filer från enheter till Azure IoT Hub med Java | Microsoft-dokument
description: Så här laddar du upp filer från en enhet till molnet med Azure IoT-enheten SDK för Java. Överförda filer lagras i en Azure storage blob-behållare.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: fcc2013f67c6e91182979a9bcab683894088a1d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284530"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Ladda upp filer från enheten till molnet med IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i [send cloud-to-device-meddelanden med IoT Hub-självstudiekurs](iot-hub-java-java-c2d.md) för att visa hur du använder [filöverföringsfunktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till Azure [blob storage](../storage/index.yml). Självstudien visar hur du:

* Ge en enhet en Azure blob URI på ett säkert sätt för att ladda upp en fil.

* Använd IoT Hub-filöverföringsmeddelandena för att utlösa bearbetning av filen i appryggen.

Snabbstarten [Skicka telemetri från en enhet till en snabbstart för IoT-hubb](quickstart-send-telemetry-java.md) och [Skicka meddelanden från molnet till enheten med IoT Hub-självstudien](iot-hub-java-java-c2d.md) visar den grundläggande funktionen för ioT-hubb- och moln-till-enhet-meddelanden i IoT Hub. Med [självstudien Konfigurera meddelanderoutning med IoT Hub](tutorial-routing.md) beskrivs ett sätt att lagra meddelanden från enhet till moln på ett tillförlitligt sätt i Azure-blob-lagring. I vissa fall kan du dock inte enkelt mappa de data som dina enheter skickar till de relativt små meddelanden från enhet till moln som IoT Hub accepterar. Ett exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibrationsdata som provtas med hög frekvens
* Någon form av förbearbetade data.

Dessa filer är vanligtvis batchbearbetning i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stacken. När du behöver upland-filer från en enhet kan du fortfarande använda säkerheten och tillförlitligheten hos IoT Hub.

I slutet av den här självstudien kör du två Java-konsolappar:

* **simulerad enhet**, en modifierad version av appen som skapats i [Skicka meddelanden från molnet till enheten med IoT Hub] självstudiekurs. Den här appen laddar upp en fil till lagring med hjälp av en SAS URI som tillhandahålls av din IoT-hubb.

* **läsa-fil-ladda upp-meddelande**, som tar emot filuppladdning meddelanden från din IoT-hubb.

> [!NOTE]
> IoT Hub stöder många enhetsplattformar och -språk (inklusive C, .NET och Javascript) via Azure IoT-enhetSDK:er. Se [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) för steg-för-steg-instruktioner om hur du ansluter din enhet till Azure IoT Hub.

## <a name="prerequisites"></a>Krav

* [Java SE Utveckling Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Se till att du väljer **Java 8** under **Långsiktigt stöd** för att komma till nedladdningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhetsapp

I det här avsnittet ändrar du enhetsappen som du skapade i [Skicka meddelanden från molnet till enheten med IoT Hub](iot-hub-java-java-c2d.md) för att ladda upp en fil till IoT-hubben.

1. Kopiera en bildfil `simulated-device` till mappen `myimage.png`och byt namn på den .

2. Öppna filen med hjälp `simulated-device\src\main\java\com\mycompany\app\App.java` av en textredigerare.

3. Lägg till variabeldeklarationen i **klassen App:**

    ```java
    private static String fileName = "myimage.png";
    ```

4. Om du vill bearbeta motringningsmeddelanden för filöverföringsstatus lägger du till följande kapslade klass i **klassen App:**

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Om du vill ladda upp bilder till IoT Hub lägger du till följande metod i **klassen App** för att ladda upp bilder till IoT Hub:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Ändra **huvudmetoden** för att anropa **metoden uploadFile** enligt följande utdrag:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Använd följande kommando för att skapa appen **med simulerade enheter** och kontrollera om det finns fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en serverningstjänst för att ta emot meddelanden om filöverföring från IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md). För att ta emot meddelanden om filöverföring behöver tjänsten behörigheten **för tjänsten ansluter.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om filöverföring

I det här avsnittet skapar du en Java-konsolapp som tar emot meddelanden om filöverföring från IoT Hub.

1. Skapa ett Maven-projekt som kallas **read-file-upload-notification** med följande kommando i kommandotolken. Observera att det här kommandot är ett enda, långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigera till den nya `read-file-upload-notification` mappen i kommandotolken.

3. Öppna `pom.xml` filen i `read-file-upload-notification` mappen med hjälp av en textredigerare och lägg till följande beroende i **beroendenoden.** Genom att lägga till beroendet kan du använda **iothub-java-service-klientpaketet** i ditt program för att kommunicera med din IoT-hubbtjänst:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Spara och `pom.xml` stäng filen.

5. Öppna filen med hjälp `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` av en textredigerare.

6. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{Your IoT Hub connection string}` platshållarvärdet med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben:](#get-the-iot-hub-connection-string)

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Om du vill skriva ut information om filöverföringen till konsolen lägger du till följande kapslade klass i **klassen App:**

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Om du vill starta tråden som lyssnar efter filöverföringsmeddelanden lägger du till följande kod i **huvudmetoden:**

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Spara och `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` stäng filen.

11. Använd följande kommando för att skapa appen **läs-fil-ladda upp meddelanden** och kontrollera om det finns fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

Kör följande kommando `read-file-upload-notification` i en kommandotolk i mappen:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Kör följande kommando `simulated-device` i en kommandotolk i mappen:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Följande skärmbild visar utdata från appen **med simulerade enheter:**

![Utdata från appen simulerad enhet](media/iot-hub-java-java-upload/simulated-device.png)

Följande skärmbild visar utdata från appen **läsfil-ladda upp meddelanden:**

![Utdata från appen läsa-fil-ladda upp meddelanden](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den uppladdade filen i lagringsbehållaren som du konfigurerade:

![Laddad fil](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder filöverföringsfunktionerna i IoT Hub för att förenkla filuppladdningar från enheter. Du kan fortsätta att utforska IoT-hubbfunktioner och -scenarier med följande artiklar:

* [Skapa ett IoT-nav programmässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK:er](iot-hub-devguide-sdks.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Simulera en enhet med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
