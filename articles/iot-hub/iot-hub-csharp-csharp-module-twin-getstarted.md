---
title: Komma igång med Azure IoT Hub-modulidentitet & modultvilling (.NET)
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med IoT SDK:er för .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e728d0ef8f52927687d56bd1d4c64f03c53ef401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73947668"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Komma igång med IoT Hub-modulidentitet och modultvilling (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentitet och enhetstvilling gör det möjligt för backend-programmet att konfigurera en enhet och ge synlighet på enhetens villkor, tillhandahåller en modulidentitet och modultvilling dessa funktioner för enskilda komponenter på en enhet. På kompatibla enheter med flera komponenter, till exempel operativsystembaserade enheter eller enheter för inbyggd programvara, möjliggör modulidentiteter och modultvillingar isolerad konfiguration och villkor för varje komponent.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **CreateId entiteter**. Den här appen skapar en enhetsidentitet, en modulidentitet och tillhörande säkerhetsnyckel för att ansluta enheten och modulklienter.

* **UpdateModuleTwinReportedProperties**. Den här appen skickar uppdaterade modultvillingrapporterade egenskaper till din IoT-hubb.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.

## <a name="prerequisites"></a>Krav

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

## <a name="create-a-hub"></a>Skapa en hubb

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

Innan du börjar hämtar du modulanslutningssträngen. Logga in på [Azure-portalen](https://portal.azure.com/). Navigera till din hubb och välj **IoT-enheter**. Hitta **myFirstDevice**. Välj **myFirstDevice** för att öppna den och välj sedan **myFirstModule** för att öppna den. Kopiera **anslutningssträngen (primärnyckeln)** i **Modulidentitetsinformation**när det behövs i följande procedur.

   ![Information om Azure-portalmodulen](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. Lägg till ett nytt projekt i din lösning i Visual Studio genom att välja **Arkiv** > **nytt** > **projekt**. I Skapa ett nytt projekt väljer du **Console App (.NET Framework)** och väljer **Nästa**.

1. Ge projektet namnet *UpdateModuleTwinReportedProperties*. För **Lösning**väljer du **Lägg till i lösning**. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare.

    ![Skapa ett Visual Studio-projekt](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Välj **Skapa** för att skapa projektet.

1. Öppna **Tools** > **NuGet Package Manager** > **Hantera NuGet-paket för lösning**i Visual Studio. Välj fliken **Bläddra**.

1. Sök efter och välj **Microsoft.Azure.Devices.Client**och välj sedan **Installera**.

    ![Installera azure IoT Hub .NET-tjänst SDK-aktuell version](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Lägg till följande metod **OnDesiredPropertyChanged** till klassen **Program**:

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

1. Lägg till följande **Main** rader i huvudmetoden:

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

1. Du kan också lägga till dessa satser i **huvudmetoden** för att skicka en händelse till IoT Hub från modulen. Placera dessa rader `try catch` under blocket.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Kör apparna

Du kan nu köra apparna.

1. Högerklicka på lösningen i Solution Explorer i **Visual Studio**och välj sedan Konfigurera **StartUp-projekt**.

1. Under **Vanliga egenskaper**väljer du **Startprojekt.**

1. Välj **Flera startprojekt**och välj sedan **Start** som åtgärd för apparna och **OK** för att acceptera ändringarna.

1. Tryck på **F5** för att starta apparna.

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Connecting your device](iot-hub-node-node-device-management-get-started.md) (Komma igång med enhetshantering)

* [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
