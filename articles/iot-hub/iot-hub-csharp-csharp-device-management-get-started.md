---
title: Komma igång med Azure IoT Hub-enhetshantering (.NET/.NET) | Microsoft-dokument
description: Så här använder du Azure IoT Hub-enhetshantering för att initiera en omstart av en fjärrenhet. Du använder Azure IoT-enheten SDK för .NET för att implementera en simulerad enhetsapp som innehåller en direkt metod och Azure IoT-tjänsten SDK för .NET för att implementera en tjänstapp som anropar den direkta metoden.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 3cc74faa39b21b1ab275149db4f85de8f55fd07e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733480"
---
# <a name="get-started-with-device-management-net"></a>Komma igång med enhetshantering (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure-portalen för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.

* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en .NET-konsolapp som anropar metoden starta om direkt i den simulerade enhetsappen via IoT-hubben.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **SimuleraManagedDevice**. Den här appen ansluter till din IoT-hubb med enhetsidentiteten som skapats tidigare, tar emot en direktmetod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **TriggerReboot**. Den här appen anropar en direkt metod i den simulerade enhetsappen, visar svaret och visar de uppdaterade rapporterade egenskaperna.

## <a name="prerequisites"></a>Krav

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärromstart på enheten med en direkt metod

I det här avsnittet skapar du en .NET-konsolapp med C#, som initierar en fjärromstart på en enhet med en direkt metod. Appen använder enhetstvillingfrågor för att identifiera den senaste omstartstiden för den enheten.

1. I Visual Studio väljer du **Skapa ett nytt projekt**.

1. Leta reda på och välj projektmallen **Console App (.NET Framework)** i **Skapa ett nytt projekt**och välj sedan **Nästa**.

1. I **Konfigurera det nya projektet**namnger du projektet *TriggerReboot*och väljer .NET Framework version 4.5.1 eller senare. Välj **Skapa**.

    ![Nytt Visual C# Windows Classic Desktop-projekt](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. Högerklicka på **Projektet TriggerReboot** i **Solution Explorer**och välj sedan **Hantera NuGet-paket**.

1. Välj **Bläddra**och sök sedan efter och välj **Microsoft.Azure.Devices**. Välj **Installera** för att installera **paketet Microsoft.Azure.Devices.**

    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-tjänsten SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{iot hub connection string}` platshållarvärdet med anslutningssträngen IoT Hub som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Lägg till följande metod i klassen **Program**.  Den här koden hämtar enhetstvillingen för omstartsenheten och matar ut de rapporterade egenskaperna.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Lägg till följande metod i klassen **Program**.  Den här koden initierar omstarten på enheten med en direkt metod.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Slutligen lägger du till följande rader till **Main**-metoden:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Välj **Bygg** > **bygglösning**.

> [!NOTE]
> Den här självstudien utför bara en enskild fråga för enhetens rapporterade egenskaper. I produktionskod rekommenderar vi avsökning för att identifiera ändringar i de rapporterade egenskaperna.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en .NET-konsolapp som svarar på en direkt metod som anropas av molnet.

* Utlösa en simulerad omstart av enheten.

* Använd de rapporterade egenskaperna för att aktivera enhetstvillingfrågor för att identifiera enheter och när de senast startades om.

Så här skapar du den simulerade enhetsappen:

1. I Visual Studio väljer du Arkiv**nytt** > projekt i den TriggerReboot-lösning som du redan har skapat i **Visual** > **Studio.** Leta reda på och välj projektmallen **Console App (.NET Framework)** i **Skapa ett nytt projekt**och välj sedan **Nästa**.

1. I **Konfigurera det nya projektet**namnger du projektet *SimulateManagedDevice*och välj Lägg till i **lösning**för **Lösning**. Välj **Skapa**.

    ![Namnge och lägga till projektet i lösningen](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. Högerklicka på det nya **SimulateManagedDevice-projektet** i Solution Explorer och välj sedan **Hantera NuGet-paket**.

1. Välj **Bläddra**och sök sedan efter och välj **Microsoft.Azure.Devices.Client**. Välj **Installera**.

    ![NuGet Package Manager-fönster Klientapp](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-enheten SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{device connection string}` platshållarvärdet med den enhetsanslutningssträng som du noterade tidigare i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Lägg till följande för att implementera den direkta metoden på enheten:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
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

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Lägg slutligen till följande kod i **huvudmetoden** för att öppna anslutningen till IoT-hubben och initiera metodavlysssnaren:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

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
   ```

1. Högerklicka på lösningen i Solution Explorer och välj sedan **Ange StartUp-projekt**.

1. För **Common Properties** > **Startup Project**väljer du Ett enda **startprojekt**och väljer sedan **projektet SimulateManagedDevice.** Spara ändringarna genom att välja **OK**.

1. Välj **Bygg** > **bygglösning**.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för återförsök (till exempel en exponentiell backoff), som föreslås i [Transient felhantering](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. Om du vill köra .NET-enhetsappen **SimulateManagedDevice**högerklickar du på projektet **SimuleraeManagedDevice,** väljer **Felsökning**och väljer sedan **Starta ny instans**. Appen ska börja lyssna efter metodanrop från din IoT-hubb.

1. När enheten är ansluten och väntar på metodanrop högerklickar du på **Projektet TriggerReboot,** väljer **Felsökning**och väljer sedan **Starta ny instans**.

   Du bör se "Starta om!" skriven i **SimulatedManagedDevice-konsolen** och de rapporterade egenskaperna för enheten, som inkluderar den senaste omstartstiden, skriven i **TriggerReboot-konsolen.**

    ![Körning av tjänst- och enhetsapp](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
