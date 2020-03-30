---
title: Schemalägga jobb med Azure IoT Hub (Java) | Microsoft-dokument
description: Så här schemalägger du ett Azure IoT Hub-jobb för att anropa en direkt metod och ange en önskad egenskap på flera enheter. Du använder Azure IoT-enheten SDK för Java för att implementera de simulerade enhetsapparna och Azure IoT-tjänsten SDK för Java för att implementera en tjänstapp för att köra jobbet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: 9227192b2f7c554943fb3716ba1d1066f814c447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110315"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Schemalägga och sända jobb (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT Hub för att schemalägga och spåra jobb som uppdaterar miljontals enheter. Använd jobb för att:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkta metoder

Ett jobb radbryts en av dessa åtgärder och spårar körningen mot en uppsättning enheter. En enhetstvillingfråga definierar den uppsättning enheter som jobbet körs mot. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod på 10 000 enheter som startar om enheterna. Du anger uppsättningen enheter med en enhetstvillingfråga och schemalägger jobbet så att det körs vid en framtida tidpunkt. Jobbet spårar förloppet när var och en av enheterna får och kör metoden för omstart direkt.

Mer information om var och en av dessa funktioner finns i:

* Enhetstvilling och egenskaper: [Kom igång med enhetstvillingar](iot-hub-java-java-twin-getstarted.md)

* Direkta metoder: [IoT Hub developer guide - direkta metoder](iot-hub-devguide-direct-methods.md) och [handledning: Använd direkta metoder](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en enhetsapp som implementerar en direkt metod som kallas **lockDoor**. Enhetsappen tar också emot önskade egenskapsändringar från backend-appen.

* Skapa en backend-app som skapar ett jobb för att anropa **metoden lockDoor** direct på flera enheter. Ett annat jobb skickar önskade egenskapsuppdateringar till flera enheter.

I slutet av den här självstudien har du en java-konsolenhetsapp och en serverdelsapp för Java-konsolen:

**simulerad enhet** som ansluter till din IoT-hubb, implementerar **direct-metoden lockDoor** och hanterar önskade egenskapsändringar.

**schema-jobb** som använder jobb för att anropa **lockDoor** direkt metod och uppdatera enheten dubbla önskade egenskaper på flera enheter.

> [!NOTE]
> Artikeln [Azure IoT SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK:er som du kan använda för att skapa både enhets- och backend-appar.

## <a name="prerequisites"></a>Krav

* [Java SE Utveckling Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Se till att du väljer **Java 8** under **Långsiktigt stöd** för att komma till nedladdningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Du kan också använda [IoT-tillägget för Azure CLI-verktyget för](https://github.com/Azure/azure-iot-cli-extension) att lägga till en enhet i din IoT-hubb.

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänstappen

I det här avsnittet skapar du en Java-konsolapp som använder jobb för att:

* Anropa **lockDoor** direct-metoden på flera enheter.

* Skicka önskade egenskaper till flera enheter.

Så här skapar du appen:

1. På din utvecklingsmaskin skapar du en tom mapp med namnet **iot-java-schedule-jobs**.

2. Skapa ett Maven-projekt som kallas **schemajobb** med följande kommando i kommandotolken i **iot-java-schedule-jobs.** Observera att detta är ett enda långt kommando:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. I kommandotolken navigerar du till mappen **schema-jobb.**

4. Öppna **filen pom.xml** i mappen **schema-jobb** med hjälp av en textredigerare och lägg till följande beroende i **beroendenoden.** Med det här beroendet kan du använda **iot-service-klientpaketet** i appen för att kommunicera med din IoT-hubb:

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

7. Öppna **filen schedule-jobs\src\main\java\com\mycompany\app\App.java** med hjälp av en textredigerare.

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

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Lägg till följande metod i **klassen App** om du vill schemalägga ett jobb för att uppdatera önskade egenskaper för **byggnad** och **golv** i enhetstvillingen:

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

11. Om du vill schemalägga ett jobb för att anropa **metoden lockDoor** lägger du till följande metod i klassen **App:**

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

12. Om du vill övervaka ett jobb lägger du till följande metod i **klassen App:**

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

13. Om du vill fråga efter information om de jobb du körde lägger du till följande metod:

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

14. Uppdatera **main** huvudmetodsignaturen `throws` så att den innehåller följande sats:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Om du vill köra och övervaka två jobb sekventiellt ersätter du koden i **huvudmetoden** med följande kod:

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

16. Spara och stäng **filen schema-jobb\src\main\java\com\mycompany\app\App.java**

17. Skapa **appen schema-jobb** och korrigera eventuella fel. I kommandotolken navigerar du till mappen **schema-jobb** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet skapar du en Java-konsolapp som hanterar önskade egenskaper som skickas från IoT Hub och implementerar direktmetodanropet.

1. Skapa ett Maven-projekt som kallas **simulerad enhet** med följande kommando i kommandotolken i mappen **iot-java-schedule-jobs.** Observera att detta är ett enda långt kommando:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. I kommandotolken navigerar du till mappen **med simulerade enheter.**

3. Öppna **filen pom.xml** i mappen **simulerad enhet** med hjälp av en textredigerare och lägg till följande beroenden i **beroendenoden.** Med det här beroendet kan du använda **iot-device-client-paketet** i appen för att kommunicera med din IoT-hubb:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Lägg till följande beroende i **beroendenoden.** Det här beroendet konfigurerar en NOP för Apache [SLF4J](https://www.slf4j.org/) loggningsfasaden, som används av enhetsklienten SDK för att implementera loggning. Den här konfigurationen är valfri, men om du utelämnar den kan du se en varning i konsolen när du kör appen. Mer information om hur du loggar in enhetsklienten SDK finns i [Loggning](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)i *exempel för Azure IoT-enheten SDK för* Java-readme-fil.

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

7. Öppna filen **simulerad enhet\src\main\java\com\mycompany\app\App.java** med hjälp av en textredigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med den enhetsanslutningssträng som du kopierade tidigare i avsnittet Registrera en ny enhet i avsnittet [IoT-hubb:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt.

10. Om du vill skriva ut dubbla enhetsmeddelanden till konsolen lägger du till följande kapslade klass i **klassen App:**

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Om du vill skriva ut direktmetodmeddelanden till konsolen lägger du till följande kapslade klass i **klassen App:**

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Om du vill hantera direktmetodanrop från IoT Hub lägger du till följande kapslade klass i **klassen App:**

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

13. Uppdatera **main** huvudmetodsignaturen `throws` så att den innehåller följande sats:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Ersätt koden i **huvudmetoden** med följande kod till:
    * Skapa en enhetsklient för att kommunicera med IoT Hub.
    * Skapa ett **enhetsobjekt** för att lagra enhetstvillingegenskaperna.

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

15. Om du vill starta enhetens klienttjänster **main** lägger du till följande kod i huvudmetoden:

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

16. Om du vill vänta på att användaren ska trycka på **Retur** innan du stänger av lägger du till följande kod i slutet av **huvudmetoden:**

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Spara och stäng **filen simulerad enhet\src\main\java\com\mycompany\app\App.java.**

18. Skapa appen **med simulerade enheter** och korrigera eventuella fel. I kommandotolken navigerar du till mappen **simulerad enhet** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra konsolapparna.

1. Vid en kommandotolk i mappen **simulerad enhet** kör du följande kommando för att starta enhetsappens lyssning efter önskade egenskapsändringar och direkta metodanrop:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Enhetsklienten startar](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Kör följande kommando `schedule-jobs` i en kommandotolk i mappen för att köra tjänstappen **schema-jobb** för att köra två jobb. Den första anger önskade egenskapsvärden, den andra anropar den direkta metoden:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Java IoT Hub service app skapar två jobb](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Enhetsappen hanterar önskad egenskapsändring och direktmetodanrop:

   ![Enhetsklienten svarar på ändringarna](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetstvillingens egenskaper.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med självstudien [Kom igång med IoT Hub.](quickstart-send-telemetry-java.md)

* Styra enheter interaktivt (till exempel aktivera en fläkt från en användarstyrd app) med [självstudien Använd direktmetoder.](quickstart-control-device-java.md)
