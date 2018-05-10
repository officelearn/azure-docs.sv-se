---
title: Överföra filer från enheter till Azure IoT-hubb med Java | Microsoft Docs
description: Hur du överför filer från en enhet till molnet med Azure IoT-enhet SDK för Java. Överförda filer lagras i ett Azure storage blob-behållaren.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 794ebd3b2d25f6b7d5dcb86b0834380fce9b9a27
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Ladda upp filer från enheten till molnet med IoT-hubb

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här kursen bygger på koden i den [meddelanden moln till enhet med IoT-hubben](iot-hub-java-java-c2d.md) vägledning till hur du använder den [filen överför funktionerna i IoT-hubb](iot-hub-devguide-file-upload.md) att överföra en fil till [Azure blob lagring](../storage/index.yml). I kursen får du veta hur till:

- Ange en enhet på ett säkert sätt med en Azure blob-URI för att överföra en fil.
- Använd IoT-hubb filen överför meddelanden för att utlösa bearbetning av filen i din app-serverdelen.

Den [Kom igång med IoT-hubb](iot-hub-java-java-getstarted.md) och [meddelanden moln till enhet med IoT-hubben](iot-hub-java-java-c2d.md) självstudiekurser visar grundläggande enhet till moln och moln till enhet meddelandetjänsten funktioner i IoT-hubb. Den [meddelanden processen enhet till moln](iot-hub-java-java-process-d2c.md) självstudiekursen beskriver ett sätt att på ett tillförlitligt sätt lagra meddelanden från enhet till moln i Azure blob storage. Men i vissa fall kan du enkelt mappa data enheterna skickar till relativt liten enhet till moln meddelanden som accepterar IoT-hubb. Exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibration data samplas vid hög frekvens
* Någon form av förbearbetade data.

Dessa filer finns vanligtvis i molnet med hjälp av verktyg som bearbetas i batch [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stacken. När du behöver upland filer från en enhet kan du fortfarande använda säkerheten och pålitligheten för IoT-hubb.

I slutet av den här kursen kan du köra två appar som Java-konsolen:

* **simulerade enheten**, en modifierad version av app som skapats i kursen [skicka moln till enhet meddelanden med IoT-hubb]. Den här appen överför en fil till lagring med hjälp av en SAS-URI som tillhandahålls av din IoT-hubb.
* **Läs filen-överför-meddelande**, som tar emot filen överföra meddelanden från din IoT-hubb.

> [!NOTE]
> IoT-hubb stöder många vilka plattformar och språk (inklusive C, .NET och Javascript) via Azure IoT-enhet SDK: er. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter enheten till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Överför en fil från en enhetsapp

I det här avsnittet kan du ändra appen för enheter som du skapade i [meddelanden moln till enhet med IoT-hubben](iot-hub-java-java-c2d.md) att överföra en fil till IoT-hubben.

1. Kopiera en fil till den `simulated-device` mappen och Byt namn på den `myimage.png`.

1. Med en textredigerare, öppna den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

1. Lägg till variabeldeklarationen till den **App** klass:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Lägg till följande kapslade klassen för att bearbeta filen överför motringning statusmeddelanden den **App** klass:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Lägg till följande metod för att ladda upp bilder till IoT-hubben i **App** klassen för att ladda upp bilder till IoT-hubb:

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

1. Ändra den **huvudsakliga** metod för att anropa den **uploadFile** metod som visas i följande utdrag:

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

1. Använd följande kommando för att skapa den **simulerade enheten** appen och Sök efter fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om överföringen

I det här avsnittet skapar du en Java-konsolapp som tar emot filen överföra meddelanden från IoT-hubb.

Du behöver den **iothubowner** anslutningssträngen för din IoT-hubb att slutföra det här avsnittet. Du kan hitta anslutningssträngen i den [Azure-portalen](https://portal.azure.com/) på den **princip för delad åtkomst** bladet.

1. Skapa ett Maven-projekt som kallas **läsa filen-överför-meddelande** med följande kommando vid en kommandotolk. Observera att det här kommandot är ett långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. Kommandotolken, gå till den nya `read-file-upload-notification` mapp.

1. Med en textredigerare, öppna den `pom.xml` filen i den `read-file-upload-notification` mapp och Lägg till följande beroende på den **beroenden** nod. Lägga till beroendet kan du använda den **iothub-java-service-klient** paketet i ditt program för att kommunicera med din IoT-hubb-tjänst:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot tjänstklienten** med [Maven Sök](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Spara och Stäng den `pom.xml` filen.

1. Med en textredigerare, öppna den `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` filen.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Lägg till följande klassnivå variabler till den **App** klass:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. Om du vill skriva ut information om filöverföringen till konsolen lägger du till följande kapslade klassen för att den **App** klass:

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

1. Lägg till följande kod för att starta tråd som lyssnar efter filen överför meddelanden i **huvudsakliga** metoden:

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

1. Använd följande kommando för att skapa den **läsa filen-överför-meddelande** appen och Sök efter fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

Vid en kommandotolk i den `read-file-upload-notification` mapp, kör du följande kommando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Vid en kommandotolk i den `simulated-device` mapp, kör du följande kommando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Följande skärmbild visar utdata från den **simulerade enheten** app:

![Utdata från simulerade enheten app](media/iot-hub-java-java-upload/simulated-device.png)

Följande skärmbild visar utdata från den **läsa filen-överför-meddelande** app:

![Utdata från Läs-filen-överför-meddelandeprogram](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den överförda filen i vilken lagringsbehållare som du har konfigurerat:

![Överförda filen](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du använder filen överför funktionerna i IoT-hubb för att förenkla filöverföringar från enheter. Du kan fortsätta att utforska IoT-hubb funktioner och scenarier i följande artiklar:

* [Skapa en IoT-hubb programmässigt][lnk-create-hub]
* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT-SDK][lnk-sdks]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Simulera en enhet med IoT kant][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


