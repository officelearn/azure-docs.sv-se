---
title: Schemalägg jobb med Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Schemalägga ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du använder Azure IoT-enhetens SDK för .NET för att implementera de simulerade enhetens appar och en tjänst-app för att köra jobbet.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: 3594828ff3a79242e1cfd4663c415d8de502a329
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872772"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Schema-och sändnings jobb (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT Hub för att schemalägga och spåra jobb som uppdaterar miljon tals enheter. Använd jobb för att:

* Uppdatera önskade egenskaper
* Uppdatera Taggar
* Anropa direkt metoder

Ett jobb radbryter en av dessa åtgärder och spårar körningen mot en uppsättning enheter som definieras av en enhets dubbla frågor. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod på 10 000 enheter som startar om enheterna. Du kan ange en uppsättning enheter med en enhet med dubbla frågor och schemalägga jobbet så att det körs vid ett senare tillfälle. Jobbet spårar förloppet när var och en av enheterna tar emot och kör metoden starta om Direct.

Mer information om var och en av dessa funktioner finns i:

* Enhetens dubbla och egenskaper: [Kom igång med enhets dubbla](iot-hub-csharp-csharp-twin-getstarted.md) och [Självstudier: Använda enhetens dubbla egenskaper](tutorial-device-twins.md)

* Direkta metoder: [Guide för IoT Hub utvecklare – direkta metoder](iot-hub-devguide-direct-methods.md) och [Självstudier: Använd direkta metoder](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en app för enheten som implementerar en direkt metod med namnet **LockDoor** som kan anropas av backend-appen.

* Skapa en backend-app som skapar ett jobb för att anropa metoden **LockDoor** Direct på flera enheter. Ett annat jobb skickar önskade egenskaps uppdateringar till flera enheter.

I slutet av den här självstudien har du två .NETC#()-konsol program:

**SimulateDeviceMethods** som ansluter till din IoT-hubb och implementerar **LockDoor** Direct-metoden.

**ScheduleJob** som använder jobb för att anropa metoden **LockDoor** Direct och uppdatera enhetens dubbla önskade egenskaper på flera enheter.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Visual Studio. Den här självstudien använder Visual Studio 2017.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en .NET-konsol app som svarar på en direkt metod som anropas av lösningens Server del.

1. I Visual Studio lägger du till ett C# visuellt Windows klassiska Desktop-projekt till en ny eller befintlig lösning med hjälp av projekt mal len **konsol program** . Ge projektet namnet **SimulateDeviceMethods**.

    ![Ny Visual C# Windows klassisk Device-app](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)

2. I Solution Explorer högerklickar du på projektet **SimulateDeviceMethods** och väljer sedan **Hantera NuGet-paket...** .

3. I fönstret **NuGet Package Manager** väljer du **Bläddra** och söker efter **Microsoft. Azure. devices. client**. Välj **Installera** för att installera **Microsoft. Azure. devices. client** -paketet och godkänn användnings villkoren. Den här proceduren hämtar, installerar och lägger till en referens till [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.

    ![NuGet paket hanterarens fönster klient program](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med enhets anslutnings strängen som du antecknade i föregående avsnitt:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Lägg till följande för att implementera den direkta metoden på enheten:

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

7. Lägg till följande för att implementera enhetens dubbla lyssnare på enheten:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Slutligen lägger du till följande kod i **main** -metoden för att öppna anslutningen till IoT-hubben och initiera metoden Listener:

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

9. Spara ditt arbete och bygg din lösning.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel anslutnings försök), enligt vad som rekommenderas i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Schemalägg jobb för att anropa en direkt metod och skicka enhets dubbla uppdateringar

I det här avsnittet skapar du en .NET-konsol app ( C#med) som använder jobb för att anropa metoden **LockDoor** Direct och skicka önskade egenskaps uppdateringar till flera enheter.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **ScheduleJob**.

    ![Nytt Visual C# Windows Classic Desktop-projekt](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. I Solution Explorer högerklickar du på projektet **ScheduleJob** och väljer sedan **Hantera NuGet-paket...** .

3. Välj **Bläddra**i fönstret **NuGet Package Manager** , Sök efter **Microsoft. Azure.** Devices, Välj **Installera** för att installera **Microsoft. Azure.** Devices-paketet och godkänn användnings villkoren. I det här steget hämtas, installeras och läggs en referens till i [Azure IoT service SDK NuGet-](https://www.nuget.org/packages/Microsoft.Azure.Devices/) paketet och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Lägg till följande `using` instruktion om den inte redan finns i standard instruktionerna.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Lägg till följande fält i klassen **Program**. Ersätt plats hållarna med IoT Hub anslutnings strängen som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string) -anslutningssträngen och namnet på enheten.

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

9. Lägg till en annan metod i **program** klassen:

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
    > Mer information om frågesyntax finns i [IoT Hub frågespråk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
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

11. Spara ditt arbete och bygg din lösning.

## <a name="run-the-apps"></a>Köra apparna

Nu är det dags att köra apparna.

1. Högerklicka på din lösning i Visual Studio-Solution Explorer och välj **Ange start projekt**. Välj sedan **flera start projekt**. Kontrol lera att **SimulateDeviceMethods** är överst i listan följt av **ScheduleJob**. Ange båda åtgärderna för att **Starta** och välj **OK**.

2. Kör projekten genom att välja **Start** eller genom att gå till **fel söknings** menyn och välja **Starta fel sökning**.

3. Du ser utdata från både enhets-och backend-apparna.

    ![Kör apparna för att schemalägga jobb](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetens egenskaper.

Om du vill fortsätta komma igång med IoT Hub-och enhets hanterings mönster, till exempel fjärran [sluten av den inbyggda Air-programvaran, läser du Så här gör du en uppdatering](tutorial-firmware-update.md)av den inbyggda program varan.

Information om hur du distribuerar AI till Edge-enheter med Azure IoT Edge finns i [komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
