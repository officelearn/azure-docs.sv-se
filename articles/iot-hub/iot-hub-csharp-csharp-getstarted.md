<properties
    pageTitle="Komma igång med Azure IoT Hub för C# | Microsoft Azure"
    description="Självstudiekurs om hur du kommer igång med Azure IoT Hub med C#. Använd Azure IoT Hub och C# med Microsoft Azure IoT-SDK:er för att implementera en IoT-lösning (Internet of Things)."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>


# Komma igång med Azure IoT Hub för .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutet av de här självstudierna har du tre Windows-konsolprogram:

* **CreateDeviceIdentity**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta dina simulerade enheter.
* **ReadDeviceToCloudMessages**, som visar telemetri som skickas av den simulerade enheten.
* **SimulatedDevice**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som skickar ett telemetrimeddelande varje sekund med hjälp av AMQPS-protokollet.

> [AZURE.NOTE] Information om de olika SDK:er som du kan använda för att skapa båda programmen för körning på enheter och serverdelen i din lösning finns i [IoT Hub-SDK:er][lnk-hub-sdks].

För att kunna genomföra den här kursen behöver du följande:

+ Microsoft Visual Studio 2015.

+ Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT-hubb och du har värdnamnet och anslutningssträngen som du behöver för att slutföra resten av den här kursen.

## Skapa en enhetsidentitet

I det här avsnittet skapar du en Windows-konsolapp som skapar en enhetsidentitet i identitetsregistret i IoT-hubben. En enhet kan inte ansluta till IoT-hubbar om den inte har en post i enhetsidentitetsregistret. Mer information finns i avsnittet om enhetsidentitetsregistret i [IoT Hub Developer Guide][lnk-devguide-identity]. När du kör den här konsolappen genererar det ett unikt enhets-ID och en nyckel som din enhet kan använda för att identifiera sig själv när den skickar ”enhet-till-molnet”-meddelanden till IoT Hub.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **CreateDeviceIdentity**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][10]

2. Högerklicka i Solution Explorer på projektet **CreateDeviceIdentity** och klicka sedan på **Hantera NuGet-paket**.

3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] NuGet-paketet och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][11]

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen för IoT-hubben som du skapade i föregående avsnitt.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Lägg till följande metod i klassen **Program**:

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Den här metoden skapar en enhetsidentitet med ID:t **myFirstDevice**. (Om enhets-ID:t redan finns i registret hämtar koden bara den befintliga enhetsinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade enheten för att ansluta till din IoT-hubb.

7. Slutligen lägger du till följande rader till **Main**-metoden:

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Kör det här programmet och notera enhetsnyckeln.

    ![Enhetsnyckel som genereras av programmet][12]

> [AZURE.NOTE] IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att aktivera säker åtkomst till hubben. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [IoT Hub Developer Guide][lnk-devguide-identity].

## Ta emot meddelanden från enheten till molnet

I det här avsnittet skapar du en Windows-konsolapp som läser ”enhet till molnet”-meddelanden från IoT Hub. En IoT-hubb exponerar en [Azure Event Hubs][lnk-event-hubs-overview]-kompatibel slutpunkt så att du kan läsa meddelanden från enheten till molnet. För att göra det så enkelt som möjligt skapar vi en grundläggande läsare i den härs självstudiekursen som inte passar för distributioner med hög genomströmning. I självstudiekursen [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] lär du dig hur du behandlar ”enhet till molnet”-meddelanden i hög skala. Mer information om hur du bearbetar meddelanden från Event Hubs finns i självstudiekursen [Komma igång med Event Hubs][lnk-eventhubs-tutorial]. (Den här självstudiekursen gäller Event Hubs-kompatibla slutpunkter i IoT Hub.)

> [AZURE.NOTE] Event Hubs-kompatibla slutpunkter för läsning av meddelanden från enheter till molnet använder alltid AMQPS-protokollet.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **ReadDeviceToCloudMessages**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][10]

2. Högerklicka i Solution Explorer på projektet **ReadDeviceToCloudMessages** och klicka sedan på **Hantera NuGet-paket**.

3. I fönstret för **NuGet-pakethanteraren** letar du upp **WindowsAzure.ServiceBus**, väljer **Installera** och godkänner användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [Azure Service Bus][lnk-servicebus-nuget], med alla dess beroenden. Det här paketet gör att programmet kan ansluta till Event Hubs-kompatibla slutpunkter i din IoT-hubb.

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen för IoT-hubben som du skapade i avsnittet ”Skapa en IoT-hubb”.

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Lägg till följande metod i klassen **Program**:

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

    Den här metoden använder en **EventHubReceiver**-instans för att ta emot meddelanden från alla IoT-hubbens ”enhet till molnet”-mottagarpartitioner. Observera att du skickar en `DateTime.Now`-parameter när du skapar **EventHubReceiver**-objektet, så att det bara tar emot meddelanden när det har startat. Detta filter är användbart i en testmiljö så att du kan se den aktuella uppsättningen meddelanden. I en produktionsmiljö ska din kod kontrollera att den bearbetar alla meddelanden. Mer information finns i självstudiekursen [Behandla meddelanden från enheten till molnet i IoT Hub][lnk-process-d2c-tutorial].

7. Slutligen lägger du till följande rader till **Main**-metoden:

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

## Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Windows-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT-hubb.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **SimulatedDevice**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][10]

2. Högerklicka på projektet **SimulatedDevice** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.

3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **Microsoft.Azure.Devices.Client**, välj **Installera** för att installera **Microsoft.Azure.Devices.Client**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketet SDK för Azure IoT-enheter][lnk-device-nuget] och dess beroenden.

4. Lägg till följande `using`-instruktion högst upp i filen **Program.cs**:

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdena med IoT-hubbvärdnamnet som du hämtade i avsnittet ”Skapa en IoT-hubb” och enhetsnyckeln som du hämtade i avsnittet ”Skapa en enhetsidentitet”.

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Lägg till följande metod i klassen **Program**:

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Den här metoden skickar ett nytt ”enhet till molnet”-meddelande  varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID:t och ett slumpmässigt genererat nummer för att simulera en vindhastighetssensor.

7. Slutligen lägger du till följande rader till **Main**-metoden:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Som standard skapar metoden **Create** en **DeviceClient**-instans som använder AMQP-protokollet för att kommunicera med IoT Hub. Om du vill använda HTTPS-protokollet använder du åsidosättningen av **Create**-metoden som gör att du kan ange protokollet. Om du använder HTTPS-protokollet bör du också ta med namnrymden **System.Net.Http.Formatting** genom att lägga till NuGet-paketet **Microsoft.AspNet.WebApi.Client** till ditt projekt.

Den här självstudiekursen beskriver steg för steg hur du skapar en IoT Hub-klientenhet. Du kan också lägga till nödvändig kod i enhetsklientprogrammet med hjälp av Visual Studio-tillägget [Ansluten tjänst för Azure IoT Hub][lnk-connected-service].


> [AZURE.NOTE] För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff) vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].

## Köra programmen

Nu är det dags att köra programmen.

1.  Högerklicka på din lösning i Solution Explorer i Visual Studio och klicka sedan på **Ange startprojekt**. Välj **Flera startprojekt** och välj sedan **Start** som åtgärden för både **ReadDeviceToCloudMessages**- och **SimulatedDevice**-projektet.

    ![Egenskaper för startprojekt][41]

2.  Starta båda apparna genom att trycka på **F5**. Konsolens utdata från appen **SimulatedDevice** visar meddelandena som din simulerade enhet skickar till IoT-hubben. Konsolens utdata från appen **ReadDeviceToCloudMessages** visar meddelandena som din IoT-hubb tar emot.

    ![Konsolens utdata från appar][42]

3. På panelen **Användning** på [Azure-portalen][lnk-portal] kan du se hur många meddelanden som skickats till hubben:

    ![Panelen Användning på Azure-portalen][43]


## Nästa steg

I de här självstudierna konfigurerade du en IoT-hubb på portalen och skapade sedan en enhetsidentitet i hubbens identitetsregister. Du använde den här enhetsidentiteten så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till hubben. Du skapade också en app som visar meddelandena som tagits emot av hubben. 

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

- [Ansluta din enhet][lnk-connect-device]
- [Komma igång med enhetshantering][lnk-device-management]
- [Komma igång med Gateway-SDK][lnk-gateway-SDK]

I självstudien [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] lär du dig hur du utökar din IoT-lösning och behandlar ”enhet till molnet”-meddelanden i hög skala.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Sep16_HO3-->


