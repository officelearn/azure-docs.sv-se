---
title: Kom igång med Azure IoT Hub-enhetshantering (.NET/.NET) | Microsoft Docs
description: Hur du använder Azure IoT Hub-enhetshantering för att initiera en fjärransluten enhet omstart. Du kan använda Azure IoT-enhetens SDK för .NET för att implementera en simulerad enhetsapp som innehåller en direkt metod och tjänsten Azure IoT SDK för .NET för att implementera en service-app som anropar direktmetoden.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: c1cee32e3ee60ce229308055cca7f0e9832ddc49
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38578908"
---
# <a name="get-started-with-device-management-netnet"></a>Kom igång med enhetshantering (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använda Azure-portalen för att skapa en IoT Hub och skapa en enhetsidentitet i IoT hub.
* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.
* Skapa en .NET-konsolapp som anropar metoden omstart direkt i den simulerade enhetsappen via din IoT-hubb.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **SimulateManagedDevice**, som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.
* **TriggerReboot**, som anropar en direkt metod i den simulerade enhetsappen visar svaret och visar det uppdaterade rapporterade egenskaper.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärromstart på enheten med en direkt metod
I det här avsnittet skapar du en .NET-konsolapp (med C#) som initierar en fjärromstart på en enhet med en direkt metod. Appen använder enhetstvillingfrågor för att identifiera den senaste Omstartstid för enheten.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den nya lösningen med hjälp av projektmallen **Konsolapp (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **TriggerReboot**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createserviceapp]

2. I Solution Explorer högerklickar du på den **TriggerReboot** projektet och klicka sedan på **hantera NuGet-paket**.
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen för hubben som du skapade i avsnittet ”Skapa en IoT-hubb”. 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Lägg till följande metod i klassen **Program**.  Den här koden hämtar enhetstvillingen för håller enheten och returnerar de rapporterade egenskaperna.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Lägg till följande metod i klassen **Program**.  Den här koden initierar omstarten på enheten med en direkt metod.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Skapa lösningen.

> [!NOTE]
> Den här självstudien utför endast en enskild fråga för enhetens rapporterade egenskaper. I produktionskoden rekommenderar vi avsökningen för att identifiera ändringar i de rapporterade egenskaperna.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet kommer du att

* Skapa en .NET-konsolapp som svarar på en direkt metod som anropas via molnet
* Utlösa en simulerad enhet-omstart
* Använd de rapporterade egenskaperna för att aktivera enhetstvillingfrågor att identifiera enheter och när de senast startades om

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **SimulateManagedDevice**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]
    
2. I Solution Explorer högerklickar du på den **SimulateManagedDevice** projektet och klicka sedan på **hantera NuGet-paket...** .
3. I den **NuGet-Pakethanteraren** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhetens SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![Klientappen fönstret för NuGet-Pakethanteraren][img-clientnuget]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen som du antecknade i föregående avsnitt.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

6. Lägg till följande om du vill implementera direkt metod på enheten:

        static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
        {
            // In a production device, you would trigger a reboot scheduled to start after this method returns
            // For this sample, we simulate the reboot by writing to the console and updating the reported properties 
            try
            {
                Console.WriteLine("Rebooting!");

                // Update device twin with reboot time. 
                TwinCollection reportedProperties, reboot, lastReboot;
                lastReboot = new TwinCollection();
                reboot = new TwinCollection();
                reportedProperties = new TwinCollection();
                lastReboot["lastReboot"] = DateTime.Now;
                reboot["reboot"] = lastReboot;
                reportedProperties["iothubDM"] = reboot;
                Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }

            string result = "'Reboot started.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

7. Slutligen lägger du till följande kod till den **Main** metod för att öppna anslutningen till din IoT-hubb och initiera lyssnaren för metoden:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "reboot" method
            Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
            Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "reboot" handler
            Client.SetMethodHandlerAsync("reboot", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
8. Högerklicka på din lösning i Solution Explorer i Visual Studio och klicka sedan på **Ange startprojekt...**. Välj **enda Startprojekt**, och välj sedan den **SimulateManagedDevice** projekt i den nedrullningsbara menyn. Skapa lösningen.       

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].


## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.
1. Kör .NET-enhetsappen **SimulateManagedDevice**.  Högerklicka på den **SimulateManagedDevice** projektet, Välj **felsöka**, och välj sedan **Starta ny instans**. Den ska börja lyssna efter metodanrop från IoT Hub: 

2. Nu när enheten är ansluten och väntar på metodanropen, kör .NET **TriggerReboot** app att anropa metoden omstart i den simulerade enhetsappen. Högerklicka på den **TriggerReboot** projektet, Välj **felsöka**, och välj sedan **Starta ny instans**. Du bör se ”omstartsläge”! skrivna i den **SimulatedManagedDevice** konsolen och de rapporterade egenskaperna av enheten, bland annat senaste omstart skriven den **TriggerReboot** konsolen.
   
    ![App för tjänster och enheter som kör][img-combinedrun]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
