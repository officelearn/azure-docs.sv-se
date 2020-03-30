---
title: Komma igång med Azure IoT Hub-enhetshantering (Java) | Microsoft-dokument
description: Så här använder du Azure IoT Hub-enhetshantering för att initiera en omstart av en fjärrenhet. Du använder Azure IoT-enheten SDK för Java för att implementera en simulerad enhetsapp som innehåller en direkt metod och Azure IoT-tjänsten SDK för Java för att implementera en tjänstapp som anropar den direkta metoden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f68e25a618f5c6499ccc9d76c510eab8f1650330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110884"
---
# <a name="get-started-with-device-management-java"></a>Komma igång med enhetshantering (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure-portalen för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.

* Skapa en simulerad enhetsapp som implementerar en direkt metod för att starta om enheten. Direkta metoder anropas från molnet.

* Skapa en app som anropar metoden starta om direkt i den simulerade enhetsappen via IoT-hubben. Den här appen övervakar sedan de rapporterade egenskaperna från enheten för att se när omstarten är klar.

I slutet av den här självstudien har du två Java-konsolappar:

**simulerad enhet**. Den här appen:

* Ansluter till din IoT-hubb med enhetsidentiteten som skapats tidigare.

* Tar emot ett direkt metodanrop för omstart.

* Simulerar en fysisk omstart.

* Rapporterar tidpunkten för den senaste omstarten via en rapporterad egenskap.

**trigger-omstart**. Den här appen:

* Anropar en direkt metod i den simulerade enhetsappen.

* Visar svaret på det direkta metodanrop som skickas av den simulerade enheten.

* Visar de uppdaterade rapporterade egenskaperna.

> [!NOTE]
> Information om de SDK:er som du kan använda för att skapa program för att köras på enheter och din lösnings serverdel finns i [Azure IoT SDK:er](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Krav

* [Java SE Utveckling Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Se till att du väljer **Java 8** under **Långsiktigt stöd** för att komma till nedladdningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärromstart på enheten med en direkt metod

I det här avsnittet skapar du en Java-konsolapp som:

1. Anropar metoden starta om direkt i den simulerade enhetsappen.

2. Visar svaret.

3. Avföljer de rapporterade egenskaperna som skickas från enheten för att avgöra när omstarten är klar.

Den här konsolappen ansluter till din IoT Hub för att anropa direktmetoden och läsa de rapporterade egenskaperna.

1. Skapa en tom mapp med namnet **dm-get-started**.

2. Skapa ett Maven-projekt som kallas **trigger-reboot** i mappen **dm-get-started** med kommandotolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. I kommandotolken navigerar du till mappen omstart för **utlösare.**

4. Öppna **filen pom.xml** i mappen **trigger-reboot** med hjälp av en textredigerare och lägg till följande beroende i **beroendenoden.** Med det här beroendet kan du använda iot-service-klientpaketet i appen för att kommunicera med din IoT-hubb:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Lägg till följande **byggnod** efter **beroendenoden.** Den här konfigurationen instruerar Maven att använda Java 1.8 för att skapa appen:

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

6. Spara och stäng **filen pom.xml.**

7. Öppna **filen trigger-reboot\src\main\java\com\mycompany\app\App.java.Using a text editor, open the trigger-reboot\src\main\java\com\mycompany\app\App.java** source file.

8. Lägg till följande **Import**-instruktioner i filen:

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

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med anslutningssträngen för IoT Hub som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Om du vill implementera en tråd som läser de rapporterade egenskaperna från enhetstvillingen var 10:e sekund lägger du till följande kapslade klass i **klassen App:**

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

11. Ändra signaturen för **huvudmetoden** för att utlösa följande undantag:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Om du vill anropa metoden starta om direkt på den simulerade enheten ersätter du koden i **huvudmetoden** med följande kod:

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

13. Om du vill starta tråden för att avsöka de **main** rapporterade egenskaperna från den simulerade enheten lägger du till följande kod i huvudmetoden:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Om du vill att du ska kunna **main** stoppa appen lägger du till följande kod i huvudmetoden:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Spara och stäng **filen trigger-reboot\src\main\java\com\mycompany\app\App.java.**

16. Skapa backend-appen för **utlösare omstart** och korrigera eventuella fel. I kommandotolken navigerar du till mappen omstart för **utlösare** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Java-konsolapp som simulerar en enhet. Appen lyssnar efter det direkta metodanropet för omstart från din IoT-hubb och svarar omedelbart på det samtalet. Appen vilolägen ett tag för att simulera omstartsprocessen innan den använder en rapporterad egenskap för att meddela backend-appen för **utlösare omstart** om att omstarten är klar.

1. Skapa ett Maven-projekt som kallas **simulerad enhet** i mappen **dm-get-started** med kommandotolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. I kommandotolken navigerar du till mappen **med simulerade enheter.**

3. Öppna **filen pom.xml** i mappen **simulerad enhet** med hjälp av en textredigerare och lägg till följande beroende i **beroendenoden.** Med det här beroendet kan du använda iot-service-klientpaketet i appen för att kommunicera med din IoT-hubb:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Lägg till följande beroende i **beroendenoden.** Det här beroendet konfigurerar en NOP för Apache [SLF4J](https://www.slf4j.org/) loggningsfasaden, som används av enhetsklienten SDK för att implementera loggning. Den här konfigurationen är valfri, men om du utelämnar den kan du se en varning i konsolen när du kör appen. Mer information om hur du loggar in enhetsklienten SDK finns i [Loggning](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) i *exempel för Azure IoT-enheten SDK för* Java-readme-fil.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Lägg till följande **byggnod** efter **beroendenoden.** Den här konfigurationen instruerar Maven att använda Java 1.8 för att skapa appen:

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

6. Spara och stäng **filen pom.xml.**

7. Öppna den **simulerade enheten\src\main\java\com\mycompany\app\App.java-källfilen** med hjälp av en textredigerare.

8. Lägg till följande **Import**-instruktioner i filen:

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

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med enhetsanslutningssträngen som du noterade i registrera en ny enhet i avsnittet [IoT-hubb:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Om du vill implementera en motringningshanterare för direkta metodstatushändelser lägger du till följande kapslade klass i **klassen App:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Om du vill implementera en motringningshanterare för enhets twin statushändelser lägger du till följande kapslade klass i **klassen App:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Om du vill implementera en motringningshanterare för egenskapshändelser lägger du till följande kapslade klass i **klassen App:**

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

13. Om du vill implementera en tråd för att simulera omstarten av enheten lägger du till följande kapslade klass i **klassen App.** Tråden förskollar i fem sekunder och ställer sedan in egenskapen **lastReboot** rapporterade:

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

14. Om du vill implementera den direkta metoden på enheten lägger du till följande kapslade klass i **klassen App.** När den simulerade appen tar emot ett anrop till **metoden reboot** direct returnerar den en bekräftelse till anroparen och startar sedan en tråd för att bearbeta omstarten:

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

15. Ändra signaturen för **huvudmetoden** för att kasta följande undantag:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Om du vill instansiera en **DeviceClient**ersätter du koden i **huvudmetoden** med följande kod:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Om du vill börja lyssna efter direktmetodanrop lägger du till följande kod i **huvudmetoden:**

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

18. Om du vill stänga av enhetssimulatorn lägger du till följande kod i **huvudmetoden:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Spara och stäng filen simulerad enhet\src\main\java\com\mycompany\app\App.java.

20. Skapa appen **med simulerade enheter** och korrigera eventuella fel. I kommandotolken navigerar du till mappen **simulerad enhet** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. Vid en kommandotolk i mappen **simulerad enhet** kör du följande kommando för att börja lyssna efter omstartsmetodanrop från IoT-hubben:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub simulerad enhet app för att lyssna efter omstart direkt metod samtal](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Vid en kommandotolk i mappen omstart för **utlösare** kör du följande kommando för att anropa omstartsmetoden på den simulerade enheten från IoT-hubben:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub tjänst app för att ringa omstart direkt metod](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Den simulerade enheten svarar på anropet för direkt metodomstart:

    ![Java IoT Hub simulerad enhetsapp svarar på direktmetoden samtalet](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
