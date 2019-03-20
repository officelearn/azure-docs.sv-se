---
title: Kom igång med enhetstvillingar för Azure IoT Hub (Java) | Microsoft Docs
description: Så här använder enhetstvillingar för Azure IoT Hub för att lägga till taggar och sedan använda en IoT Hub-fråga. Du kan använda Azure IoT-enhetens SDK för Java för att implementera app för enheter och tjänsten Azure IoT SDK för Java för att implementera en service-app som lägger till taggar och kör IoT Hub-frågan.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.openlocfilehash: bfb111b07db105190fc59f21b3255c2ea2b1471c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081023"
---
# <a name="get-started-with-device-twins-java"></a>Kom igång med enhetstvillingar (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I den här självstudien skapar du två Java-konsolappar:

* **Lägg till-taggar-query**, en backend-Java-app som lägger till taggar och frågar enhetstvillingar.
* **simulated-device**, en Java-enhetsapp som ansluter till din IoT-hubb och rapporterar tillståndet anslutning med hjälp av en rapporterad egenskap.

> [!NOTE]
> Artikeln [Azure IoT SDK: er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK: er som du kan använda för att skapa appar för både enheten och backend-server.

För att slutföra den här kursen behöver du:

* Senaste [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Skapa service-app

I det här avsnittet skapar du ett Java-program som lägger till platsmetadata som en tagg till enhetstvillingen i IoT Hub som är associerade med **myDeviceId**. Appen frågar först IoT-hubb för enheter som finns i USA och sedan för enheter som rapporterar anslutning till ett mobilt nätverk.

1. Skapa en tom mapp med namnet på en utvecklingsdator måste `iot-java-twin-getstarted`.

2. I den `iot-java-twin-getstarted` mapp, skapa ett Maven-projekt som heter **Lägg till-taggar-query** med följande kommando i Kommandotolken. Observera att detta är ett enda långt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. I Kommandotolken, navigera till den `add-tags-query` mapp.

4. Använd en textredigerare och öppna den `pom.xml` fil i den `add-tags-query` mapp och Lägg till följande beroende till den **beroenden** noden. Det här beroendet gör att du kan använda den **iot-service-client** paketet i din app att kommunicera med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

7. Använd en textredigerare och öppna den `add-tags-query\src\main\java\com\mycompany\app\App.java` filen.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT hub-anslutningssträngen som du antecknade i den *skapar en IoT Hub* avsnittet:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Uppdatera den **huvudsakliga** signaturen för metoden att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Lägg till följande kod till den **huvudsakliga** metod för att skapa den **DeviceTwin** och **DeviceTwinDevice** objekt. Den **DeviceTwin** objekt hanterar kommunikationen med din IoT-hubb. Den **DeviceTwinDevice** -objektet representerar enhetstvillingen med dess egenskaper och taggar:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Lägg till följande `try/catch` blockera till den **huvudsakliga** metoden:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Att uppdatera den **region** och **anläggning** device twin taggar i din enhetstvilling Lägg till följande kod i den `try` block:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Om du vill fråga efter enhetstvillingar i IoT hub, lägger du till följande kod till den `try` block efter den du lade till i föregående steg. Koden körs två frågor. Varje fråga returnerar högst 100 enheter:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Spara och Stäng den `add-tags-query\src\main\java\com\mycompany\app\App.java` fil

16. Skapa den **Lägg till-taggar-query** app och korrigera eventuella fel. I Kommandotolken, navigera till den `add-tags-query` mappen och kör sedan följande kommando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet skapar du en Java-konsolapp som anger en rapporterad egenskap-värde som skickas till IoT Hub.

1. I den `iot-java-twin-getstarted` mapp, skapa ett Maven-projekt som heter **simulated-device** med följande kommando i Kommandotolken. Observera att detta är ett enda långt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. I Kommandotolken, navigera till den `simulated-device` mapp.

3. Använd en textredigerare och öppna den `pom.xml` fil i den `simulated-device` mapp och Lägg till följande beroenden till den **beroenden** noden. Det här beroendet gör att du kan använda den **iot-device-client** paketet i din app att kommunicera med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.14.2</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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
    private static String deviceId = "myDeviceId";
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt. 

1. Lägg till följande metod för att den **App** klassen för att skriva ut information om uppdateringar för enhetstvilling:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

9. Lägg till följande kod till den **huvudsakliga** metod för att:
    * Skapa en klientenhet att kommunicera med IoT Hub.
    * Skapa en **enhet** objekt för att lagra tvillingegenskaper.

      ```java
      DeviceClient client = new DeviceClient(connString, protocol);

      // Create a Device object to store the device twin properties
      Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
      };
      ```

10. Lägg till följande kod till den **huvudsakliga** metod för att skapa en **connectivityType** rapporterade egenskap och skicka den till IoT Hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

11. Lägg till följande kod i slutet av den **huvudsakliga** metod. Väntar på den **RETUR** nyckel kan tid för IoT Hub för att rapportera status för de två åtgärderna:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Ändra signaturen för **main**-metoden och ta med undantagen som visas nedan:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

1. Spara och Stäng den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

13. Skapa den **simulated-device** app och korrigera eventuella fel. I Kommandotolken, navigera till den `simulated-device` mappen och kör sedan följande kommando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Köra apparna

Du är nu redo att köra konsolappar.

1. I en kommandotolk i den `add-tags-query` mapp, kör följande kommando för att köra den **Lägg till-taggar-query** service-app:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-tjänstapp för att uppdatera taggvärden och köra enhetsfrågor](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Du kan se den **anläggning** och **region** taggar som lagts till i enhetstvillingen. Den första frågan returnerar enheten, men inte i andra.

2. I en kommandotolk i den `simulated-device` mapp, kör följande kommando för att lägga till den **connectivityType** rapporterade egenskap till enhetstvillingen:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Enhetsklienten lägger till den ** connectivityType ** rapporterade egenskap](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. I en kommandotolk i den `add-tags-query` mapp, kör följande kommando för att köra den **Lägg till-taggar-query** service-appen en gång:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-tjänstapp för att uppdatera taggvärden och köra enhetsfrågor](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Nu enheten har skickat den **connectivityType** egenskapen till IoT Hub, den andra frågan returnerar din enhet.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagts till enhetsmetadata som taggar från en backend-app och skrev en enhetsapp till rapporten anslutning enhetsinformation i enhetstvillingen. Du också lärt dig hur du frågar twin enhetsinformationen med SQL-liknande IoT Hub-frågespråk.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT Hub](quickstart-send-telemetry-java.md) självstudien.

* Kontrollera enheter interaktivt (till exempel aktivera en fans, från en användarstyrd app) med den [Använd direkta metoder](quickstart-control-device-java.md) självstudien.