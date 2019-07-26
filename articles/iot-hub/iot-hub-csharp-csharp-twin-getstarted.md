---
title: Kom igång med Azure IoT Hub Device Ungarns (.NET/.NET) | Microsoft Docs
description: Så här använder du Azure IoT Hub-enheten för att lägga till taggar och sedan använda en IoT Hub fråga. Du använder Azure IoT-enhetens SDK för .NET för att implementera den simulerade Device-appen och Azure IoT service SDK för .NET för att implementera en service app som lägger till taggarna och kör IoT Hubs frågan.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robinsh
ms.openlocfilehash: c1b8f60fd155cf9bce0b999da7459299b6f3c7aa
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404522"
---
# <a name="get-started-with-device-twins-netnet"></a>Kom igång med enhets dubbla (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien får du följande .NET-konsol program:

* **CreateDeviceIdentity**, en .net-app som skapar en enhets identitet och en associerad säkerhets nyckel för att ansluta din simulerade enhets app.

* **AddTagsAndQuery**, en .net-backend-app som lägger till taggar och frågar enheten på två sätt.

* **ReportConnectivity**är en .net-enhets app som simulerar en enhet som ansluter till din IoT Hub med enhets identiteten som skapades tidigare och som rapporterar sitt anslutnings tillstånd.

> [!NOTE]
> Artikeln [Azure IoT SDK](iot-hub-devguide-sdks.md) : er innehåller information om Azure IoT SDK: er som du kan använda för att bygga både enhets-och backend-appar.
> 

För att slutföra den här självstudien behöver du följande:

* Visual Studio.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänst appen

I det här avsnittet skapar du en .NET-konsol app ( C#med) som lägger till platsens metadata till den enhet som är den dubbla som är kopplad till **myDeviceId**. Den frågar sedan enheten efter varandra i IoT Hub och väljer de enheter som finns i USA och sedan de som rapporterade en mobil anslutning.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **AddTagsAndQuery**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. I Solution Explorer högerklickar du på projektet **AddTagsAndQuery** och klickar sedan på **Hantera NuGet-paket...** .

3. I fönstret **NuGet Package Manager** väljer du **Bläddra** och söker efter **Microsoft. Azure.** Devices. Välj **Installera** för att installera **Microsoft. Azure.** Devices-paketet och godkänn användnings villkoren. Den här proceduren hämtar, installerar och lägger till en referens till [Azure IoT service SDK NuGet-](https://www.nuget.org/packages/Microsoft.Azure.Devices/) paketet och dess beroenden.
   
    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med IoT Hub anslutnings strängen som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Lägg till följande metod i klassen **Program**:

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
   
    **RegistryManager** -klassen visar alla de metoder som krävs för att interagera med enheten tillsammans från tjänsten. Föregående kod initierar först **registryManager** -objektet och hämtar sedan enheten för **myDeviceId**och uppdaterar slutligen dess Taggar med önskad plats information.
   
    Efter uppdateringen körs två frågor: det första alternativet väljer att endast enhetarna är dubbla enheter som finns i **Redmond43** -anläggningen och den andra refinar frågan så att endast de enheter som också är anslutna via mobil nät verket återställs.
   
    Observera att föregående kod, **när objektet skapas** , anger ett maximalt antal returnerade dokument. **Frågespråket** innehåller en **HasMoreResults** Boolean-egenskap som du kan använda för att anropa **GetNextAsTwinAsync** -metoderna flera gånger för att hämta alla resultat. En metod som heter **GetNextAsJson** är tillgänglig för resultat som inte är enhets dubbla, till exempel resultat av agg regerings frågor.

7. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. I Solution Explorer öppnar du **Ange start projekt...** och kontrollerar att **åtgärden** för **AddTagsAndQuery** -projektet är **igång**. Skapa lösningen.

9. Kör det här programmet genom att högerklicka på projektet **AddTagsAndQuery** och välja **Felsök**, följt av **Starta ny instans**. Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för den fråga som begränsar resultatet till enheter som använder ett mobil nät verk.
   
    ![Fråga resultat i fönster](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

I nästa avsnitt skapar du en enhets app som rapporterar anslutnings informationen och ändrar resultatet för frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhets appen

I det här avsnittet skapar du en .NET-konsol app som ansluter till hubben som **myDeviceId**och uppdaterar sedan dess rapporterade egenskaper så att den innehåller den information som den är ansluten till med ett mobilt nätverk.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **ReportConnectivity**.
   
    ![Ny Visual C# Windows klassisk Device-app](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. I Solution Explorer högerklickar du på projektet **ReportConnectivity** och klickar sedan på **Hantera NuGet-paket...** .

3. I fönstret **NuGet Package Manager** väljer du **Bläddra** och söker efter **Microsoft. Azure. devices. client**. Välj **Installera** för att installera **Microsoft. Azure. devices. client** -paketet och godkänn användnings villkoren. Den här proceduren hämtar, installerar och lägger till en referens till [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.
   
    ![NuGet paket hanterarens fönster klient program](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med enhets anslutnings strängen som du antecknade i föregående avsnitt.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Lägg till följande metod i klassen **Program**:

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

    **Klient** objekt visar alla metoder som du behöver för att interagera med enheten tillsammans från enheten. Den kod som visas ovan initierar **klient** objekt och hämtar sedan enheten för **myDeviceId**.

7. Lägg till följande metod i klassen **Program**:

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

   Koden ovan uppdaterar **myDeviceId**-egenskapen rapporterad med anslutnings informationen.

8. Slutligen lägger du till följande rader till **Main**-metoden:

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

9. I Solution Explorer öppnar du **Ange start projekt...** och kontrollerar att **åtgärden** för **ReportConnectivity** -projektet är **igång**. Skapa lösningen.

10. Kör det här programmet genom att högerklicka på projektet **ReportConnectivity** och välja **Felsök**, följt av **Starta ny instans**. Du bör se hur den hämtar den dubbla informationen och sedan skicka anslutningen som en *rapporterad egenskap*.
   
    ![Kör Device app för att rapportera anslutningen](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Nu när enheten rapporterade anslutnings information, bör den visas i båda frågorna. Kör .NET **AddTagsAndQuery** -appen för att köra frågorna igen. Den här gången ska **myDeviceId** visas i båda frågeresultaten.
   
    ![Enhets anslutningen har rapporter ATS](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till metadata för enheten som taggar från en backend-app och skrev en simulerad Device-app för att rapportera enhetens anslutnings information på enheten. Du har också lärt dig hur du frågar den här informationen med hjälp av SQL-liknande IoT Hub frågespråk.

Använd följande resurser för att lära dig att:

* Skicka telemetri från enheter med själv studie kursen [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-dotnet.md)

* konfigurera enheter med enhetens dubbla egenskaper med hjälp av självstudierna [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md)

* kontrol lera enheter interaktivt (t. ex. genom att aktivera en fläkt från en användardefinierad app) med självstudierna [Använd direkt metoder](quickstart-control-device-dotnet.md) .
