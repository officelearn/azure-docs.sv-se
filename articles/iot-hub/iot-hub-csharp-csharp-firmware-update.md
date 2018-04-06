---
title: Enheten firmware-uppdatering med Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Hur du använder hantering av enheter på Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Du kan använda Azure IoT-enhet SDK för .NET för att implementera en simulerad enhetsapp och tjänsten Azure IoT SDK för .NET att implementera ett service-appen som utlöser firmware-uppdatering.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr;dobett
ms.openlocfilehash: 50bb2ada9cf848bdcfb4f958272ff918996bf411
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Använd enhetshantering för att starta en enhet på en firmware-uppdatering (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introduktion
I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudiekursen du sett hur du använder den [enheten dubbla] [ lnk-devtwin] och [direkt metoder] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här kursen visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering använder samma IoT-hubb primitiver.  Det här mönstret används i uppdatering av inbyggd hanteringsprogramvara för den [hallon Pi enheten implementering exempel][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en .NET-konsolapp som anropar den **firmwareUpdate** direkt metod i appen simulerade enheten via din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen inbyggd programvara, laddar ned avbildningen av inbyggd programvara och slutligen använder inbyggd avbildningen. Under varje steg i uppdateringen använder enheten rapporterade egenskaperna för att rapportera förlopp.

I slutet av den här kursen har du en enhet konsolapp .NET (C#) och en backend-konsolapp .NET (C#):

* **SimulatedDeviceFwUpdate**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare, får den **firmwareUpdate** direkt metod körs via en process som flera tillstånd för att simulera en firmware-uppdatering Exempel: väntar på avbildningen hämtas, hämta den nya avbildningen och slutligen avbildningen används.

* **TriggerFWUpdate**, som använder tjänsten SDK för att anropa via fjärranslutning på **firmwareUpdate** direkt metod på den simulerade enheten visar svaret och regelbundet (varje 500ms) visar den uppdaterade rapporterat Egenskaper.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Följ den [Kom igång med enhetshantering](iot-hub-csharp-csharp-device-management-get-started.md) artikel för att skapa din IoT-hubb och hämta anslutningssträngen för IoT-hubb.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en .NET-konsolapp (med C#) som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder direkta metoden för att initiera uppdateringen och använder enheten dubbla frågor för att hämta status för aktiva firmware-uppdatering med jämna mellanrum.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **TriggerFWUpdate**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createserviceapp]

2. I Solution Explorer högerklickar du på den **TriggerFWUpdate** projektet och klicka sedan på **hantera NuGet-paket**.
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt flera platshållarvärdena med IoT-hubb anslutningssträngen för hubben som du skapade i föregående avsnitt och ID för din enhet.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Lägg till följande metod i klassen **Program**. Den här metoden avsöker enheten två uppdaterade status var 500 millisekunder. Skriver den till konsolen endast när status faktiskt har ändrats. För det här exemplet för att förhindra att förbruka extra IoT-hubb meddelanden i din prenumeration avsökning slutar när enheten rapporterar statusen **applyComplete** eller ett fel.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Lägg till följande metod i klassen **Program**:

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Slutligen lägger du till följande rader till den **Main** metod. Detta skapar ett register manager för att läsa enhet dubbla med startar uppgiften avsökning på en arbetstråd och utlöser firmware-uppdatering.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Skapa lösningen.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet får du:

* Skapa en .NET-konsolapp som svarar på en direkt metod som anropas av molnet
* Simulera en firmware-uppdatering som utlöses av en serverdelstjänst via en direkt metod
* Använda rapporterade egenskaper för att aktivera enhetstvillingfrågor för att identifiera enheter och ta reda på när de senast slutfört en uppdatering av en inbyggd programvara

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **SimulateDeviceFWUpdate**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]
    
2. I Solution Explorer högerklickar du på den **SimulateDeviceFWUpdate** projektet och klicka sedan på **hantera NuGet-paket**.
3. I den **NuGet Package Manager** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhet SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![NuGet-Pakethanteraren fönstret-klientappen][img-clientnuget]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med den anslutningssträng för enheten som du antecknade i den **skapa en enhetsidentitet** avsnitt.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Lägg till följande metod för att rapportera status till molnet via enhet dubbla: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Lägg till följande metod för att simulera nedladdningen av avbildningen av inbyggd programvara:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Lägg till följande metod för att simulera avbildningen inbyggd programvara till enheten:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Lägg till följande metod för att simulera väntar på att hämta bild för inbyggd programvara. Uppdatera status för **väntar på** och avmarkera andra egenskaper för uppdatering av inbyggd programvara på dubbla. Dessa egenskaper rensas för att ta bort alla befintliga värden från tidigare firmware-uppdateringar. Detta är nödvändigt eftersom rapporterade egenskaper som skickas i en korrigering-åtgärd (en delta) och inte en PUT-åtgärd (en fullständig uppsättning egenskaper som ersätter alla tidigare värden). Normalt informeras enheter när en uppdatering är tillgänglig och när en princip som en administaratör har definierat börjar ladda ned och tillämpa uppdateringen. Logiken för att aktivera principen ska köras i den här funktionen. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Lägg till följande metod för att utföra nedladdningen. Så att statusen uppdateras **hämtar** via enhet-dubbla anropar du metoden simulera nedladdning och rapporterar statusen **downloadComplete** eller **downloadFailed** via dubbla beroende på resultatet av hämtningen. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Lägg till följande metod om du vill använda avbildningen. Så att statusen uppdateras **tillämpa** via enhet-dubbla anrop av simulera gäller bild metoden och uppdateringsstatus till **applyComplete** eller **applyFailed** via den dubbla beroende på resultatet av åtgärden Verkställ. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Lägg till följande metod för att sekvensera uppdateringsåtgärden inbyggd programvara från väntar på att ladda ned avbildningen till avbildningen till enheten:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Lägg till följande metod för att hantera den **updateFirmware** direkt metod från molnet. Den URL-Adressen till firmware-uppdatering från nyttolast meddelande och skickar det till den **doUpdate** aktiviteten som den startar på en annan arbetstråd tråd. Den returnerar omedelbart metodsvaret till molnet.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Den här metoden utlöser simulerade uppdateringen som ska köras som en **aktivitet** och sedan omedelbart svarar på metodanropet informera tjänsten firmware-uppdatering har startats. Uppdateringsstatus och slutförande skickas till tjänsten via rapporterade egenskaper för enhet dubbla. Vi svarar metodanropet när du startar uppdateringen snarare än efter slutförandet, eftersom:
> * En verklig uppdateringsprocessen är mycket troligt tar längre tid än tidsgränsen för anrop av metoden.
> * En verklig uppdateringsprocessen är mycket troligt att kräva en omstart, som kan starta om den här appen att göra den **MetodRequest** objekt som är tillgänglig. (Uppdatera rapporterade egenskaperna är dock möjligt även efter en omstart.) 

14. Slutligen lägger du till följande kod i **Main** metod för att öppna anslutningen till din IoT-hubb och initiera lyssnaren för metoden:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Skapa lösningen.       

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].


## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.
1. Kör .NET enhetsapp **SimulatedDeviceFWUpdate**.  Högerklicka på den **SimulatedDeviceFWUpdate** projektet, Välj **felsöka**, och välj sedan **Starta ny instans**. Den ska börja lyssna efter metodanrop från din IoT-hubb: 

2. När enheten är ansluten och väntar på att metoden anrop kör .NET **TriggerFWUpdate** app att anropa metoden firmware update i appen simulerade enheten. Högerklicka på den **TriggerFWUpdate** projektet, Välj **felsöka**, och välj sedan **Starta ny instans**. Du bör se Uppdatera aktivitetssekvensen som skrivits i den **SimulatedDeviceFWUpdate** konsolen och sekvensen rapporteras i rapporterade egenskaper för enheten i den **TriggerFWUpdate** konsolen. Observera att processen tar några sekunder att slutföra. 
   
    ![Tjänster och enheter appen kör][img-combinedrun]


## <a name="next-steps"></a>Nästa steg
I den här kursen används direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används egenskaperna rapporterade för att följa förloppet för firmware-uppdatering.

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/