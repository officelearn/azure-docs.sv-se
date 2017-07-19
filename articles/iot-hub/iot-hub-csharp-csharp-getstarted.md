---
title: "Komma igång med Azure IoT Hub (.NET) | Microsoft Docs"
description: "Lär dig hur du skickar meddelanden från enheten till molnet i Azure IoT Hub med IoT SDK:er för .NET. Skapa appar för simulerade enheter och tjänster för att registrera din enhet, skicka meddelanden och läsa meddelanden från IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 103d64ea73c309f387ff90d181f472ad246d3026
ms.contentlocale: sv-se
ms.lasthandoff: 07/13/2017

---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-net"></a>Anslut din simulerade enhet till IoT Hub med hjälp av .NET
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutet av den här självstudiekursen har du tre .NET-konsolappar:

* **CreateDeviceIdentity**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta din app för simulerade enheter.
* **ReadDeviceToCloudMessages**, som visar telemetri som skickas av appen för simulerade enheter.
* **SimulatedDevice**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som skickar ett telemetrimeddelande varje sekund med hjälp av MQTT-protokollet.

Du kan hämta eller klona Visual Studio-lösningen som innehåller de tre apparna från Github.

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>Ta emot meddelanden från enheten till molnet
I det här avsnittet ska du skapa en .NET-konsolapp som läser ”enhet till molnet”-meddelanden från IoT Hub. En IoT Hub exponerar en [Azure Event Hubs][lnk-event-hubs-overview]-kompatibel slutpunkt så att du kan läsa meddelanden från enheter till molnet. För att göra det så enkelt som möjligt skapar vi en grundläggande läsare i den härs självstudiekursen som inte passar för distributioner med hög genomströmning. I självstudiekursen [Bearbeta meddelanden från enhet till moln][lnk-process-d2c-tutorial] står det hur du bearbetar ”enhet till molnet”-meddelanden i hög skala. Mer information om hur du bearbetar meddelanden från Event Hubs finns i självstudiekursen [Komma igång med Event Hubs][lnk-eventhubs-tutorial]. (Den här självstudiekursen gäller Event Hub-kompatibla slutpunkter i IoT Hub.)

> [!NOTE]
> Event Hub-kompatibla slutpunkter för läsning av meddelanden från enheter till molnet använder alltid AMQP-protokollet.
> 
> 

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Console App (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **ReadDeviceToCloudMessages**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][10a]
2. Högerklicka i Solution Explorer på projektet **ReadDeviceToCloudMessages** och klicka sedan på **Hantera NuGet-paket**.
3. I fönstret för **NuGet-pakethanteraren** letar du upp **WindowsAzure.ServiceBus**, väljer **Installera** och godkänner användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [Azure Service Bus][lnk-servicebus-nuget], med alla dess beroenden. Det här paketet gör att programmet kan ansluta till Event Hub-kompatibla slutpunkter i din IoT Hub.
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
   ```csharp
   using Microsoft.ServiceBus.Messaging;
   using System.Threading;
   ```
5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i avsnittet ”Skapa en IoT Hub”.
   
   ```csharp
   static string connectionString = "{iothub connection string}";
   static string iotHubD2cEndpoint = "messages/events";
   static EventHubClient eventHubClient;
   ```
6. Lägg till följande metod i klassen **Program**:
   
   ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
   ```
   
    Den här metoden använder en **EventHubReceiver**-instans för att ta emot meddelanden från alla IoT-hubbens ”enhet till molnet”-mottagarpartitioner. Observera att du skickar en `DateTime.Now`-parameter när du skapar **EventHubReceiver**-objektet, så att det bara tar emot meddelanden när det har startat. Detta filter är användbart i en testmiljö så att du kan se den aktuella uppsättningen meddelanden. I en produktionsmiljö ska din kod kontrollera att den bearbetar alla meddelanden. Mer information finns i självstudiekursen [Behandla meddelanden från enheten till molnet i IoT Hub][lnk-process-d2c-tutorial].
7. Slutligen lägger du till följande rader till **Main**-metoden:
   
   ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }  
    Task.WaitAll(tasks.ToArray());
   ```

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet ska du skapa en .NET-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT Hub.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Console App (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **SimulatedDevice**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][10b]
2. Högerklicka på projektet **SimulatedDevice** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **Microsoft.Azure.Devices.Client**, välj **Installera** för att installera **Microsoft.Azure.Devices.Client**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketet SDK för Azure IoT-enheter][lnk-device-nuget] och dess beroenden.
4. Lägg till följande `using`-instruktion högst upp i filen **Program.cs**:
   
   ```csharp
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   ```
5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdena med IoT-hubbvärdnamnet som du hämtade i avsnittet ”Skapa en IoT Hub” och enhetsnyckeln som du hämtade i avsnittet ”Skapa en enhetsidentitet”.
   
   ```csharp
   static DeviceClient deviceClient;
   static string iotHubUri = "{iot hub hostname}";
   static string deviceKey = "{device key}";
   ```

6. Lägg till följande metod i klassen **Program**:
   
   ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
   ```
    Den här metoden skickar ett nytt ”enhet till molnet”-meddelande varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID:t och ett slumpmässigt genererat nummer för att simulera en temperatursensor och en fuktighetssensor.
7. Slutligen lägger du till följande rader till **Main**-metoden:
   
   ```csharp
   Console.WriteLine("Simulated device\n");
   deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey), TransportType.Mqtt);

   SendDeviceToCloudMessagesAsync();
   Console.ReadLine();
   ```
   
   Som standard skapar metoden **Create** i en NET Framework-app en **DeviceClient**-instans som använder AMQP-protokollet för att kommunicera med IoT Hub (klienter med UWP och PCL använder HTTP som standard). Om du vill använda MQTT eller HTTP-protokollet använder du åsidosättandet av **Create**-metoden som gör att du kan ange protokollet. Om du använder HTTP-protokollet bör du också ta med namnområdet **System.Net.Http.Formatting** genom att lägga till NuGet-paketet **Microsoft.AspNet.WebApi.Client** till ditt projekt.

Den här självstudiekursen beskriver steg för steg hur du skapar en IoT Hub-simulerad enhetsapp. Du kan också lägga till nödvändig kod i enhetsprogrammet med hjälp av Visual Studio-tillägget [Ansluten tjänst för Azure IoT Hub][lnk-connected-service].

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. Högerklicka på din lösning i Solution Explorer i Visual Studio och klicka sedan på **Ange startprojekt**. Välj **Flera startprojekt** och välj sedan **Start** som åtgärden för både **ReadDeviceToCloudMessages**- och **SimulatedDevice**-projektet.
   
    ![Egenskaper för startprojekt][41]
2. Starta båda apparna genom att trycka på **F5**. Konsolens utdata från appen **SimulatedDevice** visar meddelandena som din simulerade enhetsapp skickar till IoT Hub. Konsolens utdata från appen **ReadDeviceToCloudMessages** visar meddelandena som din IoT Hub tar emot.
   
    ![Konsolens utdata från appar][42]
3. På panelen **Användning** på [Azure Portal][lnk-portal] kan du se hur många meddelanden som har skickats till IoT Hub:
   
    ![Panelen Användning på Azure Portal][43]

## <a name="next-steps"></a>Nästa steg
I de här självstudierna konfigurerade du en IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du använde den här enhetsidentiteten så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till IoT Hub. Du skapade också en app som visar meddelandena som tagits emot av IoT Hub. 

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Connecting your device][lnk-connect-device] (Ansluta din enhet)
* [Connecting your device][lnk-device-management] (Komma igång med enhetshantering)
* [Komma igång med IoT Edge][lnk-iot-edge]

Självstudiekursen [Bearbeta meddelanden från enhet till moln][lnk-process-d2c-tutorial] beskriver hur du utökar din IoT-lösning och bearbetar ”enhet till molnet”-meddelanden i hög skala.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

