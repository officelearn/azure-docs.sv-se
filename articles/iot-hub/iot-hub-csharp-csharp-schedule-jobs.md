---
title: Schemalägga med Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod på flera enheter. Du kan använda Azure IoT-enhet SDK för .NET för att implementera de simulerade enheten apparna och service-appen för att köra jobbet.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 012/16/2018
ms.author: v-masebo;dobett
ms.openlocfilehash: 76c8d3739b2af3c010cd80585c93c097fc9eb466
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Schemat och sändning jobb (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT-hubb för att schemalägga och spåra jobb som uppdaterar miljoner enheter. Använd jobb till:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkt metoder

Ett jobb radbryts i någon av följande åtgärder och spårar körning mot en uppsättning enheter som definieras av en enhet dubbla fråga. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod i 10 000 enheter som startar om enheterna. Anger vilken uppsättning av enheter med en enhet dubbla fråga och schemalägga jobbet ska köras vid ett senare tillfälle. Spårar jobbförloppet som var och en av enheterna som tar emot och köra den direkta metoden omstart.

Mer information om var och en av dessa funktioner finns:

* Enheten dubbla och egenskaper: [Kom igång med enheten twins] [ lnk-get-started-twin] och [Självstudier: hur du använder identiska enhetsegenskaper][lnk-twin-props]
* Dirigera metoder: [IoT-hubb Utvecklarhandbok - direkt metoder] [ lnk-dev-methods] och [kursen: direkta metoder][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en enhetsapp som implementerar en direkt metod som kallas **LockDoor** som kan anropas av backend-app.
* Skapa en backend-app som skapar ett jobb för att anropa den **LockDoor** direkt metod på flera enheter. Ett annat jobb skickar önskade egenskapen uppdateringar till flera enheter.

I slutet av den här kursen har du två konsolappar för .NET (C#):

**SimulateDeviceMethods** som ansluter till din IoT-hubb och implementerar den **LockDoor** direkt metod.

**ScheduleJob** som använder jobb för att anropa den **LockDoor** direkt metod och uppdatera dubbla önskad enhetsegenskaper på flera enheter.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet skapar du en .NET-konsolapp som svarar på en direkt metod som anropas av lösningen tillbaka slutet.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **SimulateDeviceMethods**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]
    
1. I Solution Explorer högerklickar du på den **SimulateDeviceMethods** projektet och klicka sedan på **hantera NuGet-paket...** .

1. I den **NuGet Package Manager** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhet SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![NuGet-Pakethanteraren fönstret-klientappen][img-clientnuget]

1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med den anslutningssträng för enheten som du antecknade i föregående avsnitt:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;

1. Add the following to implement the direct method on the device:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }

1. Add the following to implement the device twins listener on the device:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Slutligen lägger du till följande kod i **Main** metod för att öppna anslutningen till din IoT-hubb och initiera lyssnaren för metoden:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. Spara ditt arbete och skapa din lösning.         

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskod, bör du implementera försök principer (till exempel anslutning försök), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel][lnk-transient-faults].
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Schemaläggning för direkta metoden och skicka dubbla uppdateringar

I det här avsnittet skapar du en .NET-konsolapp (med C#) som använder jobb för att anropa den **LockDoor** direkt metod och skicka önskade egenskapen uppdateringar till flera enheter.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **ScheduleJob**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]

1. I Solution Explorer högerklickar du på den **ScheduleJob** projektet och klicka sedan på **hantera NuGet-paket...** .

1. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Det här steget hämtar, installerar och lägger till en referens till den [Azure IoT service SDK] [ lnk-nuget-service-sdk] NuGet-paketet och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]

1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Lägg till följande `using` instruktionen om den inte redan finns i standard-instruktioner.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarna med IoT-hubb anslutningssträngen för hubben som du skapade i föregående avsnitt och namnet på din enhet.

    ```csharp
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Lägg till följande metod i klassen **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Lägg till följande metod i klassen **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Lägg till en annan metod för att den **programmet** klass:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Mer information om frågesyntaxen finns [IoT-hubb frågespråket][lnk-query].
    > 

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Spara ditt arbete och skapa din lösning. 


## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. Högerklicka på lösningen i Visual Studio Solution Explorer och klicka sedan på **skapa**. **Flera Startprojekt**. Kontrollera att `SimulateDeviceMethods` är överst i listan följt av `ScheduleJob`. Ange både sina åtgärder till **starta** och på **OK**.

1. Kör projekt genom att klicka på **starta** eller gå till den **felsöka** -menyn och klicka på **Start Debugging**.

1. Du kan se utdata från både enheten och backend-appar.

    ![Köra appar för att schemalägga jobb][img-schedulejobs]


## <a name="next-steps"></a>Nästa steg

I den här kursen används ett jobb för att schemalägga en direkt metod för att en enhet och enheten-dubbla egenskaper att uppdatera.

Om du vill fortsätta komma igång med IoT-hubb och device management mönster, till exempel remote via luften firmware-uppdatering, läsa [Självstudier: hur du gör en firmware-uppdatering][lnk-fwupdate].

Läs om hur du distribuerar AI till enheter med Azure IoT kant i [komma igång med IoT kant][lnk-iot-edge].

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
