---
title: Kom igång med Azure IoT Hub Device Management (.NET/.NET) | Microsoft Docs
description: Använda Azure-IoT Hub enhets hantering för att starta en fjärrstyrd enhet. Du använder Azure IoT-enhetens SDK för .NET för att implementera en simulerad enhets app som innehåller en direkt metod och Azure IoT service SDK för .NET för att implementera en tjänst-app som anropar den direkta metoden.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 40db247dba1d55b5121f95a4d69ca853f3d7ee56
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404575"
---
# <a name="get-started-with-device-management-netnet"></a>Kom igång med enhets hantering (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure Portal för att skapa en IoT Hub och skapa en enhets identitet i din IoT-hubb.

* Skapa en simulerad enhets app som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en .NET-konsol app som anropar metoden starta om direkt i den simulerade appen via din IoT Hub.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **SimulateManagedDevice**, som ansluter till din IoT-hubb med enhets identiteten som skapades tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **TriggerReboot**, som anropar en direkt metod i den simulerade Device-appen, visar svaret och visar uppdaterade egenskaper som rapporteras.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärran sluten omstart på enheten med en direkt metod

I det här avsnittet skapar du en .NET-konsol app ( C#med) som initierar en fjärromstart på en enhet med hjälp av en direkt metod. Appen använder enhets dubbla frågor för att identifiera den senaste omstarts tiden för enheten.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den nya lösningen med hjälp av projektmallen **Konsolapp (.NET Framework)** . Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **TriggerReboot**.

    ![Nytt Visual C# Windows Classic Desktop-projekt](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. I Solution Explorer högerklickar du på projektet **TriggerReboot** och klickar sedan på **Hantera NuGet-paket**.

3. Välj **Bläddra**i fönstret **NuGet Package Manager** , Sök efter **Microsoft. Azure.** Devices, Välj **Installera** för att installera **Microsoft. Azure.** Devices-paketet och godkänn användnings villkoren. Den här proceduren hämtar, installerar och lägger till en referens till [Azure IoT service SDK NuGet-](https://www.nuget.org/packages/Microsoft.Azure.Devices/) paketet och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med IoT Hub anslutnings strängen som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen.
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Lägg till följande metod i klassen **Program**.  Den här koden hämtar enheten så att den omstartar enheten och matar ut de rapporterade egenskaperna.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Lägg till följande metod i klassen **Program**.  Den här koden startar omstarten på enheten med en direkt metod.

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

7. Slutligen lägger du till följande rader till **Main**-metoden:
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Skapa lösningen.

> [!NOTE]
> Den här självstudien utför bara en fråga för enhetens rapporterade egenskaper. I produktions kod rekommenderar vi att du avsöker för att identifiera ändringar i de rapporterade egenskaperna.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet gör du följande:

* Skapa en .NET-konsol app som svarar på en direkt metod som anropas av molnet.

* Utlös en simulerad omstart av enheten.

* Använd de rapporterade egenskaperna för att aktivera enhets dubbla frågor för att identifiera enheter och när de startades om senast.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **SimulateManagedDevice**.
   
    ![Ny Visual C# Windows klassisk Device-app](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. I Solution Explorer högerklickar du på projektet **SimulateManagedDevice** och klickar sedan på **Hantera NuGet-paket...** .

3. I fönstret **NuGet Package Manager** väljer du **Bläddra** och söker efter **Microsoft. Azure. devices. client**. Välj **Installera** för att installera **Microsoft. Azure. devices. client** -paketet och godkänn användnings villkoren. Den här proceduren hämtar, installerar och lägger till en referens till [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.
   
    ![NuGet paket hanterarens fönster klient program](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med enhets anslutnings strängen som du antecknade i föregående avsnitt.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Lägg till följande för att implementera den direkta metoden på enheten:

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

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. Slutligen lägger du till följande kod i **main** -metoden för att öppna anslutningen till IoT-hubben och initiera metoden Listener:

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
        
8. Högerklicka på din lösning i Solution Explorer i Visual Studio och klicka sedan på **Ange startprojekt...** . Välj **enstaka start projekt**och välj sedan **SimulateManagedDevice** -projektet i list menyn. Skapa lösningen.       

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel en exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Köra apparna

Nu är det dags att köra apparna.

1. Köra .NET-enhets appens **SimulateManagedDevice**. Högerklicka på projektet **SimulateManagedDevice** , Välj **Felsök**och välj sedan **Starta ny instans**. Den ska börja lyssna efter metod anrop från IoT Hub. 

2. Nu när enheten är ansluten och väntar på metod anrop kör du .NET **TriggerReboot** -appen för att anropa metoden reboot i den simulerade Device-appen. Det gör du genom att högerklicka på projektet **TriggerReboot** , välja **Felsök**och sedan **Starta ny instans**. Du bör se "starta om!" Skrivet i **SimulatedManagedDevice** -konsolen och de rapporterade egenskaperna för enheten, som innehåller den senaste omstarts tiden, skrivet i **TriggerReboot** -konsolen.
   
    ![Körning av tjänst-och enhets app](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
