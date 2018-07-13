---
title: Kom igång med Azure IoT Hub-modulidentitet och -modultvilling (portal och .NET) | Microsoft Docs
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med portalen och .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: dobett
ms.openlocfilehash: 4b4b193751606883548e25e731dcece4ae72ba7b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666899"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Kom igång med IoT Hub-modulidentitet och modultvilling med portalen och .NET-enhet

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentiteten och enhetstvillingen gör att serverdelsprogrammet kan konfigurera en enhet och ger synlighet för enhetstillståndet tillhandahåller en modulidentitet och en modultvilling dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.

I den här kursen lär du dig 
1. att skapa en modulidentitet i portalen. 
2. hur du använder .NET-enhetens SDK för att uppdatera modultvillingen från din enhet.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Skapa en enhetsidentitet i portalen

Nu har du din IoT Hub. Öppna [portalen](https://portal.azure.com) och gå till IoT-hubben. Klicka på IoT-enheter och sedan på Lägg till för att skapa en enhetsidentitet. Ge den namnet **MyFirstDevice**. 

![Skapa enhetsidentitet][8]

När du har sparat kan du se att MyFirstDevice-identiteten har skapats i din lista över enhetsidentiteter.

![Enhets-id som skapats][11]

Klicka nu på raden. Du ser information om enheten.

![Enhetsinformation][10]

## <a name="create-a-module-identity-in-the-portal"></a>Skapa en modulidentitet i portalen

Inom en enhetsidentitet kan du skapa upp till 20 modulidentiteter. Klicka på knappen **Add Module Identity** (Lägg till modulidentitet) högst upp för att skapa din första modulidentitet som heter **myFirstModule**. 

![Enhetsinformation][9]

Spara och klicka på modulidentiteten du nyss skapade. Du kan se information om modulidentiteten. Spara anslutningssträngen – primärnyckeln. Den kommer att användas i nästa avsnitt där du konfigurerar modulen på enheten.

![Enhetsinformation][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

Du har skapat modulidentiteten i din IoT Hub. Försök kommunicera till molnet från den simulerade enheten. När en modulidentitet har skapats skapas en modultvilling implicit i IoT Hub. I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

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
    ```

    Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
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
        Client.CloseAsync().Wait();
    }
    ```

    Det är kodexemplet visar hur du hämtar modultvillingen och uppdaterar rapporterade egenskaper med AMQP-protokollet. I offentlig förhandsversion stöder vi endast AMQP för modultvillingåtgärder.

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna. Högerklicka på din lösning i Solution Explorer i Visual Studio och klicka sedan på **Ange startprojekt**. Välj **Multiple startup projects** (Starta flera projekt) och välj **Starta** som åtgärd för konsolappen. Och tryck på F5 för att starta båda apparna. 

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Kom igång med IoT Hub identitets- och modulen modultvilling med hjälp av .NET säkerhetskopierings- och .NET-enhet][lnk-csharp-csharp-getstarted]
* [Komma igång med IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
