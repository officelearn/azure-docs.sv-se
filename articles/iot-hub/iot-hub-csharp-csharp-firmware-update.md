---
title: Uppdatering av inbyggd programvara för enheten med Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Hur du använder enhetshantering i Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Du kan använda Azure IoT-enhetens SDK för .NET för att implementera en simulerad enhetsapp och tjänsten Azure IoT SDK för .NET för att implementera en service-app som utlöser uppdateringen av inbyggd programvara.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: 1bf7a647ab2fdc175231133b0cfd8abdd51b6d43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723933"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Använda enhetshantering för att initiera en enhet på en firmware-uppdatering (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introduktion
I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudier, såg du hur du använder den [enhetstvillingen] [ lnk-devtwin] och [direkta metoder ] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här självstudien använder samma IoT Hub-primitiver och visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering.  Det här mönstret används i implementeringen på uppdatering av inbyggd programvara för den [Raspberry Pi enheten implementering exempel][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en .NET-konsolapp som anropar den **firmwareUpdate** direkt metod i den simulerade enhetsappen via din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen för inbyggd programvara, laddar ned avbildningen för inbyggd programvara och slutligen gäller avbildningen för inbyggd programvara. Enheten använder de rapporterade egenskaperna under varje steg av uppdateringen för att informera om förloppet.

I slutet av den här självstudien har du en enhet för .NET (C#) konsolapp och en backend-konsolapp .NET (C#):

* **SimulatedDeviceFwUpdate**, som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare, tar emot den **firmwareUpdate** direkt metod, körs via en process som har flera tillstånd att simulera en firmware-uppdatering Exempel: väntar för nedladdning av avbildningen, ladda ned den nya avbildningen och slutligen avbildningen används.

* **TriggerFWUpdate**, som använder SDK-tjänsten via en fjärranslutning anropa den **firmwareUpdate** direkta metoden på den simulerade enheten visar svaret och regelbundet (varje 500ms) visar det uppdaterade rapporterade Egenskaper.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Följ den [Kom igång med enhetshantering](iot-hub-csharp-csharp-device-management-get-started.md) artikeln om du vill skapa din IoT-hubb och få din IoT Hub-anslutningssträngen.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlösa en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en .NET-konsolapp (med C#) som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder en direkt metod för att initiera uppdateringen och använder enhetstvillingfrågor för att regelbundet hämta status för aktiva firmware-uppdatering.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **TriggerFWUpdate**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createserviceapp]

2. I Solution Explorer högerklickar du på den **TriggerFWUpdate** projektet och klicka sedan på **hantera NuGet-paket**.
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt flera platshållarvärdena med IoT Hub-anslutningssträngen för hubben som du skapade i föregående avsnitt och ID för enheten.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Lägg till följande metod i klassen **Program**. Den här metoden avsöker enhetstvillingen för uppdaterad status för varje 500 millisekunder. Skriver till konsolen förrän statusen faktiskt har ändrats. I det här exemplet att förhindra att förbruka extra IoT Hub-meddelanden i din prenumeration, avsökning stoppas när enheten rapporterar statusen **applyComplete** eller ett fel.  
   
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

8. Slutligen lägger du till följande rader till den **Main** metod. Detta skapar ett register manager att läsa enhetstvillingen med, börjar avsökningen uppgiften på en arbetstråd och utlöser sedan uppdateringen av inbyggd programvara.
   
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

* Skapa en .NET-konsolapp som svarar på en direkt metod som anropas via molnet
* Simulera en firmware-uppdatering som utlöses av en serverdelstjänst via en direkt metod
* Använda rapporterade egenskaper för att aktivera enhetstvillingfrågor för att identifiera enheter och ta reda på när de senast slutfört en uppdatering av en inbyggd programvara

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **SimulateDeviceFWUpdate**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]
    
2. I Solution Explorer högerklickar du på den **SimulateDeviceFWUpdate** projektet och klicka sedan på **hantera NuGet-paket**.
3. I den **NuGet-Pakethanteraren** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhetens SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![Klientappen fönstret för NuGet-Pakethanteraren][img-clientnuget]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen som du antecknade i den **skapa en enhetsidentitet** avsnittet.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Lägg till följande metod för att rapportera status tillbaka till molnet via enhetstvillingen: 

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

7. Lägg till följande metod för att simulera nedladdning av avbildningen för inbyggd programvara:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Lägg till följande metod för att simulera Tillämpa avbildningen för inbyggd programvara på enheten:
        
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
 
9.  Lägg till följande metod för att simulera väntar på att ladda ned avbildningen för inbyggd programvara. Uppdatera status till **väntar på** och avmarkera andra egenskaper för uppdatering av inbyggd programvara på läsningen. De här egenskaperna rensas för att ta bort alla befintliga värden från tidigare firmwareuppdateringar. Detta är nödvändigt eftersom rapporterade egenskaper som skickas som en korrigeringsåtgärd (en Deltasynkronisering) och inte en PUT-åtgärd (en fullständig uppsättning egenskaper som ersätter alla föregående värden). Normalt informeras enheter när en uppdatering är tillgänglig och när en princip som en administaratör har definierat börjar ladda ned och tillämpa uppdateringen. Logiken för att aktivera principen ska köras i den här funktionen. 
        
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

10. Lägg till följande metod för att utföra nedladdningen. Uppdateringsstatus till **hämtar** via enhetstvillingen, anropar du metoden simulera nedladdning och rapporterar statusen **downloadComplete** eller **downloadFailed** via läsningen beroende på resultatet av hämtningen. 
        
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

11. Lägg till följande metod om du vill använda avbildningen. Uppdateringsstatus till **tillämpa** anrop som gäller simulera via enhetstvillingen, bild-metoden och uppdateringsstatus till **applyComplete** eller **applyFailed** via den Twin beroende på resultatet av tillämpa åtgärden. 
        
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

12. Lägg till följande metod för att sekvensera uppdateringen av inbyggd programvara från väntar på att ladda ned avbildningen via avbildning tillämpas på enheten:
        
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

13. Lägg till följande metod för att hantera den **updateFirmware** direkt metod från molnet. Den URL: en till uppdatering av inbyggd programvara från meddelandet nyttolasten och skickar det till den **doUpdate** uppgiften som startas på en annan arbetstråd tråd. Det returnerar omedelbart metodsvaret till molnet.
        
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
> Den här metoden utlöser simulerade uppdateringen ska köras som en **uppgift** och sedan svarar omedelbart på metodanropet informerar tjänsten att uppdateringen av inbyggd programvara har startats. Uppdateringsstatus och slutförande skickas till tjänsten via rapporterade egenskaper i enhetstvillingen. Vi svarar metodanropet när du startar uppdateringen i stället när den är klart eftersom:
> * En verklig uppdateringsprocess är mycket troligt tar längre tid än tidsgränsen för anropet av metoden.
> * En verklig uppdateringsprocess är mycket troligt att kräva en omstart, vilket skulle startar den här appen att göra den **MethodRequest** objekt som är inte tillgänglig. (Uppdatera rapporterade egenskaper är dock möjligt även efter en omstart.) 

14. Slutligen lägger du till följande kod till den **Main** metod för att öppna anslutningen till din IoT-hubb och initiera lyssnaren för metoden:
   
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
1. Kör .NET-enhetsappen **SimulatedDeviceFWUpdate**.  Högerklicka på den **SimulatedDeviceFWUpdate** projektet, Välj **felsöka**, och välj sedan **Starta ny instans**. Den ska börja lyssna efter metodanrop från IoT Hub: 

2. När enheten är ansluten och väntar på metodanropen, kör .NET **TriggerFWUpdate** app att anropa metoden firmware update i den simulerade enhetsappen. Högerklicka på den **TriggerFWUpdate** projektet, Välj **felsöka**, och välj sedan **Starta ny instans**. Du bör se Uppdatera sekvens som skrivits i den **SimulatedDeviceFWUpdate** konsolen och även sekvensen som rapporterats via rapporterade egenskaper för enheten i den **TriggerFWUpdate** konsolen. Observera att processen tar några sekunder att slutföra. 
   
    ![App för tjänster och enheter som kör][img-combinedrun]


## <a name="next-steps"></a>Nästa steg
I den här självstudien används en direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används de rapporterade egenskaperna för att följa förloppet för uppdatering av inbyggd programvara.

Läs hur du utökar din IoT-lösning och schema anropar på flera enheter i den [schema och sändningsjobb] [ lnk-tutorial-jobs] självstudien.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-csharp-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
