---
title: Kom igång med Azure IoT Hub-modulidentitet och -modultvilling (.NET) | Microsoft Docs
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med IoT SDK:er för .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 3b10ae0998acbb010e61ebb41c602c7d42bc14fa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263170"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Kom igång med IoT Hub identitets- och modulen modultvilling med hjälp av .NET-serverdel och .NET-enhet

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentiteten och enhetstvillingen gör att serverdelsprogrammet kan konfigurera en enhet och ger synlighet för enhetstillståndet tillhandahåller en modulidentitet och en modultvilling dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **CreateIdentities**, som skapar en enhetsidentitet, en modulidentitet och en associerad säkerhetsnyckel för att ansluta enheten och modulklienterna.

* **UpdateModuleTwinReportedProperties**, som skickar uppdaterade rapporterade egenskaper för modultvillingen till din IoT Hub.

> [!NOTE]
> Information om Azure IoT SDK: erna som du kan använda för att skapa båda programmen för körning på enheter och lösningens backend-servrar finns i [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2017.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

1. **Skapa ett Visual Studio-projekt:** I Visual Studio lägger du till ett visuellt objekt C# Windows Classic Desktop-projekt i den befintliga lösningen med hjälp av den **Konsolapp (.NET Framework)** projektmall. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare. Ge projektet namnet **UpdateModuleTwinReportedProperties**.

    ![Skapa ett Visual Studio-projekt](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Installera den senaste Azure IoT Hub .NET-enheten-SDK:** Modultvilling för identitets- och modulen finns i offentlig förhandsversion. Det är bara tillgängliga i SDK: er med IoT Hub, förhandsversioner enheter. I Visual Studio öppnar du Verktyg > Nuget-pakethanteraren > Hantera NuGet-paket för lösningen. Sök efter Microsoft.Azure.Devices.Client. Se till att du markerar kryssrutan för att inkludera förhandsversion. Välj den senaste versionen och installera. Nu har du åtkomst till alla modulfunktioner. 

    ![Installera Azure IoT Hub .NET-tjänstens SDK V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Hämta anslutningssträngen modulen** --om du loggar in nu [Azure-portalen](https://portal.azure.com/). Gå till din IoT Hub och klicka på IoT-enheter. Hitta myFirstDevice, öppna den och du ser myFirstModule har skapats. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

    ![Information om Azure-portalmodulen](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **Skapa UpdateModuleTwinReportedProperties-konsolapp**

    Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Lägg till följande metod **OnDesiredPropertyChanged** till klassen **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
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

* [Komma igång med enhetshantering](iot-hub-node-node-device-management-get-started.md)
* [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)