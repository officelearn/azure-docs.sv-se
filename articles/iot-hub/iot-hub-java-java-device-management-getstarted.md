---
title: Kom igång med Azure IoT Hub enhetshantering (Java) | Microsoft Docs
description: Hur du använder hantering av Azure IoT Hub-enheter för att initiera en omstart av fjärranslutna enheter. Azure IoT-enhet SDK för Java används för att implementera en simulerad enhetsapp som innehåller en direkt metod och Azure IoT SDK för Java att implementera ett service-appen som anropar metoden direkt.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636196"
---
# <a name="get-started-with-device-management-java"></a>Komma igång med hantering av enheter (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använda Azure portal för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en direkt metod för att starta om enheten. Direkta metoder anropas från molnet.
* Skapa en app som anropar metoden omstart direkt i appen simulerade enheten via din IoT-hubb. Den här appen övervakar sedan rapporterade egenskaper från enheten för att se när datorn startas om åtgärden har slutförts.

I slutet av den här kursen har du två appar som Java-konsolen:

**simulerade enheten**. Den här appen:

* Ansluter till din IoT-hubb med enhetens identitet skapade tidigare.
* Tar emot ett direkt metodanrop för omstart.
* Simulerar en fysisk omstart.
* Rapporterar tiden för den senaste omstarten via en rapporterade egenskap.

**utlösaren omstart**. Den här appen:

* Anropar en direkt metod i appen simulerade enheten.
* Visar svaret på direkt metodanropet skickas av den simulerade enheten
* Visar den uppdaterade rapporterade egenskaper.

> [!NOTE]
> Information om SDK: er som du kan använda för att bygga program ska köras på enheter och din lösningens serverdel finns [Azure IoT SDK][lnk-hub-sdks].

För att slutföra den här kursen behöver du:

* Java SE 8. <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar Java för den här självstudiekursen i Windows eller Linux.
* Maven 3.  <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar [Maven][lnk-maven] för den här självstudiekursen i Windows eller Linux.
* [Node.js-version 0.10.0 eller senare](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten omstart på enheten med en direkt metod

I det här avsnittet kan skapa du en Java-konsolapp som:

1. Anropar metoden omstart direkt i appen simulerade enheten.
1. Visar svaret.
1. Omröstningar rapporterade egenskaperna skickas från enheten att avgöra när omstarten är klar.

Den här konsolen appen ansluter till din IoT-hubb för att anropa metoden direkt och läsa rapporterade egenskaper.

1. Skapa en tom mapp som kallas dm-get-started.

1. Skapa ett Maven-projekt kallas i mappen dm-get-started **utlösaren omstart** med följande kommando vid en kommandotolk. Nedan visas ett långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Navigera till mappen utlösare omstart vid en kommandotolk.

1. Med en textredigerare, öppna filen pom.xml i mappen utlösare omstart och Lägg till följande beroende på den **beroenden** nod. Detta beroende kan du använda iot-service-klient-paketet i appen kommunicerar med IoT-hubben:

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

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT-hubb-anslutningssträng som du antecknade i den *skapar en IoT-hubb* avsnitt:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. För att implementera en tråd som läser rapporterade egenskaper från enheten dubbla var 10: e sekund, lägger du till följande kapslade klassen för att den **App** klass:

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

1. Ändra signaturen för den **huvudsakliga** metod för att utlösa följande undantag:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. Lägg till följande kod för att anropa metoden omstart direkt på den simulerade enheten den **huvudsakliga** metoden:

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

1. Lägg till följande kod för att starta tråd för att avsöka rapporterade egenskaper från den simulerade enheten den **huvudsakliga** metoden:

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

1. Skapa den **utlösaren omstart** backend-app och korrigera eventuella fel. Navigera till mappen utlösare omstart och kör följande kommando vid en kommandotolk:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Java-konsolapp som simulerar en enhet. App-lyssnar efter omstarten direkt metodanrop från din IoT-hubb och omedelbart svarar på anropet. App sedan i viloläge på ett tag att simulera processen startas om innan en rapporterade egenskap används för att meddela den **utlösaren omstart** backend-app att omstarten har slutförts.

1. Skapa ett Maven-projekt kallas i mappen dm-get-started **simulerade enheten** med följande kommando vid en kommandotolk. Följande är ett långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Gå till mappen simulated-device i Kommandotolken.

1. Med en textredigerare, öppna filen pom.xml i mappen simulerade enhet och Lägg till följande beroende på den **beroenden** nod. Detta beroende kan du använda iot-service-klient-paketet i appen kommunicerar med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning][lnk-maven-device-search].

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
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med den anslutningssträng för enheten som du antecknade i den *skapa en enhetsidentitet* avsnitt:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Lägg till följande kapslade klassen för att implementera en motringning hanterare för direkta metoden statushändelser, den **App** klass:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Lägg till följande kapslade klassen för att implementera en motringning hanterare för enheten dubbla statushändelser, den **App** klass:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Lägg till följande kapslade klassen för att implementera en motringning hanterare för egenskapen händelser, den **App** klass:

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

1. Lägg till följande kapslade klassen för att implementera en tråd för att simulera enheten omstart, den **App** klass. Tråden i viloläge i fem sekunder och anger sedan den **lastReboot** rapporterade egenskapen:

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

1. Lägg till följande kapslade klassen för att implementera metoden direkt på enheten, den **App** klass. När simulerade appen tar emot ett anrop till den **omstart** direkta metoden den returnerar en bekräftelse till anroparen och startar sedan en tråd för att bearbeta omstarten:

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

1. Ändra signaturen för den **huvudsakliga** metod för att utlösa följande undantag:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Att skapa en instans av en **DeviceClient**, Lägg till följande kod i den **huvudsakliga** metoden:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Om du vill börja lyssna efter direkt metodanrop, lägger du till följande kod i **huvudsakliga** metoden:

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

1. Lägg till följande kod för att stänga av enheten simulatorn den **huvudsakliga** metoden:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Spara och stäng filen simulated-device\src\main\java\com\mycompany\app\App.java.

1. Skapa den **simulerade enheten** backend-app och korrigera eventuella fel. Navigera till mappen simulerade enheten och kör följande kommando vid en kommandotolk:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. Kör följande kommando för att börja lyssna efter omstart metodanrop från din IoT-hubb i en kommandotolk i mappen simulerade enhet:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-hubb simulerade enhetsapp att lyssna efter omstart direkt metodanrop][1]

1. Kör följande kommando för att anropa metoden omstart den simulerade enheten från din IoT-hubb i en kommandotolk i mappen utlösare omstart:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-hubb service-appen för att anropa metoden omstart direkt][2]

1. Den simulerade enheten svarar på omstart direkt metodanropet:

    ![Java IoT-hubb simulerade enhetsapp besvarar metodanropet direkt][3]

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