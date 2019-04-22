---
title: Kom igång med Azure IoT Hub-modulidentitet och -modultvilling (portal och .NET) | Microsoft Docs
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med portalen och .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 75b86ea028a500b6b358c468a1d10a830db01b6a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59283757"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Kom igång med IoT Hub-modulidentitet och modultvilling med portalen och .NET-enhet

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentiteten och enhetstvillingen gör att serverdelsprogrammet kan konfigurera en enhet och ger synlighet för enhetstillståndet tillhandahåller en modulidentitet och en modultvilling dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.
>

I den här kursen lär du dig:

1. Så här att skapa en modul i portalen.

2. Hur du använder en .NET SDK enhetsuppdatering modultvilling från din enhet.

> [!NOTE]
> Information om Azure IoT SDK: erna som du kan använda för att skapa båda programmen för körning på enheter och lösningens backend-servrar finns i [Azure IoT SDK: er](iot-hub-devguide-sdks.md).
>

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Skapa en modulidentitet i portalen

Inom en enhetsidentitet kan du skapa upp till 20 modulidentiteter. Klicka på knappen **Add Module Identity** (Lägg till modulidentitet) högst upp för att skapa din första modulidentitet som heter **myFirstModule**.

  ![Enhetsinformation](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

Spara och klicka på modulidentiteten du nyss skapade. Du kan se information om modulidentiteten. Spara anslutningssträngen – primärnyckeln. Den kommer att användas i nästa avsnitt där du konfigurerar modulen på enheten.

  ![Enhetsinformation](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

Du har skapat modulidentiteten i din IoT Hub. Försök kommunicera till molnet från den simulerade enheten. När en modulidentitet har skapats skapas en modultvilling implicit i IoT Hub. I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

I Visual Studio lägger du till ett Visual C# Windows Classic Desktop-projekt i befintliga lösningen med hjälp av den **Konsolapp (.NET Framework)** projektmall. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare. Ge projektet namnet **UpdateModuleTwinReportedProperties**.

  ![Skapa ett Visual Studio-projekt](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installera den senaste Azure IoT Hub .NET-enheten-SDK

Modultvilling för identitets- och modulen finns i offentlig förhandsversion. Det är bara tillgängliga i SDK: er för IoT Hub förhandsversioner enheter. I Visual Studio öppnar du Verktyg > Nuget-pakethanteraren > Hantera NuGet-paket för lösningen. Sök efter Microsoft.Azure.Devices.Client. Kontrollera att du har markerat innehåller förhandsversioner markerar du kryssrutan. Välj den senaste versionen och installera. Nu har du åtkomst till alla modulfunktioner.

  ![Installera Azure IoT Hub .NET-tjänstens SDK V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>Hämta anslutningssträngen modul

Logga in på [Azure-portalen](https://portal.azure.com/). Gå till din IoT Hub och klicka på IoT-enheter. Hitta myFirstDevice, öppna den och du ser myFirstModule har skapats. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

  ![Information om Azure-portalmodulen](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Skapa UpdateModuleTwinReportedProperties-konsolapp

Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
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
        Console.WriteLine(JsonConvert.SerializeObject(twin));

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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Det är kodexemplet visar hur du hämtar modultvillingen och uppdaterar rapporterade egenskaper med AMQP-protokollet. I offentlig förhandsversion stöder vi endast AMQP för modultvillingåtgärder.

## <a name="run-the-apps"></a>Köra apparna

Nu är det dags att köra apparna. Högerklicka på din lösning i Solution Explorer i Visual Studio och klicka sedan på **Ange startprojekt**. Välj **Multiple startup projects** (Starta flera projekt) och välj **Starta** som åtgärd för konsolappen. Och tryck på F5 för att starta båda apparna.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Kom igång med IoT Hub identitets- och modulen modultvilling med hjälp av .NET säkerhetskopierings- och .NET-enhet](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)