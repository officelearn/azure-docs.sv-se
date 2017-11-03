---
title: "Kom igång med Azure IoT Hub-enhet twins (Java) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub-enhet twins att lägga till taggar och sedan använda en IoT-hubb-fråga. Azure IoT-enhet SDK för Java används för att implementera appen enheten och tjänsten Azure IoT SDK för Java att implementera en service-app som lägger till taggar och IoT-hubb-frågan körs."
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
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: 6d306d4742a53789d8e69c80d7fbdfc4e1ade4bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-java"></a>Kom igång med enheten twins (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I kursen får skapa du två Java-konsolappar:

* **Lägg till-taggar-query**, en backend-Java-app som lägger till taggar och frågar enheten twins.
* **simulerade enheten**, en enhet Java-app som som ansluter till din IoT-hubb och rapporterar anslutningen villkor med hjälp av en rapporterade egenskap.

> [!NOTE]
> Artikeln [Azure IoT SDK](iot-hub-devguide-sdks.md) innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.

För att slutföra den här kursen behöver du:

* Senaste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Om du vill skapa enhetens identitet programmässigt läsa motsvarande avsnitt i den [ansluta enheten till din IoT-hubb som använder Java](iot-hub-java-java-getstarted.md#create-a-device-identity) artikel.

## <a name="create-the-service-app"></a>Skapa service-appen

I det här avsnittet skapar du en Java-app som platsen metadata läggs till som en tagg för att enheten dubbla i IoT-hubb som är associerade med **myDeviceId**. Appen frågor IoT-hubb för enheter som finns i USA och sedan för enheter som rapporterar en mobilnät-anslutning.

1. Skapa en tom mapp med namnet på utvecklingsdatorn, `iot-java-twin-getstarted`.

1. I den `iot-java-twin-getstarted` mapp, skapa ett Maven-projekt som kallas **Lägg till-taggar-query** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Kommandotolken, gå till den `add-tags-query` mapp.

1. Med en textredigerare, öppna den `pom.xml` filen i den `add-tags-query` mapp och Lägg till följande beroende på den **beroenden** nod. Det här beroendet kan du använda den **iot-service-klient** paketet i appen kommunicerar med IoT-hubben:

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

1. Med en textredigerare, öppna den `add-tags-query\src\main\java\com\mycompany\app\App.java` filen.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT-hubb-anslutningssträng som du antecknade i den *skapar en IoT-hubb* avsnitt:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Uppdatering av **huvudsakliga** Metodsignaturen att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Lägg till följande kod i den **huvudsakliga** metoden för att skapa den **DeviceTwin** och **DeviceTwinDevice** objekt. Den **DeviceTwin** objekt hanterar kommunikation med IoT-hubb. Den **DeviceTwinDevice** -objektet representerar enheten dubbla med dess egenskaper och taggar:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Lägg till följande `try/catch` blockera till den **huvudsakliga** metoden:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Uppdatera den **region** och **anläggningen** enheten dubbla taggar i din enhet dubbla Lägg till följande kod i den `try` block:

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

1. Om du vill fråga enheten twins i IoT-hubb, lägger du till följande kod i `try` block efter koden som du lade till i föregående steg. Koden körs två frågor. Varje fråga returnerar högst 100 enheter:

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

1. Spara och Stäng den `add-tags-query\src\main\java\com\mycompany\app\App.java` fil

1. Skapa den **Lägg till-taggar-query** app och korrigera eventuella fel. Kommandotolken, gå till den `add-tags-query` mappen och kör sedan följande kommando:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet skapar du en Java-konsolapp som anger ett rapporterat egenskapsvärde som skickas till IoT-hubb.

1. I den `iot-java-twin-getstarted` mapp, skapa ett Maven-projekt som kallas **simulerade enheten** med följande kommando vid en kommandotolk. Observera att detta är ett enda långt kommando:

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
    private static String deviceId = "myDeviceId";
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt. 

1. Lägg till följande kod i den **huvudsakliga** metod för att:
    * Skapa en klientenheter kommunicerar med IoT-hubben.
    * Skapa en **enhet** objekt att lagra dubbla Enhetsegenskaper.

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

1. Lägg till följande kod i den **huvudsakliga** metoden för att skapa en **connectivityType** rapporterade egenskapen och skicka den till IoT-hubb:

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
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Lägg till följande kod i slutet av den **huvudsakliga** metod. Väntar på att den **RETUR** nyckel kan tid för IoT-hubb att rapportera status för enheten dubbla åtgärder:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Spara och Stäng den `simulated-device\src\main\java\com\mycompany\app\App.java` filen.

1. Skapa den **simulerade enheten** app och korrigera eventuella fel. Kommandotolken, gå till den `simulated-device` mappen och kör sedan följande kommando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra konsolappar.

1. Vid en kommandotolk i den `add-tags-query` mapp, kör följande kommando för att köra den **Lägg till-taggar-query** service-appen:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-hubb service-appen att uppdatera värden och köra frågor för enhet](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Du kan se den **anläggningen** och **region** taggarna dubbla för enheten. Den första frågan returnerar enheten men inte i andra.

1. Vid en kommandotolk i den `simulated-device` mapp, kör följande kommando för att lägga till den **connectivityType** rapporteras egenskap till enheten dubbla:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Enhetsklienten lägger till den ** connectivityType ** rapporterade egenskapen](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. Vid en kommandotolk i den `add-tags-query` mapp, kör följande kommando för att köra den **Lägg till-taggar-query** service-appen en gång:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-hubb service-appen att uppdatera värden och köra frågor för enhet](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Nu enheten har skickat den **connectivityType** egenskap till IoT-hubben, den andra frågan returnerar enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du lagt till enhetsmetadata som taggar från en backend-app och skrev en enhetsapp till enheten anslutningsinformation i enheten dubbla. Du också fått lära dig hur man frågar dubbla enhetsinformation med hjälp av frågespråket i SQL-liknande IoT-hubb.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb](iot-hub-java-java-getstarted.md) kursen.
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app) med den [metoder från direkt](iot-hub-java-java-direct-methods.md) kursen.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
