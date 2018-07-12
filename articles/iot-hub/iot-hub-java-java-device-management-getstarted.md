---
title: Kom igång med enhetshantering i Azure IoT Hub (Java) | Microsoft Docs
description: Hur du använder Azure IoT Hub-enhetshantering för att initiera en fjärransluten enhet omstart. Du kan använda Azure IoT-enhetens SDK för Java för att implementera en simulerad enhetsapp som innehåller en direkt metod och tjänsten Azure IoT SDK för Java för att implementera en service-app som anropar direktmetoden.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38481981"
---
# <a name="get-started-with-device-management-java"></a>Kom igång med enhetshantering (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använda Azure-portalen för att skapa en IoT Hub och skapa en enhetsidentitet i IoT hub.
* Skapa en simulerad enhetsapp som implementerar en direkt metod om du vill starta om enheten. Direkta metoder anropas från molnet.
* Skapa en app som anropar metoden omstart direkt i den simulerade enhetsappen via din IoT-hubb. Den här appen övervakar sedan de rapporterade egenskaperna från enheten för att se när omstart åtgärden har slutförts.

I slutet av den här självstudien har du två Java-konsolappar:

**simulated-device**. Den här appen:

* Ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare.
* Tar emot ett samtal för omstart direkt metod.
* Simulerar en fysisk omstart.
* Rapporterar tidpunkten för den senaste omstarten via en rapporterad egenskap.

**utlösaren omstart**. Den här appen:

* Anropar en direkt metod i den simulerade enhetsappen.
* Visar svaret till anropet direkt metod som skickas av den simulerade enheten
* Visar det uppdaterade rapporterade egenskaper.

> [!NOTE]
> Information om SDK: er som du kan använda för att skapa program som körs på enheter och lösningens backend-servrar finns i [Azure IoT SDK: er][lnk-hub-sdks].

För att slutföra den här kursen behöver du:

* Java SE 8. <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar Java för den här självstudiekursen i Windows eller Linux.
* Maven 3.  <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar [Maven][lnk-maven] för den här självstudiekursen i Windows eller Linux.
* [Node.js-version 0.10.0 eller senare](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärromstart på enheten med en direkt metod

I det här avsnittet skapar du en Java-konsolapp som:

1. Anropar metoden omstart direkt i den simulerade enhetsappen.
1. Visar svaret.
1. Polls – de rapporterade egenskaperna som skickas från enheten för att avgöra när omstarten är klar.

Den här konsolappen ansluter till din IoT-hubb för att anropa den direkta metoden och läsa rapporterade egenskaper.

1. Skapa en tom mapp med namnet dm-get-started.

1. I mappen dm-get-started skapar du ett Maven-projekt som heter **utlösaren omstart** med följande kommando i Kommandotolken. Nedan visas ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. I Kommandotolken, navigerar du till mappen utlösare omstart.

1. Använd en textredigerare och öppna filen pom.xml i mappen utlösare omstart och Lägg till följande beroende till den **beroenden** noden. Det här beroendet gör att du kan använda iot-service-client-paketet i din app för att kommunicera med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning][lnk-maven-service-search].

1. Lägg till följande **skapa** noden efter den **beroenden** noden. Den här konfigurationen instruerar Maven du använder Java 1.8 för att skapa programmet:

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

1. Använd en textredigerare och öppna källfilen trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT hub-anslutningssträngen som du antecknade i den *skapar en IoT Hub* avsnittet:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. För att implementera en tråd som läser de rapporterade egenskaperna från enhetstvillingen var tionde sekund, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Ändra signaturen för den **huvudsakliga** metod för att följande undantag:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. Lägg till följande kod för att anropa metoden omstart direkt på den simulerade enheten på **huvudsakliga** metoden:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. Lägg till följande kod för att starta tråd för att avsöka de rapporterade egenskaperna från den simulerade enheten genom den **huvudsakliga** metoden:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Lägg till följande kod så att du kan stoppa appen, den **huvudsakliga** metoden:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Spara och stäng filen trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Skapa den **utlösaren omstart** serverdelsapp och korrigera eventuella fel. I Kommandotolken, navigera till mappen utlösare omstart och kör följande kommando:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Java-konsolapp som simulerar en enhet. App-lyssnar efter omstarten dirigera metodanrop från IoT hub och omedelbart svarar på anropet. Appen sedan vilar på ett tag att simulera omstarten processen innan den använder en rapporterad egenskap för att meddela den **utlösaren omstart** serverdelsappen att omstarten har slutförts.

1. I mappen dm-get-started skapar du ett Maven-projekt som heter **simulated-device** med följande kommando i Kommandotolken. Följande är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Gå till mappen simulated-device i Kommandotolken.

1. Använd en textredigerare och öppna filen pom.xml i mappen simulated-device och Lägg till följande beroende till den **beroenden** noden. Det här beroendet gör att du kan använda iot-service-client-paketet i din app för att kommunicera med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning][lnk-maven-device-search].

1. Lägg till följande **skapa** noden efter den **beroenden** noden. Den här konfigurationen instruerar Maven du använder Java 1.8 för att skapa programmet:

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

1. Använd en textredigerare och öppna filen simulated-device\src\main\java\com\mycompany\app\App.java källa.

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
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med enhetens anslutningssträng som du antecknade i den *skapa en enhetsidentitet* avsnittet:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. För att implementera en motringningshanterare för direkt metod statushändelser, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. För att implementera en motringningshanterare för enhetshändelser twin status, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. För att implementera en motringningshanterare för egenskapen händelser, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. För att implementera en tråd för att simulera enheten omstart, lägger du till följande kapslade klassen för att den **App** klass. Tråden vilar i fem sekunder och ställer sedan den **lastReboot** rapporterade egenskap:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Om du vill implementera direkt metod på enheten, lägger du till följande kapslade klassen för att den **App** klass. När simulerade appen tar emot ett anrop till den **omstart** direkt metod den returnerar en bekräftelse till anroparen och startar sedan en tråd för att bearbeta omstarten:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
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

1. Ändra signaturen för den **huvudsakliga** metoden utlöser följande undantag:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Att skapa en instans av en **DeviceClient**, Lägg till följande kod till den **huvudsakliga** metoden:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Om du vill påbörja avlyssning för direkt metod-anrop, lägger du till följande kod till den **huvudsakliga** metoden:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Om du vill stänga av enhetssimulatorn lägger du till följande kod till den **huvudsakliga** metoden:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Spara och stäng filen simulated-device\src\main\java\com\mycompany\app\App.java i.

1. Skapa den **simulated-device** serverdelsapp och korrigera eventuella fel. Navigera till mappen simulated-device i Kommandotolken, och kör följande kommando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. Kör följande kommando för att börja lyssna efter omstart metodanrop från IoT hub i en kommandotolk i mappen simulated-device:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT Hub simulerad enhetsapp för att lyssna efter omstart direkt metodanrop][1]

1. I Kommandotolken i mappen utlösare omstart, kör du följande kommando för att anropa metoden omstart på din simulerade enhet från IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT Hub-tjänstapp för att anropa metoden omstart direkt][2]

1. Den simulerade enheten svarar på anropet direkt metod för omstart:

    ![Java IoT Hub-simulerad enhetsapp som svarar på metodanropet direkt][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22