---
title: Ladda upp filer från enheter till Azure IoT Hub med Java | Microsoft Docs
description: Hur du överför filer från en enhet till molnet med Azure IoT-enhetens SDK för Java. Överförda filer lagras i en Azure storage blob-behållare.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 57faff3a95e5b4ccdbc44cb7adb77d34694042c9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220396"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Ladda upp filer från din enhet till molnet med IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i den [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-java-java-c2d.md) självstudien för att visa dig hur du använder den [filen ladda upp funktionerna i IoT Hub](iot-hub-devguide-file-upload.md) att överföra en fil till [Azure-blob Storage](../storage/index.yml). Självstudien visar hur du:

- Ange en enhet på ett säkert sätt med en Azure blob-URI: N för att ladda upp en fil.
- Använd IoT Hub filen ladda upp meddelanden för att utlösa fil i din app-serverdel.

Den [Kom igång med IoT Hub](quickstart-send-telemetry-java.md) och [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-java-java-c2d.md) självstudiekurser visar de grundläggande funktionerna av enhet till moln och från moln till enhet meddelanden i IoT Hub. Den [processen enhet till moln-meddelanden](tutorial-routing.md) självstudien beskrivs ett sätt att pålitligt lagra meddelanden från enheten till molnet i Azure blob storage. Men i vissa fall kan inte du enkelt mappa enheterna skickar till relativt liten enhet-till-moln-meddelanden som IoT-hubb tar emot data. Exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibrationer data samplas med hög frekvens
* Någon form av förbearbetade data.

Dessa filer är vanligtvis bearbetas i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stack. När du behöver upland filer från en enhet, kan du fortfarande använda säkerheten och pålitligheten för IoT Hub.

I slutet av den här kursen kan du köra två Java-konsolappar:

* **simulated-device**, en modifierad version av appen skapades i självstudien [skicka molnet till enhet-meddelanden med IoT Hub]. Den här appen överför en fil till storage med hjälp av en SAS-URI som tillhandahålls av din IoT-hubb.
* **Läs –--filuppladdningsmeddelande**, som tar emot filen ladda upp meddelanden från IoT hub.

> [!NOTE]
> IoT-hubb har stöd för många enhetsplattformar och språk (inklusive C, .NET och Javascript) via SDK: er för Azure IoT-enheter. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter din enhet till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en app för enheter

I det här avsnittet ska du ändra app för enheter som du skapade i [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-java-java-c2d.md) att överföra en fil till IoT hub.

1. Kopiera en fil till den `simulated-device` mappen och Byt namn på den `myimage.png`.

1. Använd en textredigerare och öppna den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

1. Lägg till variabeldeklaration till den **App** klass:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Om du vill bearbeta filen överför motringning statusmeddelanden, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Om du vill kunna överföra bilder till IoT Hub, lägger du till följande metod för att den **App** klassen för att kunna överföra bilder till IoT Hub:

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

1. Ändra den **huvudsakliga** metod för att anropa den **uploadFile** metod som du ser i följande kodavsnitt:

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

1. Använd följande kommando för att skapa den **simulated-device** appen och Sök efter fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Ta emot ett filuppladdningsmeddelande

I det här avsnittet skapar du en Java-konsolapp som tar emot filen ladda upp meddelanden från IoT Hub.

Du behöver den **iothubowner** anslutningssträngen för din IoT-hubb att slutföra det här avsnittet. Du hittar anslutningssträngen i den [Azure-portalen](https://portal.azure.com/) på den **princip för delad åtkomst** bladet.

1. Skapa ett Maven-projekt som heter **Läs –--filuppladdningsmeddelande** med följande kommando i Kommandotolken. Observera att det här kommandot är ett enda långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. I Kommandotolken, gå till den nya `read-file-upload-notification` mapp.

1. Använd en textredigerare och öppna den `pom.xml` fil i den `read-file-upload-notification` mapp och Lägg till följande beroende till den **beroenden** noden. Om du lägger till beroendet kan du använda den **iothub-java-service-client** paketet i ditt program kan kommunicera med IoT hub-tjänsten:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Spara och Stäng den `pom.xml` filen.

1. Använd en textredigerare och öppna den `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` filen.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Lägg till de följande variablerna på klassnivå till den **App** klass:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. Om du vill skriva ut information om själva filuppladdningen till konsolen, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
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

1. Om du vill starta en tråd som lyssnar efter meddelanden för uppladdning av filen, lägger du till följande kod till den **huvudsakliga** metoden:

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

1. Spara och Stäng den `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` filen.

1. Använd följande kommando för att skapa den **Läs –--filuppladdningsmeddelande** appen och Sök efter fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

I en kommandotolk i den `read-file-upload-notification` mapp, kör du följande kommando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

I en kommandotolk i den `simulated-device` mapp, kör du följande kommando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

I följande skärmbild visas utdata från den **simulated-device** app:

![Utdata från app för simulerade enheter](media/iot-hub-java-java-upload/simulated-device.png)

I följande skärmbild visas utdata från den **Läs –--filuppladdningsmeddelande** app:

![Utdata från Läs –--filuppladdningsmeddelande app](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den överförda filen i storage-behållare som du har konfigurerat:

![Uppladdad fil](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du använder filen ladda upp funktionerna i IoT Hub för att förenkla filöverföringar från enheter. Du kan fortsätta att utforska IoT hub funktioner och scenarier i följande artiklar:

* [Skapa en IoT hub programmässigt][lnk-create-hub]
* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT SDK: er][lnk-sdks]

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Simulera en enhet med IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot

[Azure Storage]:../storage/common/storage-quickstart-create-account.md
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


