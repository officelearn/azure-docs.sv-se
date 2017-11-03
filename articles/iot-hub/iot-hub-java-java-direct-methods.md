---
title: "Använda Azure IoT Hub direkt metoder (Java) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub direkt metoder. Azure IoT-enhet SDK för Java används för att implementera en simulerad enhetsapp som innehåller en direkt metod och Azure IoT SDK för Java att implementera ett service-appen som anropar metoden direkt."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 4fb759ecd7767c126bc22165494652039ba1caa4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-java"></a>Använda direkt metoder (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

I kursen får skapa du två Java-konsolappar:

* **anropa-direct-method**, en backend-Java-app som anropar en metod i appen simulerade enheten och visar svaret.
* **simulerade enheten**, en Java-app som simulerar en enhet som ansluter till din IoT-hubb med enhetens identitet som du skapar. Den här appen svarar direkt anropas från serverdelen.

> [!NOTE]
> Information om SDK: er som du kan använda för att bygga program ska köras på enheter och din lösningens serverdel finns [Azure IoT SDK][lnk-hub-sdks].

För att slutföra den här kursen behöver du:

* Java SE 8. <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar Java för den här självstudiekursen i Windows eller Linux.
* Maven 3.  <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar [Maven][lnk-maven] för den här självstudiekursen i Windows eller Linux.
* [Node.js-version 0.10.0 eller senare](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Java-konsolapp som svarar på en metod som anropas av lösningen tillbaka slutet.

1. Skapa en tom mapp som kallas iot-java-direct-metoden.

1. I mappen iot-java-direct-metoden skapar du ett Maven-projekt som kallas **simulerade enheten** med följande kommando vid en kommandotolk. Följande kommando är ett långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Gå till mappen simulated-device i Kommandotolken.

1. Använd en textredigerare och öppna filen pom.xml i mappen simulated-device och lägg till följande beroenden till noden **dependencies**. Det här beroendet kan du använda iot-enhet-klientpaketet i appen kommunicerar med IoT-hubben:

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

1. Öppna filen simulated-device\src\main\java\com\mycompany\app\App.java i en textredigerare.

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
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt. 

1. Lägg till följande kapslade klassen för att returnera en statuskod för din IoT-hubb i **App** klass:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Lägg till följande kapslade klassen för att hantera direkta metoden anrop från lösningens serverdel den **App** klass:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
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

1. Så här skapar du en **DeviceClient** och lyssna efter direkta metoden anrop, lägga till en **huvudsakliga** metod för att den **App** klass:

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Spara och stäng filen simulated-device\src\main\java\com\mycompany\app\App.java

1. Skapa den **simulerade enheten** app och korrigera eventuella fel. Navigera till mappen simulerade enheten och kör följande kommando vid en kommandotolk:

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>Anropa en metod som direkt på en enhet

I det här avsnittet skapar du en Java-konsolapp som anropar en direkt metod och visar sedan svaret. Den här konsolen appen ansluter till din IoT-hubb för att anropa metoden direkt.

1. I mappen iot-java-direct-metoden skapar du ett Maven-projekt som kallas **invoke-direct-metoden** med följande kommando vid en kommandotolk. Följande kommando är ett långt kommando:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Navigera till mappen invoke-direct-metod i en kommandotolk.

1. Med en textredigerare, öppna filen pom.xml i mappen invoke-direct-metoden och Lägg till följande beroende på den **beroenden** nod. Detta beroende kan du använda iot-service-klient-paketet i appen kommunicerar med IoT-hubben:

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

1. Använd en textredigerare och öppna filen invoke-direct-method\src\main\java\com\mycompany\app\App.java.

1. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT-hubb-anslutningssträng som du antecknade i den *skapar en IoT-hubb* avsnitt:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. Lägg till följande kod för att anropa metoden på den simulerade enheten den **huvudsakliga** metoden:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. Spara och stäng filen invoke-direct-method\src\main\java\com\mycompany\app\App.java

1. Skapa den **invoke-direct-method** app och korrigera eventuella fel. Navigera till mappen invoke-direct-metod i en kommandotolk och kör följande kommando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra konsolappar.

1. Kör följande kommando för att börja lyssna efter metodanrop från din IoT-hubb i en kommandotolk i mappen simulerade enhet:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-hubb simulerade enhetsapp att lyssna efter direkt metodanrop][8]

1. Kör följande kommando för att anropa en metod för den simulerade enheten från din IoT-hubb i en kommandotolk i mappen invoke-direct-metoden:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-hubb service-appen för att anropa en direkt metod][7]

1. Den simulerade enheten svarar på metodanropet direkt:

    ![Java IoT-hubb simulerade enhetsapp besvarar metodanropet direkt][9]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Denna enhetsidentitet använde för att aktivera appen simulerade enheten att ta hänsyn till metoder som anropas av molnet. Du kan också skapat en app som anropar metoder på enheten och visar svaret från enheten.

Om du vill utforska andra IoT-scenarier finns [schemalägga jobb på flera enheter][lnk-devguide-jobs].

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
