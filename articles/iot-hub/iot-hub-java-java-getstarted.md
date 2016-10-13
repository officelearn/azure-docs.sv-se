<properties
    pageTitle="Komma igång med Azure IoT Hub för Java | Microsoft Azure"
    description="Självstudiekurs: Komma igång med Azure IoT Hub för Java Använd Azure IoT Hub och Java med Microsoft Azure IoT SDK:er för att implementera ett IoT-lösning (Internet of Things)."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>


# Komma igång med Azure IoT Hub för Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutet av de här självstudierna har du tre Java-konsolprogram:

* **create-device-identity**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta dina simulerade enheter.
* **read-d2c-messages**, som visar telemetri som skickas av den simulerade enheten.
* **simulated-device**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som skickar ett telemetrimeddelande varje sekund med hjälp av AMQPS-protokollet.

> [AZURE.NOTE] Artikeln [IoT Hub SDK][lnk-hub-sdks] innehåller information om de olika SDK:er som du kan använda för att skapa båda programmen för körning på enheter och serverdelen i din lösning.

För att kunna genomföra den här kursen behöver du följande:

+ Java SE 8. <br/> [Förbered utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar Java för den här självstudiekursen i Windows eller Linux.

+ Maven 3.  <br/> [Förbered utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar Maven för den här självstudiekursen i Windows eller Linux.

+ Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Som ett sista steg antecknar du **primärnyckelvärdet** och klickar sedan på **Meddelanden**. På bladet **Meddelanden** noterar du **Händelsehubb-kompatibelt namn** och **Händelsehubb-kompatibel slutpunkt**. Du behöver dessa tre värden när du skapar **read-d2c-messages**-programmet.

![Aviseringsblad, Azure-portal, IoT Hub][6]

Nu har du skapat IoT-hubben och har IoT Hub-värdnamnet, IoT Hub-anslutningssträngen, det Event Hubs-kompatibla namnet och den Event Hubs-kompatibla slutpunkten som du behöver för att slutföra resten av kursen.

## Skapa en enhetsidentitet

I det här avsnittet ska du skapa en Java-konsolapp som skapar en ny enhetsidentitet i identitetsregistret i IoT-hubben. En enhet kan inte ansluta till IoT-hubbar om den inte har en post i enhetsidentitetsregistret. Mer information finns i avsnittet om **enhetsidentitetsregistret** i [IoT Hub Developer Guide][lnk-devguide-identity]. När du kör den här konsolappen genererar det ett unikt enhets-ID och en nyckel som din enhet kan använda för att identifiera sig själv när den skickar ”enhet-till-molnet”-meddelanden till IoT Hub.

1. Skapa en ny tom mapp med namnet iot-java-get-started. Skapa ett nytt Maven-projekt i mappen iot-java-get-started med namnet **create-device-identity** med hjälp av följande kommando i Kommandotolken. Observera att detta är ett enda långt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå till den nya mappen create-device-identity i Kommandotolken.

3. Använd en textredigerare och öppna filen pom.xml i mappen create-device-identity och lägg till följande beroende till noden **dependencies**. På så sätt kan du använda iothub-service-sdk-paketet i ditt program:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Spara och stäng filen pom.xml.

5. Använd en textredigerare och öppna filen create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Lägg till följande **Import**-instruktioner i filen:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Lägg till följande variabler på klassnivå till klassen **App** och ersätt **{yourhubconnectionstring}** med värdena som du noterade och skrev ned tidigare:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Ändra signaturen för **main**-metoden och ta med undantagen som visas nedan:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Lägg till följande kod som huvudstycket i **main**-metoden. Den här koden skapar en enhet med namnet *javadevice* i ditt IoT Hub-identitetsregister om den inte redan finns. Den visar sedan enhets-ID:t och enhetsnyckeln som du behöver senare:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Spara och stäng filen App.java.

11. Skapa programmet **create-device-identity** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen create-device-identity:

    ```
    mvn clean package -DskipTests
    ```

12. Kör programmet **create-device-identity** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen create-device-identity:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Skriv ner **Enhets-ID** och **Enhetsnyckel**. Du behöver dessa senare när du skapar ett program som ansluter till IoT Hub som en enhet.

> [AZURE.NOTE] IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till hubben. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [IoT Hub Developer Guide][lnk-devguide-identity].

## Ta emot meddelanden från enheten till molnet

I det här avsnittet ska du skapa en Java-konsolapp som läser ”enhet till molnet”-meddelanden från IoT Hub. En IoT-hubb exponerar en [Event Hubs-][lnk-event-hubs-overview]-kompatibel slutpunkten så att du kan läsa meddelanden från enheten till molnet. För att göra det så enkelt som möjligt skapar vi en grundläggande läsare i den härs självstudiekursen som inte passar för distributioner med hög genomströmning. I självstudiekursen [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] lär du dig hur du bearbetar ”enhet till molnet”-meddelanden i hög skala. Självstudierna [Komma igång med Event Hubs][lnk-eventhubs-tutorial] innehåller ytterligare information om hur du bearbetar meddelanden från Event Hubs och gäller Event Hubs-kompatibla slutpunkter i IoT Hub.

> [AZURE.NOTE] Event Hubs-kompatibla slutpunkter för läsning av meddelanden från enheter till molnet använder alltid AMQPS-protokollet.

1. I mappen iot-java-get-started som du skapade i avsnittet *Skapa en enhetsidentitet* skapar du ett nytt Maven-projekt med namnet **read-d2c-messages** genom att köra följande kommando i Kommandotolken. Observera att detta är ett enda långt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå till den nya mappen read-d2c-messages i Kommandotolken.

3. Använd en textredigerare och öppna filen pom.xml i mappen read-d2c-messages och lägg till följande beroende till noden **dependencies**. På så sätt kan du använda eventhubs-client-paketet i ditt program för att läsa från den Event Hubs-kompatibla slutpunkten:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Spara och stäng filen pom.xml.

5. Öppna filen read-d2c-messages\src\main\java\com\mycompany\app\App.java i en textredigerare.

6. Lägg till följande **Import**-instruktioner i filen:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt **{youriothubkey}**, **{youreventhubcompatibleendpoint}** och **{youreventhubcompatiblename}** med de värden som du skrev ner tidigare:

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Lägg till följande **receiveMessages**-metod till klassen **App**. Den här metoden skapar en **EventHubClient**-instans för att ansluta till den Event Hubs-kompatibla slutpunkten och skapar sedan en **PartitionReceiver**-instans asynkront för att läsa från Event Hubs-partitionen. Den loopar kontinuerligt och skriver ut meddelandeinformationen tills programmet avslutas.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Den här metoden använder ett filter när den skapar mottagaren så att mottagaren endast läser meddelanden som skickas till IoT Hub efter att mottagaren har börjat köra. Detta är användbart i en testmiljö så att du kan se den aktuella uppsättningen meddelanden. I en produktionsmiljö bör koden se till att alla meddelanden bearbetas. Mer information finns i självstudierna [Behandla meddelanden från enheten till molnet i IoT Hub][lnk-process-d2c-tutorial].

9. Ändra signaturen för **main**-metoden och ta med undantagen som visas nedan:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Lägg till följande kod i metoden **main** i klassen **App**. Den här koden skapar två **EventHubClient**- och **PartitionReceiver**-instanser och gör att du kan stänga programmet när du har bearbetat meddelandena:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Den här koden förutsätter att du skapade IoT-hubben på F1-nivån (gratis). En kostnadsfri IoT-hubb har två partitioner med namnen ”0” och ”1”.

11. Spara och stäng filen App.java.

12. Skapa programmet **read-d2c-messages** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen read-d2c-messages-meddelanden:

    ```
    mvn clean package -DskipTests
    ```

## Skapa en simulerad enhetsapp

I det här avsnittet ska du skapa en Java-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT-hubb.

1. I mappen iot-java-get-started som du skapade i avsnittet *Skapa en enhetsidentitet* skapar du ett nytt Maven-projekt med namnet **imulated-device** genom att köra följande kommando i Kommandotolken. Observera att detta är ett enda långt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå till mappen simulated-device i Kommandotolken.

3. Använd en textredigerare och öppna filen pom.xml i mappen simulated-device och lägg till följande beroenden till noden **dependencies**. På så sätt kan du använda iothub-java-client-paketet i ditt program för att kommunicera med IoT-hubben och för att serialisera Java-objekt till JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Spara och stäng filen pom.xml.

5. Öppna filen simulated-device\src\main\java\com\mycompany\app\App.java i en textredigerare.

6. Lägg till följande **Import**-instruktioner i filen:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Lägg till följande variabler på klassnivå i klassen **App** och ersätt **{youriothubname}** med namnet på din IoT-hubb och **{yourdevicekey}** med de enhetsvärden som du genererade i avsnittet *Skapa en enhetsidentitet*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Det här exempelprogrammet använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt. Du kan använda antingen HTTPS- eller AMQPS-protokollet för att kommunicera med IoT Hub.

8. Lägg till följande kapslade **TelemetryDataPoint**-klass inuti klassen **App** för att ange de telemetridata som enheten skickar till IoT-hubben:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Lägg till följande kapslade **EventCallback**-klass inuti klassen **App** för att visa bekräftelsestatusen som IoT-hubben returnerar när den bearbetar ett meddelande från den simulerade enheten. Den här metoden meddelar även huvudtråden i programmet när meddelandet har bearbetats:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Lägg till följande kapslade **MessageSender**-klass inuti klassen **App**. Metoden **Kör** i den här klassen genererar exempeltelemetridata som skickas till IoT-hubben och väntar på en bekräftelse innan nästa meddelande skickas:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Den här metoden skickar ett nytt ”enhet till molnet”-meddelande en sekund efter att IoT-hubben bekräftar det föregående meddelandet. Meddelandet innehåller ett JSON-serialiserat objekt med deviceId och ett slumpmässigt genererat nummer för att simulera en vindhastighetssensor.

11. Ersätt metoden **main** med följande kod som skapar en tråd för att skicka ”enhet till molnet”-meddelanden till din IoT-hubb:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Spara och stäng filen App.java.

13. Skapa programmet **simulated-device** med Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff) vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].

## Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando i Kommandotolken i mappen read-d2c för att börja övervaka den första partitionen i din IoT-hubb:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-tjänstens klientprogram för att övervaka meddelanden från enheten till molnet][7]

2. Kör följande kommando i Kommandotolken i mappen simulated-device för att börja skicka telemetridata till din IoT-hubb:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Java IoT Hub-tjänstens klientprogram för att skicka meddelanden från enheten till molnet][8]

3. På panelen **Användning** på [Azure-portalen][lnk-portal] kan du se hur många meddelanden som skickats till hubben:

    ![Azure-portal Användningspanel som visar antalet meddelanden som har skickats till IoT Hub][43]

## Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT-hubb på portalen och skapade sedan en enhetsidentitet i hubbens identitetsregister. Du använde den här enhetsidentiteten så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till hubben. Du skapade också en app som visar meddelandena som tagits emot av hubben. 

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

- [Ansluta din enhet][lnk-connect-device]
- [Komma igång med enhetshantering][lnk-device-management]
- [Komma igång med Gateway-SDK][lnk-gateway-SDK]

I självstudien [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] lär du dig hur du utökar din IoT-lösning och behandlar ”enhet till molnet”-meddelanden i hög skala.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Oct16_HO1-->


