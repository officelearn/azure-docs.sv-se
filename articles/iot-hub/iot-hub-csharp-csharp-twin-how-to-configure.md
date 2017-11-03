---
title: "Använd Azure IoT Hub dubbla enhetsegenskaper (.NET/.NET) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub-enhet twins för att konfigurera enheter. Du kan använda Azure IoT-enhet SDK för .NET för att implementera en simulerad enhetsapp och tjänsten Azure IoT SDK för .NET att implementera ett service-appen som ändrar en konfiguration för enheter med hjälp av en enhet dubbla."
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.openlocfilehash: 0bb18f1b289dd01866842a5193437dabdb5fd20b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Använd önskade egenskaper för att konfigurera enheter
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

I slutet av den här kursen har du två .NET konsolappar:

* **SimulateDeviceConfiguration**, en simulerad enhetsapp som väntar på en uppdatering för önskad konfiguration och rapporterar status för en simulerad konfigurationsprocessen för uppdateringen.
* **SetDesiredConfigurationAndQuery**, en backend-app, vilket anger du önskad konfiguration på en enhet och frågar konfigurationsprocessen för uppdateringen.

> [!NOTE]
> Artikeln [Azure IoT SDK] [ lnk-hub-sdks] innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.
> 
> 

Den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.

Om du har följt den [Kom igång med enheten twins] [ lnk-twin-tutorial] kursen har du redan en IoT-hubb och en enhetsidentitet kallas **myDeviceId**. I så fall kan du hoppa till den [skapa den simulerade enhetsapp] [ lnk-how-to-configure-createapp] avsnitt.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Skapa den simulerade enhetsapp
I det här avsnittet skapar du en .NET-konsolapp som ansluter till din hubb som **myDeviceId**, väntar på en uppdatering för önskad konfiguration och rapporterar uppdateringar på uppdateringsprocessen simulerade konfiguration.

1. I Visual Studio skapar du ett nytt projekt i Visual C# klassiska skrivbordet med hjälp av den **konsolprogram** projektmall. Namnge projektet **SimulateDeviceConfiguration**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]

1. I Solution Explorer högerklickar du på den **SimulateDeviceConfiguration** projektet och klicka sedan på **hantera NuGet-paket...** .
1. I den **NuGet Package Manager** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhet SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![NuGet-Pakethanteraren fönstret-klientappen][img-clientnuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med den anslutningssträng för enheten som du antecknade i föregående avsnitt.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. Lägg till följande metod i klassen **Program**:
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    Den **klienten** objektet innehåller de metoder som du behöver för att interagera med enheten twins från enheten. Koden som visas ovan, initierar den **klienten** objekt och hämtar sedan enheten dubbla för **myDeviceId**.

1. Lägg till följande metod för att den **programmet** klass. Den här metoden anger de ursprungliga värdena i telemetri på den lokala enheten och uppdaterar sedan enheten dubbla.

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Lägg till följande metod för att den **programmet** klass. Det här är en motringning som identifierar en ändring i *önskade egenskaper* i dubbla för enheten.

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    Den här metoden uppdaterar rapporterade egenskaper på lokal dubbla enhetsobjektet med begäran om uppdatering konfiguration och anger statusen till **väntande**, uppdaterar enheten dubbla på tjänsten. När en uppdatering av enheten dubbla, Slutför ändrad konfiguration genom att anropa metoden `CompleteConfigChange` beskrivs i nästa punkt.

1. Lägg till följande metod för att den **programmet** klass. Den här metoden simulerar en återställning av enheten och sedan uppdateringar lokalt rapporterade egenskaper som anger status till **lyckade** och tar bort den **pendingConfig** element. Sedan uppdaterar den enheten dubbla på tjänsten. 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Slutligen lägger du till följande rader till den **Main** metoden:

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > Den här självstudiekursen simulerar inte någon funktion för samtidiga konfigurationsuppdateringar. Vissa processer för uppdatering av konfiguration kanske kan hantera förändringar av mål-konfiguration när uppdateringen körs, kanske vissa måste placeras i kö och vissa kan avvisa dem med ett feltillstånd. Se till att tänka på önskat beteende för din specifika konfigurationsprocessen och Lägg till lämpliga logiken innan du påbörjar konfigurationsändringen.
   > 
   > 
1. Skapa lösningen och sedan köra den enhet från Visual Studio genom att klicka på **F5**. Du bör se de meddelanden som anger att den simulerade enheten hämtar enheten dubbla, konfigurera telemetrin och väntar på att ändra önskade egenskap på utdata-konsolen. Fortsätt att köra appen.

## <a name="create-the-service-app"></a>Skapa service-appen
I det här avsnittet skapar du en .NET-konsolapp som uppdaterar det *önskade egenskaper* på enhet-dubbla som är associerade med **myDeviceId** med ett nytt telemetri configuration-objekt. Sedan frågar enheten-twins lagras i IoT-hubb och visar skillnaden mellan önskade och rapporterade konfigurationer av enheten.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **SetDesiredConfigurationAndQuery**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]
1. I Solution Explorer högerklickar du på den **SetDesiredConfigurationAndQuery** projektet och klicka sedan på **hantera NuGet-paket...** .
1. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.
   
    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Lägg till följande metod i klassen **Program**:
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    Den **registret** objektet innehåller de metoder som krävs för att interagera med enheten twins från tjänsten. Den här koden initierar den **registret** objekt, hämtar enheten dubbla för **myDeviceId**, och uppdaterar sedan dess egenskaper med ett nytt telemetri configuration-objekt.
    Efter att den frågar enheten-twins lagras i IoT-hubben var 10: e sekund och skriver ut önskad och rapporterade telemetri konfigurationer. Referera till den [IoT-hubb frågespråket] [ lnk-query] att lära dig hur du skapar omfattande rapporter på alla enheter.
   
   > [!IMPORTANT]
   > Det här programmet frågar IoT-hubb var 10: e sekund som illustration. Använda frågor för att generera användarinriktad rapporter över flera enheter och inte för att identifiera ändringar. Om din lösning kräver meddelanden i realtid av enhetshändelser, använder [dubbla meddelanden][lnk-twin-notifications].
   > 
   > 
1. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. I Solution Explorer, öppna den **ange Startprojekt...**  och kontrollera att den **åtgärd** för **SetDesiredConfigurationAndQuery** projektet är **starta**. Skapa lösningen.
1. Med **SimulateDeviceConfiguration** enheten app körs, köra service-appen från Visual Studio med hjälp av **F5**. Du bör se rapporterade konfigurationsändringen från **väntande** till **lyckade** med den nya aktivt skicka frekvensen av fem minuter i stället för 24 timmar.

 ![Enheten har konfigurerats][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Det finns en fördröjning på upp till en minut mellan enheten rapporten igen och frågeresultatet. Detta är att aktivera query-infrastruktur för att arbeta med mycket hög skala. Att hämta konsekvent vyer för en enskild enhet dubbel användning av **getDeviceTwin** metod i den **registret** klass.
   > 
   > 

## <a name="next-steps"></a>Nästa steg
I kursen får du ange en önskad konfiguration som *önskade egenskaper* från lösningen serverdel och skrev en enhetsapp för att identifiera den ändringen och simulera en flera steg uppdateringsprocess reporting dess status i rapporterade egenskaper.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb] [ lnk-iothub-getstarted] självstudiekursen
* schemalägga eller utföra åtgärder på stora mängder enheter finns i [schema och broadcast jobb] [ lnk-schedule-jobs] kursen.
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app), med den [metoder från direkt] [ lnk-methods-tutorial] kursen.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
