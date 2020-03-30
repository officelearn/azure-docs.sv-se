---
title: Komma igång med Java (Azure IoT Hub device twins) | Microsoft-dokument
description: Så här använder du Azure IoT Hub-enhetstvillingar för att lägga till taggar och sedan använda en IoT Hub-fråga. Du använder Azure IoT-enheten SDK för Java för att implementera enhetsappen och Azure IoT-tjänsten SDK för Java för att implementera en tjänstapp som lägger till taggarna och kör IoT Hub-frågan.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e0114c37b2204a7ad1d7b0cf9c7f336dcd85883a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110499"
---
# <a name="get-started-with-device-twins-java"></a>Komma igång med enhetstvillingar (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I den här självstudien skapar du två Java-konsolappar:

* **add-tags-query**, en Java back-end-app som lägger till taggar och frågor enhet tvillingar.
* **simulerad enhet**, en Java-enhetsapp som ansluter till din IoT-hubb och rapporterar dess anslutningstillstånd med hjälp av en rapporterad egenskap.

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

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänstappen

I det här avsnittet skapar du en Java-app som lägger till platsmetadata som en tagg till enhetstvillingen i IoT Hub som är associerad med **myDeviceId**. Appen frågar först IoT-hubben för enheter som finns i USA och sedan för enheter som rapporterar en mobilnätverksanslutning.

1. På din utvecklingsmaskin skapar du en tom mapp med namnet **iot-java-twin-getstarted**.

2. Skapa ett Maven-projekt med namnet **add-tags-query** i mappen **iot-java-twin-getstarted** med följande kommando i kommandotolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Navigera till mappen **add-tags-query** i kommandotolken.

4. Öppna **filen pom.xml** i mappen **add-tags-query** med hjälp av en textredigerare och lägg till följande beroende i **beroendenoden.** Med det här beroendet kan du använda **iot-service-klientpaketet** i appen för att kommunicera med din IoT-hubb:

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

5. Lägg till följande **byggnod** efter **beroendenoden.** Den här konfigurationen instruerar Maven att använda Java 1.8 för att skapa appen.

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

7. Öppna **filen add-tags-query\src\main\java\com\mycompany\app\App.java** med hjälp av en textredigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med anslutningssträngen för IoT-hubb som du kopierade i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Uppdatera **main** huvudmetodsignaturen `throws` så att den innehåller följande sats:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Ersätt koden i **huvudmetoden** med följande kod för att skapa **DeviceTwin-** och **DeviceTwinDevice-objekten.** **DeviceTwin-objektet** hanterar kommunikationen med din IoT-hubb. **DeviceTwinDevice-objektet** representerar enhetstvillingen med dess egenskaper och taggar:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Lägg till `try/catch` följande block i **huvudmetoden:**

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Om du vill uppdatera **region-** och **anläggningsenhetens** dubbla `try` taggar i enhetstvillingen lägger du till följande kod i blocket:

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

14. Om du vill fråga om enhetstvillingarna `try` i IoT-hubb lägger du till följande kod i blocket efter koden som du lade till i föregående steg. Koden kör två frågor. Varje fråga returnerar högst 100 enheter.

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

15. Spara och stäng **filen add-tags-query\src\main\java\com\mycompany\app\App.java**

16. Skapa **appen add-tags-query** och korrigera eventuella fel. I kommandotolken navigerar du till mappen **add-tags-query** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet skapar du en Java-konsolapp som anger ett rapporterat egenskapsvärde som skickas till IoT Hub.

1. Skapa ett Maven-projekt med namnet **simulerad enhet** med följande kommando i kommandotolken i **iot-java-twin-getstarted-**

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. I kommandotolken navigerar du till mappen **med simulerade enheter.**

3. Öppna **filen pom.xml** i mappen **simulerad enhet** med hjälp av en textredigerare och lägg till följande beroenden i **beroendenoden.** Med det här beroendet kan du använda **iot-device-client-paketet** i appen för att kommunicera med din IoT-hubb.

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

7. Öppna filen **simulerad enhet\src\main\java\com\mycompany\app\App.java** med hjälp av en textredigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med enhetsanslutningssträngen som du kopierade i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt.

10. Lägg till följande metod i **klassen App** för att skriva ut information om dubbla uppdateringar:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Ersätt koden i **huvudmetoden** med följande kod till:

    * Skapa en enhetsklient för att kommunicera med IoT Hub.

    * Skapa ett **enhetsobjekt** för att lagra enhetstvillingegenskaperna.

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

12. Lägg till följande kod i **huvudmetoden** för att skapa en **connectivityType-rapporterad** egenskap och skicka den till IoT Hub:

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

13. Lägg till följande kod i **main** slutet av huvudmetoden. Väntar på **Enter** Enter-tangenten ger tid för IoT Hub att rapportera status för enhetens dubbla operationer.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Ändra signaturen för **main**-metoden och ta med undantagen som visas nedan:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Spara och stäng **filen simulerad enhet\src\main\java\com\mycompany\app\App.java.**

16. Skapa appen **med simulerade enheter** och korrigera eventuella fel. I kommandotolken navigerar du till mappen **simulerad enhet** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra konsolapparna.

1. Kör följande kommando i en kommandotolk i mappen add-tags-query för att köra tjänstappen för tilläggstaggar:At a command prompt in the **add-tags-query** folder, run the following command to run the **add-tags-query** service app:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub service app för att uppdatera taggvärden och köra enhetsfrågor](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Du kan se **anläggningen** och **regionen** taggar läggas till enheten tvilling. Den första frågan returnerar enheten, men den andra inte.

2. I en kommandotolk i mappen **simulerad enhet** kör du följande kommando för att lägga till **egenskapen connectivityType** reported till enhetstvillingen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Enhetsklienten lägger till den **connectivityType** rapporterade egenskapen](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. I en kommandotolk i mappen **add-tags-query** kör du följande kommando för att köra tjänstappen för **tilläggstaggar en** andra gång:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub service app för att uppdatera taggvärden och köra enhetsfrågor](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Nu när enheten har skickat **egenskapen connectivityType** till IoT Hub returnerar den andra frågan enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till enhetsmetadata som taggar från en backend-app och skrev en enhetsapp för att rapportera enhetsanslutningsinformation i enhetstvillingen. Du har också lärt dig hur du frågar enhetens tvillinginformation med hjälp av frågespråket SQL-liknande IoT Hub.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med självstudien [Kom igång med IoT Hub.](quickstart-send-telemetry-java.md)

* Styra enheter interaktivt (till exempel aktivera en fläkt från en användarstyrd app) med självstudiekursen [Använd direkta metoder.](quickstart-control-device-java.md)
