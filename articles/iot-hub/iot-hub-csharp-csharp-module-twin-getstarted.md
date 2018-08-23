---
title: Kom igång med Azure IoT Hub-modulidentitet och -modultvilling (.NET) | Microsoft Docs
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med IoT SDK:er för .NET.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 8d5d4ab85d8441998fd384e01f85d1d427d68cc2
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054979"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Kom igång med IoT Hub identitets- och modulen modultvilling med hjälp av .NET-serverdel och .NET-enhet

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentiteten och enhetstvillingen gör att serverdelsprogrammet kan konfigurera en enhet och ger synlighet för enhetstillståndet tillhandahåller en modulidentitet och en modultvilling dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **CreateIdentities**, som skapar en enhetsidentitet, en modulidentitet och en associerad säkerhetsnyckel för att ansluta enheten och modulklienterna.
* **UpdateModuleTwinReportedProperties**, som skickar uppdaterade rapporterade egenskaper för modultvillingen till din IoT Hub.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

1. **Skapa ett Visual Studio-projekt**I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den befintliga lösningen med hjälp av projektmallen **Console App (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare. Ge projektet namnet **UpdateModuleTwinReportedProperties**.

    ![Skapa ett Visual Studio-projekt][13]

2. **Installera den senaste Azure IoT Hub .NET-enheten-SDK** -modultvilling för identitets- och modulen finns i offentlig förhandsversion. Den finns bara tillgänglig i IoT Hub-förhandsversionens enhets-SDK:er. I Visual Studio öppnar du Verktyg > Nuget-pakethanteraren > Hantera NuGet-paket för lösningen. Sök efter Microsoft.Azure.Devices.Client. Se till att du markerar kryssrutan för att inkludera förhandsversion. Välj den senaste versionen och installera. Nu har du åtkomst till alla modulfunktioner. 

    ![Installera Azure IoT Hub .NET-tjänstens SDK V1.16.0-preview-005][14]

3. **Hämta modulens anslutningssträng** – nu om du loggar in på [Azure-portalen][lnk-portal]. Gå till din IoT Hub och klicka på IoT-enheter. Leta rätt på myFirstDevice och öppna den, så ser du att myFirstModule har skapats. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

    ![Information om Azure-portalmodulen][15]

4. **Skapa UpdateModuleTwinReportedProperties-konsolapp** Lägg till följande `using`-instruktioner överst i **Program.cs**-filen:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", status, reason);
    }
    ```

    Lägg till följande metod **OnDesiredPropertyChanged** till klassen **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Det är kodexemplet visar hur du hämtar modultvillingen och uppdaterar rapporterade egenskaper med AMQP-protokollet. I offentlig förhandsversion stöder vi endast AMQP för modultvillingåtgärder.

5. Förutom ovanstående **Main** metod, du kan lägga till nedan kodblock skicka händelsen till IoT Hub från din modul:
    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Köra apparna

Nu är det dags att köra apparna. Högerklicka på din lösning i Solution Explorer i Visual Studio och klicka sedan på **Ange startprojekt**. Välj **Multiple startup projects** (Starta flera projekt) och välj **Starta** som åtgärd för konsolappen. Starta sedan appen genom att trycka på F5. 

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Connecting your device][lnk-device-management] (Komma igång med enhetshantering)
* [Komma igång med IoT Edge][lnk-iot-edge]


<!-- Images. -->
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
