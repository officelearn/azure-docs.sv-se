---
title: Komma igång med Azure IoT Hub-enhetstvillingar (.NET/.NET) | Microsoft-dokument
description: Så här använder du Azure IoT Hub-enhetstvillingar för att lägga till taggar och sedan använda en IoT Hub-fråga. Du använder Azure IoT-enheten SDK för .NET för att implementera den simulerade enhetsappen och Azure IoT-tjänsten SDK för .NET för att implementera en tjänstapp som lägger till taggarna och kör IoT Hub-frågan.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 426430c075cfcb084cfe3238ebd83a19e909369b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110766"
---
# <a name="get-started-with-device-twins-net"></a>Komma igång med enhetstvillingar (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I den här självstudien skapar du dessa .NET-konsolappar:

* **CreateDeviceIdentity**. Den här appen skapar en enhetsidentitet och tillhörande säkerhetsnyckel för att ansluta din simulerade enhetsapp.

* **AddTagsAndQuery**. Den här backend-appen lägger till taggar och frågor enhetstvillingar.

* **ReportConnectivity**. Den här enhetsappen simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapats tidigare och rapporterar dess anslutningstillstånd.

> [!NOTE]
> Artikeln [Azure IoT SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK:er som du kan använda för att skapa både enhets- och backend-appar.
>

## <a name="prerequisites"></a>Krav

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänstappen

I det här avsnittet skapar du en .NET-konsolapp med C#, som lägger till platsmetadata till enhetstvillingen som är associerad med **myDeviceId**. Sedan frågar de enhetstvillingar som lagras i IoT-hubben och väljer de enheter som finns i USA och sedan de som rapporterade en mobilanslutning.

1. I Visual Studio väljer du **Skapa ett nytt projekt**. I **Skapa nytt projekt**väljer du **Konsolapp (.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du projektet **AddTagsAndQuery**.

    ![Konfigurera projektet AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. Högerklicka på projektet **AddTagsAndQuery** i Solution Explorer och välj sedan **Hantera NuGet-paket**.

1. Välj **Bläddra** och sök efter och välj **Microsoft.Azure.Devices**. Välj **Installera**.

    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-tjänsten SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{iot hub connection string}` med anslutningssträngen för IoT Hub som du kopierade i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Lägg till följande metod i klassen **Program:**

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    Klassen **RegistryManager** visar alla metoder som krävs för att interagera med enhetstvillingar från tjänsten. Den föregående koden initierar först **registretManager-objektet,** hämtar sedan enhetstvillingen för **myDeviceId**och uppdaterar slutligen taggarna med önskad platsinformation.

    Efter uppdatering körs två frågor: den första väljer bara enhetstvillingar för enheter som finns i **Redmond43-anläggningen** och den andra förfinar frågan för att välja endast de enheter som också är anslutna via mobilnätet.

    Den tidigare koden, när **frågeobjektet** skapas, anger ett maximalt antal returnerade dokument. **Frågeobjektet** innehåller en **HasMoreResults** boolesk egenskap som du kan använda för att anropa **GetNextAsTwinAsync-metoderna** flera gånger för att hämta alla resultat. En metod som kallas **GetNextAsJson** är tillgänglig för resultat som inte är enhetstvillingar, till exempel resultat av aggregeringsfrågor.

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Kör det här programmet genom att högerklicka på **projektet AddTagsAndQuery** och välja **Felsökning**, följt av **Starta ny instans**. Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för frågan som begränsar resultaten till enheter som använder ett mobilnät.

    ![Frågeresultat i fönstret](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

I nästa avsnitt skapar du en enhetsapp som rapporterar anslutningsinformationen och ändrar resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsappen

I det här avsnittet skapar du en .NET-konsolapp som ansluter till hubben som **myDeviceId**och uppdaterar sedan dess rapporterade egenskaper så att de innehåller den information som den är ansluten med ett mobilnät.

1. Välj **Arkiv** > **nytt** > **projekt**i Visual Studio. I **Skapa nytt projekt**väljer du **Konsolapp (.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera det nya projektet**namnger du projektet **ReportConnectivity**. För **Lösning**väljer du **Lägg till i lösning**och väljer sedan **Skapa**.

1. Högerklicka på **projektet ReportConnectivity** i Solution Explorer och välj sedan **Hantera NuGet-paket**.

1. Välj **Bläddra** och sök efter och välj **Microsoft.Azure.Devices.Client**. Välj **Installera**.

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-enheten SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{device connection string}` med enhetsanslutningssträngen som du noterade i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Lägg till följande metod i klassen **Program:**

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    **Klientobjektet** exponerar alla metoder som du behöver för att interagera med enhetstvillingar från enheten. Koden som visas ovan initierar **klientobjektet** och hämtar sedan enhetstvillingen för **myDeviceId**.

1. Lägg till följande metod i klassen **Program:**

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Koden ovan uppdaterar den rapporterade egenskapen **för myDeviceId** med anslutningsinformationen.

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Högerklicka på din lösning i Solution Explorer och välj **Ange StartUp-projekt**.

1. I **Common Properties** > **Startup Project**väljer du **Flera startprojekt**. För **ReportConnectivity**väljer du **Starta** som **åtgärd**. Spara ändringarna genom att välja **OK**.  

1. Kör den här appen genom att högerklicka på **projektet ReportConnectivity** och välja **Felsökning**och sedan **starta en ny instans**. Du bör se appen hämta den dubbla informationen och sedan skicka anslutning som en ***rapporterad egenskap***.

    ![Kör enhetsapp för att rapportera anslutning](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   När enheten har rapporterat sin anslutningsinformation bör den visas i båda frågorna.

1. Högerklicka på projektet **AddTagsAndQuery** och välj **Felsökning** > **Starta ny instans** för att köra frågorna igen. Den här gången bör **myDeviceId** visas i båda frågeresultaten.

    ![Enhetsanslutningen har rapporterats](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp för att rapportera enhetsanslutningsinformation i enhetstvillingen. Du har också lärt dig hur du frågar den här informationen med hjälp av frågespråket SQL-liknande IoT Hub.

Du kan läsa mer av följande resurser:

* Mer information om hur du skickar telemetri från enheter finns i [skicka telemetri från en enhet till en IoT-hubbdatorialkurs.](quickstart-send-telemetry-dotnet.md)

* Mer information om hur du konfigurerar enheter med hjälp av enhetstvillingens önskade egenskaper finns i de önskade egenskaperna Använd för att konfigurera självstudiekurs för [enheter.](tutorial-device-twins.md)

* Mer information om hur du styr enheter interaktivt, till exempel aktivera en fläkt från en användarstyrd app, finns i självstudien [Använd direktmetoder.](quickstart-control-device-dotnet.md)
