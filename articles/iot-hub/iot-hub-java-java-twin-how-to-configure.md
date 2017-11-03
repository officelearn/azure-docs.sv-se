---
title: "Använd Azure IoT Hub dubbla enhetsegenskaper (Java) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub-enhet twins för att konfigurera enheter. Azure IoT-enhet SDK för Java används för att implementera en simulerad enhetsapp och tjänsten Azure IoT SDK för Java att implementera ett service-appen som ändrar en konfiguration för enheter med hjälp av en enhet dubbla."
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
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Använd önskade egenskaper för att konfigurera enheter

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

I slutet av den här kursen har du två appar som Java-konsolen:

* **simulerade enheten**, en simulerad enhetsapp som väntar på en uppdatering för önskad konfiguration och rapporterar status för en simulerad konfigurationsprocessen för uppdateringen.
* **Ange konfiguration**, en backend-app, vilket anger du önskad konfiguration på en enhet och frågar konfigurationsprocessen för uppdateringen.

> [!NOTE]
> Artikeln [Azure IoT SDK] [ lnk-hub-sdks] innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Om du har följt den [Kom igång med enheten twins] [ lnk-twin-tutorial] kursen har du redan en IoT-hubb och en enhetsidentitet kallas **myDeviceId**. I så fall kan du hoppa till den [skapa den simulerade enhetsapp] [ lnk-how-to-configure-createapp] avsnitt.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Skapa den simulerade enhetsapp
I det här avsnittet skapar du en Java-konsolapp som ansluter till din hubb som **myDeviceId**, väntar på en uppdatering för önskad konfiguration och rapporterar uppdateringar på uppdateringsprocessen simulerade konfiguration.

1. Skapa en tom mapp som kallas dt-get-started.

1. Skapa ett Maven-projekt kallas i mappen dt-get-started **simulerade enheten** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Gå till mappen simulated-device i Kommandotolken.

1. Med en textredigerare, öppna filen pom.xml i mappen simulerade enhet och Lägg till följande beroende på den **beroenden** nod. Detta beroende kan du använda iot-service-klient-paketet i appen kommunicerar med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot enhetsklienten** [Maven sökning] [lnk-maven-enhet – Sök].

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
    import java.util.Scanner;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt **{yourdeviceconnectionstring}** med den anslutningssträng för enheten som du antecknade i den *skapa en enhetsidentitet* avsnitt:

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Lägg till följande kapslade klassen för att implementera en motringning hanterare för enheten dubbla statushändelser (för felsökning), den **App** klass:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Lägg till följande kapslade klassen för att den **App** klass:

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > TelemetryConfig-klassen utökar den [enhetsklass] [ lnk-java-device-class] att få tillgång till önskade egenskaper återanrop.

1. Ändra signaturen för den **huvudsakliga** metod för att utlösa följande undantag:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Lägg till följande kod i den **huvudsakliga** metod för att skapa en instans av en **DeviceClient** och **TelemetryConfig**:

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Lägg till följande kod i den **huvudsakliga** metod för att starta enheten dubbla tjänster:

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Lägg till följande kod i den **huvudsakliga** metod för att stänga av enheten simulatorn vid behov:

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Spara och stäng filen simulated-device\src\main\java\com\mycompany\app\App.java.

1. Skapa den **simulerade enheten** backend-app och korrigera eventuella fel. Navigera till mappen simulerade enheten och kör följande kommando vid en kommandotolk:

    `mvn clean package -DskipTests`

   > [!NOTE]
   > Den här självstudiekursen simulerar inte någon funktion för samtidiga konfigurationsuppdateringar. Vissa processer för uppdatering av konfiguration kanske kan hantera förändringar av mål-konfiguration när uppdateringen körs, kanske vissa måste placeras i kö och vissa kan avvisa dem med ett feltillstånd. Se till att tänka på önskat beteende för din specifika konfigurationsprocessen och Lägg till lämpliga logiken innan du påbörjar konfigurationsändringen.
   > 
   > 

## <a name="create-the-service-app"></a>Skapa service-appen
I det här avsnittet skapar du en Java-konsolapp som uppdaterar det *önskade egenskaper* på enhet-dubbla som är associerade med **myDeviceId** med ett nytt telemetri configuration-objekt. Sedan frågar enheten-twins lagras i IoT-hubb och visar skillnaden mellan önskade och rapporterade konfigurationer av enheten.

1. Skapa ett Maven-projekt kallas i mappen dt-get-started **ange konfiguration** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Navigera till mappen ange konfiguration i en kommandotolk.

1. Med en textredigerare, öppna filen pom.xml i mappen utlösare omstart och Lägg till följande beroende på den **beroenden** nod. Detta beroende kan du använda iot-service-klient-paketet i appen kommunicerar med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-klient** [Maven sökning] [lnk-maven-tjänsten-Sök].

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

1. Använd en textredigerare och öppna källfilen set-configuration\src\main\java\com\mycompany\app\App.java.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt **{youriothubconnectionstring}** med din IoT-hubb-anslutningssträng som du antecknade i den *skapar en IoT-hubb* avsnitt:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Lägg till följande kod för att fråga och uppdatera enheten twins på den simulerade enheten den **huvudsakliga** metoden:

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Spara och stäng filen set-desired-configuration\src\main\java\com\mycompany\app\App.java.

1. Skapa den **ange konfiguration** backend-app och korrigera eventuella fel. Navigera till mappen ange konfiguration och kör följande kommando vid en kommandotolk:

    `mvn clean package -DskipTests`
   
    Den här koden hämtar enheten dubbla för **myDeviceId**, och uppdaterar sedan dess egenskaper med ett nytt telemetri configuration-objekt.
    Efter att den frågar enheten-twins lagras i IoT-hubben var 10: e sekund och skriver ut önskad och rapporterade telemetri konfigurationer. Referera till den [IoT-hubb frågespråket] [ lnk-query] att lära dig hur du skapar omfattande rapporter på alla enheter.
   
   > [!IMPORTANT]
   > Det här programmet frågar IoT-hubb var 10: e sekund som illustration förrän enheten har uppdaterats. Använda frågor för att generera användarinriktad rapporter över flera enheter och inte för att identifiera ändringar. Om din lösning kräver meddelanden i realtid av enhetshändelser, använder [dubbla meddelanden][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Det finns en fördröjning på upp till en minut mellan enheten rapporten igen och frågeresultatet. Detta är att aktivera query-infrastruktur för att arbeta med mycket hög skala.  Att hämta konsekvent vyer för en enskild enhet dubbel användning av **getDeviceTwin** metod i den **DeviceTwin** klass.
   > 
   > 

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. Kör följande kommando för att börja lyssna efter enheten dubbla anrop från din IoT-hubb i en kommandotolk i mappen simulerade enhet:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Kör följande kommando för att fråga efter och uppdatera enheten twins på den simulerade enheten från din IoT-hubb i en kommandotolk i mappen ange konfiguration:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Den simulerade enheten svarar på omstart direkt metodanropet:

    ![Java IoT-hubb simulerade enhetsapp svarar på enheten dubbla anropet][img-deviceconfigured]

## <a name="next-steps"></a>Nästa steg
I kursen får du ange en önskad konfiguration som *önskade egenskaper* från lösningen serverdel och skrev en enhetsapp för att identifiera den ändringen och simulera en flera steg uppdateringsprocess reporting dess status i rapporterade egenskaper.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb] [ lnk-iothub-getstarted] självstudiekursen
* schemalägga eller utföra åtgärder på stora mängder enheter finns i [schema och broadcast jobb] [ lnk-schedule-jobs] kursen.
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app), med den [metoder från direkt] [ lnk-methods-tutorial] kursen.

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
