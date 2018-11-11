---
title: Schemalägg jobb med Azure IoT Hub (Java) | Microsoft Docs
description: Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod och ange en önskad egenskap på flera enheter. Du kan använda Azure IoT-enhetens SDK för Java för att implementera appar för simulerade enheter och tjänsten Azure IoT SDK för Java för att implementera en tjänstapp för att köra jobbet.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: 52c5e1a2166c2c609b2edc486bd451f01b3395fa
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515802"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Schemalägg och Sänd jobb (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använda Azure IoT Hub för att schemalägga och spåra jobb som uppdaterar miljontals enheter. Använd jobb till:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direktmetoder

Ett jobb omsluter en av de här åtgärderna och spårar körning mot en uppsättning enheter. En enhet twin fråga definierar vilken uppsättning av enheter som jobbet körs mot. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod på 10 000 enheter som startar om enheterna. Du anger vilken uppsättning av enheter med en enhet twin fråga och schemalägga jobb att köra vid ett senare tillfälle. Spårar jobbförloppet som var och en av enheterna tar emot och kör den direkt metoden för omstart.

Mer information om var och en av de här funktionerna finns:

* Enhetstvillingen och egenskaper: [Kom igång med enhetstvillingar](iot-hub-java-java-twin-getstarted.md)

* Direkta metoder: [IoT Hub developer guide - direkta metoder](iot-hub-devguide-direct-methods.md) och [självstudie: Använd direkta metoder](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en app för enheter som implementerar en direkt metod som kallas **lockDoor**. App för enheter får också önskad egenskapsändringar från backend-app.

* Skapa en backend-app som skapar ett jobb för att anropa den **lockDoor** direkt metod på flera enheter. Ett annat jobb skickar uppdateringar för önskad egenskap till flera enheter.

I slutet av den här självstudien har du en enhetsapp för java-konsolen och en java-backend-konsolapp:

**simulated-device** som ansluter till din IoT hub implementerar den **lockDoor** direkta metoden och hanterar önskade egenskapsändringar.

**Schemalägg jobb** som använder jobb för att anropa den **lockDoor** direkt metod och uppdatera enhetstvillingens egenskaper på flera enheter.

> [!NOTE]
> Artikeln [Azure IoT SDK: er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK: er som du kan använda för att skapa appar för både enheten och backend-server.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Senaste [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Du kan också använda den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) verktyg för att lägga till en enhet till IoT hub.

## <a name="create-the-service-app"></a>Skapa service-app

I det här avsnittet skapar du en Java-konsolapp som använder jobb för att:

* Anropa den **lockDoor** direkt metod på flera enheter.

* Skicka önskade egenskaper för flera enheter.

Skapa appen:

1. Skapa en tom mapp med namnet på en utvecklingsdator måste `iot-java-schedule-jobs`.

2. I den `iot-java-schedule-jobs` mapp, skapa ett Maven-projekt som heter **schemalägga jobb** med följande kommando i Kommandotolken. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. I Kommandotolken, navigera till den `schedule-jobs` mapp.

4. Använd en textredigerare och öppna den `pom.xml` fil i den `schedule-jobs` mapp och Lägg till följande beroende till den **beroenden** noden. Det här beroendet gör att du kan använda den **iot-service-client** paketet i din app att kommunicera med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Lägg till följande **skapa** noden efter den **beroenden** noden. Den här konfigurationen instruerar Maven du använder Java 1.8 för att skapa programmet:

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

6. Spara och Stäng den `pom.xml` filen.

7. Använd en textredigerare och öppna den `schedule-jobs\src\main\java\com\mycompany\app\App.java` filen.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT hub-anslutningssträngen som du antecknade i den *skapar en IoT Hub* avsnittet:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Lägg till följande metod för att den **App** klassen för att schemalägga ett jobb för att uppdatera den **byggnad** och **våning** önskade egenskaper i enhetstvillingen:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Schemalägga ett jobb för att anropa den **lockDoor** metoden lägger du till följande metod för att den **App** klass:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. För att övervaka ett jobb, lägger du till följande metod för att den **App** klass:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Om du vill fråga efter information om de jobb som du körde, lägger du till följande metod:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Uppdatera den **huvudsakliga** signaturen för metoden att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Lägg till följande kod för att köra och övervaka två jobb sekventiellt i **huvudsakliga** metoden:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Spara och Stäng den `schedule-jobs\src\main\java\com\mycompany\app\App.java` fil

17. Skapa den **schemalägga jobb** app och korrigera eventuella fel. I Kommandotolken, navigera till den `schedule-jobs` mappen och kör sedan följande kommando:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet skapar du en Java-konsolapp som hanterar de önskade egenskaperna som skickas från IoT Hub och implementerar metodanropet direkt.

1. I den `iot-java-schedule-jobs` mapp, skapa ett Maven-projekt som heter **simulated-device** med följande kommando i Kommandotolken. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. I Kommandotolken, navigera till den `simulated-device` mapp.

3. Använd en textredigerare och öppna den `pom.xml` fil i den `simulated-device` mapp och Lägg till följande beroenden till den **beroenden** noden. Det här beroendet gör att du kan använda den **iot-device-client** paketet i din app att kommunicera med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Lägg till följande **skapa** noden efter den **beroenden** noden. Den här konfigurationen instruerar Maven du använder Java 1.8 för att skapa programmet:

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

5. Spara och Stäng den `pom.xml` filen.

6. Använd en textredigerare och öppna den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

7. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubname}` med ditt IoT-hubbnamn och `{yourdevicekey}` med det enhetsnyckelvärde som du genererade i den *skapa en enhetsidentitet* avsnittet:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt.

9. Om du vill skriva ut twin enhetsmeddelanden till konsolen, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

10. Om du vill skriva ut direkt metod-meddelanden till konsolen, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

11. Om du vill hantera direkt metodanrop från IoT Hub, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

12. Uppdatera den **huvudsakliga** signaturen för metoden att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

13. Lägg till följande kod till den **huvudsakliga** metod för att:
    * Skapa en klientenhet att kommunicera med IoT Hub.
    * Skapa en **enhet** objekt för att lagra tvillingegenskaper.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

14. Starta klienttjänsterna enhet genom att lägga till följande kod till den **huvudsakliga** metoden:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

15. Vänta tills användaren ska trycka på den **RETUR** nyckeln innan du stänger, Lägg till följande kod i slutet av den **huvudsakliga** metoden:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

16. Spara och Stäng den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

17. Skapa den **simulated-device** app och korrigera eventuella fel. I Kommandotolken, navigera till den `simulated-device` mappen och kör sedan följande kommando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Köra apparna

Du är nu redo att köra konsolappar.

1. I en kommandotolk i den `simulated-device` mapp, kör följande kommando för att starta appen lyssna efter ändringar av önskad egenskap och direkt metodanrop:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Enhetsklienten startar](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. I en kommandotolk i den `schedule-jobs` mapp, kör följande kommando för att köra den **schemalägga jobb** tjänstapp för att köra två jobb. Först anger önskade egenskapsvärden, andra anropar direktmetoden:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT Hub-tjänstapp skapar två jobb](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Enhetsappen hanterar önskade egenskapsändringen och metodanropet direkt:

    ![Enhetsklienten svarar på ändringar](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har skapat en backend-app för att köra två jobb. Det första jobbet ange önskade egenskapsvärden och andra jobb kallas en direkt metod.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT Hub](quickstart-send-telemetry-java.md) självstudien.

* Kontrollera enheter interaktivt (till exempel aktivera en fans, från en användarstyrd app) med den [Använd direkta metoder](quickstart-control-device-java.md) tutorial.s