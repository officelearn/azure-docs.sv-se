---
title: Enheten firmware-uppdatering med Azure IoT Hub (Java/Java) | Microsoft Docs
description: Hur du använder hantering av enheter på Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Du kan använda Azure IoT-enhet SDK för Java att implementera en simulerad enhetsapp och för att implementera ett service-appen som utlöser firmware-uppdatering.
services: iot-hub
documentationcenter: java
author: msebolt
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo
ms.openlocfilehash: 19d9cc6722d65d8bcd76d73fda0f24a598479647
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Använd enhetshantering för att starta en enhet på en firmware-uppdatering (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudiekursen du sett hur du använder den [enheten dubbla] [ lnk-devtwin] och [direkt metoder] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här kursen visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering använder samma IoT-hubb primitiver.  Det här mönstret används i uppdatering av inbyggd hanteringsprogramvara för den [hallon Pi enheten implementering exempel][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Java-konsolapp som anropar den **firmwareUpdate** direkt metod på appen simulerade enheten via din IoT-hubb.
* Skapa en Java-konsolapp som simulerar enheten och implementerar den **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen inbyggd programvara, laddar ned avbildningen av inbyggd programvara och slutligen använder inbyggd avbildningen. Under varje steg i uppdateringen använder enheten rapporterade egenskaperna för att rapportera förlopp.

I slutet av den här kursen har du två appar som Java-konsolen:

**firmware-uppdatering**, anropar en direkt metod på den simulerade enheten, visas svaret och regelbundet visar rapporterade egenskapen uppdateringar

**simulerade enheten**, ansluter till din IoT-hubb med tidigare skapade enhetens identitet, tar emot firmwareUpdate direkt metodanropet och körs via en firmware-uppdatering simulering

För att kunna genomföra den här kursen behöver du följande:

* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en Java-konsolapp som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder direkta metoden för att initiera uppdateringen och använder enheten dubbla frågor för att hämta status för aktiva firmware-uppdatering med jämna mellanrum.

1. Skapa en tom mapp som kallas VB-get-started.

1. Skapa ett Maven-projekt som kallas i mappen VB-get-started **firmware-uppdatering** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Navigera till mappen firmware-uppdatering i en kommandotolk.

1. Använd en textredigerare och öppna filen pom.xml i mappen firmware-uppdatering och Lägg till följande beroende på den **beroenden** nod. Detta beroende kan du använda iot-service-klient-paketet i appen kommunicerar med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot tjänstklienten** med [Maven Sök](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Lägg till följande **skapa** noden efter den **beroenden** nod. Den här konfigurationen instruerar Maven använda Java 1.8 för att skapa appen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Spara och stäng filen pom.xml.

1. Använd en textredigerare och öppna källfilen firmware-update\src\main\java\com\mycompany\app\App.java.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt **{youriothubconnectionstring}** med din IoT-hubb-anslutningssträng som du antecknade i den *skapar en IoT-hubb* avsnitt:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. För att implementera en metod som läser rapporterade egenskaper från enheten dubbla, lägger du till följande för att den **App** klass:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Ändra signaturen för den **huvudsakliga** metod för att utlösa följande undantag:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Lägg till följande kod för att anropa metoden firmwareUpdate direkt på den simulerade enheten den **huvudsakliga** metoden:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Om du vill söka rapporterade egenskaper från den simulerade enheten, lägger du till följande kod i **huvudsakliga** metoden:

    ```java
    ShowReportedProperties();
    ```

1. Lägg till följande kod så att du kan stoppa appen, den **huvudsakliga** metoden:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Spara och stäng filen firmware-update\src\main\java\com\mycompany\app\App.java.

1. Skapa den **firmware-uppdatering** backend-app och korrigera eventuella fel. Navigera till mappen firmware-uppdatering i en kommandotolk och kör följande kommando:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Simulera en enhet för att hantera direkt metodanrop
I det här avsnittet skapar du en Java simulerade enheten konsolapp som kan ta emot den direkta metoden firmwareUpdate. Appen körs via en process som flera tillstånd för att simulera firmware-uppdatering med hjälp av reportedProperties kommunicera status.

1. Skapa ett Maven-projekt som kallas i mappen VB-get-started **simulerade enheten** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Gå till mappen simulated-device i Kommandotolken.

1. Använd en textredigerare och öppna filen pom.xml i mappen firmware-uppdatering och Lägg till följande beroende på den **beroenden** nod. Detta beroende kan du använda iot-service-klient-paketet i appen kommunicerar med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot enhetsklienten** med [Maven Sök](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Lägg till följande **skapa** noden efter den **beroenden** nod. Den här konfigurationen instruerar Maven använda Java 1.8 för att skapa appen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Spara och stäng filen pom.xml.

1. Använd en textredigerare och öppna källfilen simulated-device\src\main\java\com\mycompany\app\App.java.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    import java.util.HashMap;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt **{yourdeviceconnectionstring}** med din enhet anslutningssträng som du antecknade i den *skapa en enhetsidentitet* avsnitt:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Om du vill implementera funktioner för direkta metoden ger återanrop genom att lägga till följande kapslade klasser till den **App** klass:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Om du vill implementera dubbla enhetsfunktion ange återanrop genom att lägga till följande kapslade klasser till den **App** klass:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Lägg till följande kapslade klassen för att implementera firmware-uppdatering, den **App** klass:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Ändra signaturen för den **huvudsakliga** metod för att utlösa följande undantag:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Lägg till följande kod för att initiera direkt metoder och enheten twins rutinen den **huvudsakliga** metoden:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Så att du kan stoppa appen, Lägg till följande kod i slutet av den **huvudsakliga** metoden:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Spara och stäng filen simulated-device\src\main\java\com\mycompany\app\App.java.

1. Skapa den **simulerade enheten** app och korrigera eventuella fel. Navigera till mappen simulerade enheten och kör följande kommando vid en kommandotolk:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. Vid en kommandotolk i den **simulerade enheten** mapp, kör följande kommando för att börja lyssna efter firmware-uppdateringsmetoden direkt.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Vid en kommandotolk i den **firmware-uppdatering** mapp, kör följande kommando för att anropa firmware-uppdatering och fråga enheten twins på den simulerade enheten från din IoT-hubb:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. Du kan se den simulerade enheten svarar på den direkta metoden i konsolen.

    ![Inbyggd programvara har uppdaterats][img-fwupdate]

## <a name="next-steps"></a>Nästa steg
I den här kursen används direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används egenskaperna rapporterade för att följa förloppet för firmware-uppdatering.

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device