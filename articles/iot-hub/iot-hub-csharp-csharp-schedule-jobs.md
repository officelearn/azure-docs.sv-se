---
title: Schemalägga jobb med Azure IoT Hub (.NET/.NET) | Microsoft-dokument
description: Så här schemalägger du ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du använder Azure IoT-enheten SDK för .NET för att implementera de simulerade enhetsapparna och en tjänstapp för att köra jobbet.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 4c71a108d1967027465d127db50737119af3e2c1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733376"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Schemalägga och sända jobb (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT Hub för att schemalägga och spåra jobb som uppdaterar miljontals enheter. Använd jobb för att:

* Uppdatera önskade egenskaper

* Uppdatera taggar

* Anropa direkta metoder

Ett jobb radbryts en av dessa åtgärder och spårar körningen mot en uppsättning enheter som definieras av en enhetstvillingfråga. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod på 10 000 enheter som startar om enheterna. Du anger uppsättningen enheter med en enhetstvillingfråga och schemalägger jobbet så att det körs vid en framtida tidpunkt. Jobbet spårar förloppet när var och en av enheterna får och kör metoden för omstart direkt.

Mer information om var och en av dessa funktioner finns i:

* Enhetstvilling och egenskaper: [Kom igång med enhetstvillingar](iot-hub-csharp-csharp-twin-getstarted.md) och [självstudiekurs: Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub developer guide - direkta metoder](iot-hub-devguide-direct-methods.md) och [handledning: Använd direkta metoder](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en enhetsapp som implementerar en direkt metod som kallas **LockDoor**, som kan anropas av backend-appen.

* Skapa en backend-app som skapar ett jobb för att anropa **LockDoor** direct-metoden på flera enheter. Ett annat jobb skickar önskade egenskapsuppdateringar till flera enheter.

I slutet av den här självstudien har du två .NET-konsolappar (C#):

* **SimuleraDeviceMethods**. Den här appen ansluter till din IoT-hubb och implementerar **LockDoor** direct-metoden.

* **SchemaJob**. Den här appen använder jobb för att anropa **LockDoor** direct-metoden och uppdatera enhetstvillingens önskade egenskaper på flera enheter.

## <a name="prerequisites"></a>Krav

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en .NET-konsolapp som svarar på en direkt metod som anropas av lösningens serverdel.

1. I Visual Studio väljer du **Skapa ett nytt projekt**och väljer sedan projektmallen Console App **(.NET Framework).** Välj **Nästa** för att fortsätta.

1. I **Konfigurera det nya projektet**namnger du projektet *SimulateDeviceMethods*och väljer sedan **Skapa**.

    ![Konfigurera projektet SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. Högerklicka på projektet **SimuleraDeviceMethods** i Solution Explorer och välj sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager**väljer du **Bläddra** och sök efter och välj **Microsoft.Azure.Devices.Client**. Välj **Installera**.

    ![NuGet Package Manager-fönster Klientapp](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-enheten SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med den enhetsanslutningssträng som du noterade i föregående avsnitt:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Lägg till följande kod för att implementera den direkta metoden på enheten:

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

1. Lägg till följande metod för att implementera enhetstvillinglyssnaren på enheten:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Lägg slutligen till följande kod i **huvudmetoden** för att öppna anslutningen till IoT-hubben och initiera metodavlysssnaren:

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

1. Spara ditt arbete och bygg din lösning.

> [!NOTE]
> För att hålla det enkelt implementerar den här självstudien inte några principer för återförsök. I produktionskoden bör du implementera principer för återförsök (t.ex. återförsök för anslutning), som föreslås i [tillfällig felhantering](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Schemalägga jobb för att anropa en direkt metod och skicka enhetstvillinguppdateringar

I det här avsnittet skapar du en .NET-konsolapp (med C#) som använder jobb för att anropa **LockDoor** direct-metoden och skicka önskade egenskapsuppdateringar till flera enheter.

1. Välj **Arkiv** > **nytt** > **projekt**i Visual Studio. I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera det nya projektet**namnger du projektet *ScheduleJob*. För **Lösning**väljer du **Lägg till i lösning**och väljer sedan **Skapa**.

    ![Namnge och konfigurera projektet ScheduleJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. Högerklicka på **Projektet ScheduleJob** i Solution Explorer och välj sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager**väljer du **Bläddra,** sök efter och väljer **Microsoft.Azure.Devices**och väljer sedan **Installera**.

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-tjänsten SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Lägg till `using` följande sats om det inte redan finns i standardsatserna.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarna med anslutningssträngen för IoT Hub som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string) och namnet på enheten.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Lägg till följande metod i klassen **Program:**

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

1. Lägg till följande metod i klassen **Program:**

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

1. Lägg till en annan metod i **klassen Program:**

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
    > Mer information om frågesyntax finns i [IoT Hub-frågespråk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
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

1. Spara ditt arbete och bygg din lösning.

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. Högerklicka på lösningen i Utforskaren för Visual Studio-lösning och välj sedan **Ange StartUp-projekt**.

1. Välj **Common Properties** > **Startup Project**och välj sedan **Flera startprojekt**.

1. Kontrollera `SimulateDeviceMethods` att den är överst `ScheduleJob`i listan följt av . Ställ in båda deras åtgärder på **Start** och välj **OK**.

1. Kör projekten genom att klicka på **Start** eller gå till **Felsökningsmenyn** och klicka på **Starta felsökning**.

   Du ser utdata från både enhets- och backend-appar.

    ![Kör apparna för att schemalägga jobb](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetstvillingens egenskaper.

* Om du vill fortsätta komma igång med IoT Hub och enhetshanteringsmönster som fjärröver uppdateringen av den inbyggda programvaran läser du [Självstudiekurs: Hur du gör en uppdatering av den inbyggda programvaran](tutorial-firmware-update.md).

* Mer information om hur du distribuerar AI till kantenheter med Azure IoT Edge finns i [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
