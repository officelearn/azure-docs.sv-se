---
title: "Schemalägga med Azure IoT Hub (Java) | Microsoft Docs"
description: "Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod och ange en önskad egenskap på flera enheter. Azure IoT-enhet SDK för Java används för att implementera apparna som simulerade enheten och tjänsten Azure IoT SDK för Java att implementera ett service-appen för att köra jobbet."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: 9009afc83b40064c6dbf10100f514c681af9b96b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="schedule-and-broadcast-jobs-java"></a>Schemat och sändning jobb (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT-hubb för att schemalägga och spåra jobb som uppdaterar miljoner enheter. Använd jobb till:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkt metoder

Ett jobb radbryts i någon av följande åtgärder och spårar körning mot en uppsättning enheter. En enhet dubbla fråga definierar vilken uppsättning av enheter som jobbet körs mot. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod i 10 000 enheter som startar om enheterna. Anger vilken uppsättning av enheter med en enhet dubbla fråga och schemalägga jobbet ska köras vid ett senare tillfälle. Spårar jobbförloppet som var och en av enheterna som tar emot och köra den direkta metoden omstart.

Mer information om var och en av dessa funktioner finns:

* Enheten dubbla och egenskaper: [Kom igång med enheten twins](iot-hub-java-java-twin-getstarted.md)
* Dirigera metoder: [IoT-hubb Utvecklarhandbok - direkt metoder](iot-hub-devguide-direct-methods.md) och [kursen: direkta metoder](iot-hub-java-java-direct-methods.md)

I den här självstudiekursen lär du dig att:

* Skapa en enhetsapp som implementerar en direkt metod som kallas **lockDoor**. Enheten appen tar också emot önskade egenskapsändringar från backend-app.
* Skapa en backend-app som skapar ett jobb för att anropa den **lockDoor** direkt metod på flera enheter. Ett annat jobb skickar önskade egenskapen uppdateringar till flera enheter.

I slutet av den här kursen har du en enhet för java konsolapp och en java-backend-konsolapp:

**simulerade enheten** som ansluter till din IoT-hubb implementerar den **lockDoor** direkt metod och hanterar behövs egenskapsändringar.

**schema-jobb** som använder jobb för att anropa den **lockDoor** direkt metod och uppdatera dubbla önskad enhetsegenskaper på flera enheter.

> [!NOTE]
> Artikeln [Azure IoT SDK](iot-hub-devguide-sdks.md) innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Om du vill skapa enhetens identitet programmässigt läsa motsvarande avsnitt i den [ansluta enheten till din IoT-hubb som använder Java](iot-hub-java-java-getstarted.md#create-a-device-identity) artikel. Du kan också använda den [iothub explorer](https://github.com/Azure/iothub-explorer) verktyg för att lägga till en enhet till IoT-hubben.

## <a name="create-the-service-app"></a>Skapa service-appen

I det här avsnittet skapar du en Java-konsolapp som använder jobb till:

* Anropa den **lockDoor** direkt metod på flera enheter.
* Skicka egenskaper till flera enheter.

För att skapa appen:

1. Skapa en tom mapp med namnet på utvecklingsdatorn, `iot-java-schedule-jobs`.

1. I den `iot-java-schedule-jobs` mapp, skapa ett Maven-projekt som kallas **schema jobb** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Kommandotolken, gå till den `schedule-jobs` mapp.

1. Med en textredigerare, öppna den `pom.xml` filen i den `schedule-jobs` mapp och Lägg till följande beroende på den **beroenden** nod. Det här beroendet kan du använda den **iot-service-klient** paketet i appen kommunicerar med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
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

1. Spara och Stäng den `pom.xml` filen.

1. Med en textredigerare, öppna den `schedule-jobs\src\main\java\com\mycompany\app\App.java` filen.

1. Lägg till följande **Import**-instruktioner i filen:

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

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT-hubb-anslutningssträng som du antecknade i den *skapar en IoT-hubb* avsnitt:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Lägg till följande metod för att den **App** klassen för att schemalägga ett jobb för att uppdatera den **byggnad** och **våning** önskade egenskaper i enheten dubbla:

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

1. Schemalägga ett jobb för att anropa den **lockDoor** metod, Lägg till följande metod för att den **App** klass:

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

1. Om du vill övervaka ett jobb, lägger du till följande metod för att den **App** klass:

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

1. Lägg till följande metod för att fråga efter information om de jobb som du körde:

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

1. Uppdatering av **huvudsakliga** Metodsignaturen att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Om du vill köra och övervaka två jobb sekventiellt, lägger du till följande kod i **huvudsakliga** metoden:

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

1. Spara och Stäng den `schedule-jobs\src\main\java\com\mycompany\app\App.java` fil

1. Skapa den **schema jobb** app och korrigera eventuella fel. Kommandotolken, gå till den `schedule-jobs` mappen och kör sedan följande kommando:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet skapar du en Java-konsolapp som hanterar de egenskaper skickats från IoT-hubb och implementerar metodanropet direkt.

1. I den `iot-java-schedule-jobs` mapp, skapa ett Maven-projekt som kallas **simulerade enheten** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Kommandotolken, gå till den `simulated-device` mapp.

1. Med en textredigerare, öppna den `pom.xml` filen i den `simulated-device` mapp och Lägg till följande beroenden till den **beroenden** nod. Det här beroendet kan du använda den **iot enhetsklienten** paketet i appen kommunicerar med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
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

1. Spara och Stäng den `pom.xml` filen.

1. Med en textredigerare, öppna den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätta `{youriothubname}` med din IoT-hubbnamnet och `{yourdevicekey}` med nyckeln för enheten värde som du genererade i den *skapa en enhetsidentitet* avsnitt:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt.

1. Om du vill skriva ut enheten dubbla meddelanden till konsolen, lägger du till följande kapslade klassen för att den **App** klass:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Direkt metod meddelanden till konsolen, till att lägga till följande kapslade klassen för att den **App** klass:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Lägg till följande kapslade klassen för att hantera direkta metodanrop från IoT-hubb i **App** klass:

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

1. Uppdatering av **huvudsakliga** Metodsignaturen att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Lägg till följande kod i den **huvudsakliga** metod för att:
    * Skapa en klientenheter kommunicerar med IoT-hubben.
    * Skapa en **enhet** objekt att lagra dubbla Enhetsegenskaper.

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

1. Lägg till följande kod för att starta klienttjänsterna enheten den **huvudsakliga** metoden:

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

1. Vänta på att användaren trycker du på den **RETUR** nyckeln innan du stänger, Lägg till följande kod i slutet av den **huvudsakliga** metoden:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Spara och Stäng den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

1. Skapa den **simulerade enheten** app och korrigera eventuella fel. Kommandotolken, gå till den `simulated-device` mappen och kör sedan följande kommando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra konsolappar.

1. Vid en kommandotolk i den `simulated-device` mapp, kör följande kommando för att starta enheten appen lyssnar efter önskad egenskapsändringar och direkt metodanrop:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Enhetsklienten startar](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. Vid en kommandotolk i den `schedule-jobs` mapp, kör följande kommando för att köra den **schema jobb** service app att köras två jobb. Först anger önskad egenskapsvärden, andra anropar metoden direkt:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Service-appen för Java IoT-hubb skapar två jobb](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. Appen enheten hanterar önskade egenskapen ändrings- och metodanropet direkt:

    ![Enhetsklienten besvarar ändringarna](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har skapat en backend-app för att köra två jobb. Det första jobbet ange önskade värden och det andra jobbet direkt metod.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb](iot-hub-java-java-getstarted.md) kursen.
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app) med den [metoder från direkt](iot-hub-java-java-direct-methods.md) kursen.
