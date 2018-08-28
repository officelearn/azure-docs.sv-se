---
title: Schemalägg jobb med Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod på flera enheter. Du kan använda Azure IoT-enhetens SDK för .NET för att implementera appar för simulerade enheter och en tjänstapp för att köra jobbet.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: eb7b4c4c6228818f78e002f4a06a000e9aa34a3a
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109645"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Schemalägg och Sänd jobb (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använda Azure IoT Hub för att schemalägga och spåra jobb som uppdaterar miljontals enheter. Använd jobb till:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direktmetoder

Ett jobb omsluter en av de här åtgärderna och spårar körning mot en uppsättning enheter som definieras av en enhet twin fråga. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod på 10 000 enheter som startar om enheterna. Du anger vilken uppsättning av enheter med en enhet twin fråga och schemalägga jobb att köra vid ett senare tillfälle. Spårar jobbförloppet som var och en av enheterna tar emot och kör den direkt metoden för omstart.

Mer information om var och en av de här funktionerna finns:

* Enhetstvillingen och egenskaper: [Kom igång med enhetstvillingar](iot-hub-csharp-csharp-twin-getstarted.md) och [självstudie: Så här använder du tvillingegenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub developer guide - direkta metoder](iot-hub-devguide-direct-methods.md) och [självstudie: Använd direkta metoder](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en app för enheter som implementerar en direkt metod som kallas **LockDoor** som kan anropas av backend-appen.

* Skapa en backend-app som skapar ett jobb för att anropa den **LockDoor** direkt metod på flera enheter. Ett annat jobb skickar uppdateringar för önskad egenskap till flera enheter.

I slutet av den här självstudien har du två .NET (C#)-konsolappar:

**SimulateDeviceMethods** som ansluter till din IoT hub och implementerar den **LockDoor** direkt metod.

**ScheduleJob** som använder jobb för att anropa den **LockDoor** direkt metod och uppdatera enhetstvillingens egenskaper på flera enheter.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2017.
* Ett aktivt Azure-konto. Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en .NET-konsolapp som svarar på en direkt metod som anropas av lösningens serverdel slutet.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **SimulateDeviceMethods**.
   
    ![Ny Visual C# Windows Classic enhetsapp](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. I Solution Explorer högerklickar du på den **SimulateDeviceMethods** projektet och klicka sedan på **hantera NuGet-paket...** .

3. I den **NuGet-Pakethanteraren** väljer **Bläddra** och Sök efter **Microsoft.Azure.Devices.Client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhetens SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.
   
    ![Klientappen fönstret för NuGet-Pakethanteraren](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen som du antecknade i föregående avsnitt:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Lägg till följande om du vill implementera direkt metod på enheten:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

7. Lägg till följande om du vill implementera device twins lyssnaren på enheten:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Slutligen lägger du till följande kod till den **Main** metod för att öppna anslutningen till din IoT-hubb och initiera lyssnaren för metoden:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

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
        
9. Spara ditt arbete och skapa din lösning.         

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel anslutning nya försök), vilket rekommenderas i MSDN-artikeln [hantering av tillfälliga fel](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Schemalägga jobb för att anropa en direkt metod och skicka uppdateringar för enhetstvilling

I det här avsnittet skapar du en .NET-konsolapp (med C#) som använder jobb för att anropa den **LockDoor** direkt metod och skicka önskad egenskap uppdateringar till flera enheter.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **ScheduleJob**.

    ![Nytt Visual C# Windows Classic Desktop-projekt](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. I Solution Explorer högerklickar du på den **ScheduleJob** projektet och klicka sedan på **hantera NuGet-paket...** .

3. I den **NuGet-Pakethanteraren** väljer **Bläddra**, Sök efter **Microsoft.Azure.Devices**väljer **installera** att installera den **Microsoft.Azure.Devices** paketet och Godkänn användningsvillkoren. Det här steget hämtar, installerar och lägger till en referens till den [Azure IoT service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-paketet och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Lägg till följande `using` instruktionen om den inte redan finns i standard-instruktioner.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Lägg till följande fält i klassen **Program**. Ersätt platshållarna med IoT Hub-anslutningssträngen för hubben som du skapade i föregående avsnitt och namnet på din enhet.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Lägg till följande metod i klassen **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. Lägg till följande metod i klassen **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. Lägg till en annan metod till den **programmet** klass:

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
    > Mer information om frågesyntaxen finns i [IoT Hub-frågespråk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    > 

10. Slutligen lägger du till följande rader till **Main**-metoden:

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

11. Spara ditt arbete och skapa din lösning. 

## <a name="run-the-apps"></a>Köra apparna

Nu är det dags att köra apparna.

1. Högerklicka på din lösning i Visual Studio Solution Explorer och klicka sedan på **skapa**. **Flera Startprojekt**. Se till att `SimulateDeviceMethods` är överst i listan följt av `ScheduleJob`. Ange både sina åtgärder **starta** och klicka på **OK**.

2. Kör projekt genom att klicka på **starta** eller gå till den **felsöka** menyn och klickar på **Starta felsökning**.

3. Du ser utdata från både enheten och backend-appar.

    ![Kör appar för att schemalägga jobb](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod att en enhet och varje uppdatering av enheten tvillingegenskaper.

Om du vill fortsätta att komma igång med IoT Hub och enhetshanteringsmönster som via luften firmware-uppdatering, läsa [självstudie: hur du gör en firmware-uppdatering](tutorial-firmware-update.md).

Läs om hur du distribuerar AI till gränsenheter med Azure IoT Edge i [komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).