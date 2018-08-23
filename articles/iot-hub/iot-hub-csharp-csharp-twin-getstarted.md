---
title: Kom igång med enhetstvillingar (.NET/.NET) för Azure IoT Hub | Microsoft Docs
description: Så här använder enhetstvillingar för Azure IoT Hub för att lägga till taggar och sedan använda en IoT Hub-fråga. Du kan använda Azure IoT-enhetens SDK för .NET för att implementera den simulerade enhetsappen och tjänsten Azure IoT SDK för .NET för att implementera en service-app som lägger till taggar och kör IoT Hub-frågan.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dobett
ms.openlocfilehash: c598fa5b8f44c6d04d3b4ca6b02b660a4cedaf04
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42060229"
---
# <a name="get-started-with-device-twins-netnet"></a>Kom igång med enhetstvillingar (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du de här .NET-konsolappar:

* **CreateDeviceIdentity**, en .NET-app som skapar en enhetsidentitet och en associerad Säkerhetsnyckel för att ansluta din simulerade enhetsapp.
* **AddTagsAndQuery**, en backend-.NET-app som lägger till taggar och frågar enhetstvillingar.
* **ReportConnectivity**, en .NET-enhetsappen som simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare och rapporterar tillståndet för anslutningen.

> [!NOTE]
> Artikeln [Azure IoT SDK: er] [ lnk-hub-sdks] innehåller information om Azure IoT SDK: er som du kan använda för att skapa appar för både enheten och backend-server.
> 
> 

Den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Skapa service-app
I det här avsnittet skapar du en .NET-konsolapp (med C#) som lägger till platsmetadata i enhetstvillingen som är associerade med **myDeviceId**. Därefter beordrar enhetstvillingar som lagras i IoT hub att välja de enheter som finns i USA och de som rapporterats av en mobil anslutning.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **AddTagsAndQuery**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]
1. I Solution Explorer högerklickar du på den **AddTagsAndQuery** projektet och klicka sedan på **hantera NuGet-paket...** .
1. I den **NuGet-Pakethanteraren** väljer **Bläddra** och Sök efter **microsoft.azure.devices**. Välj **installera** att installera den **Microsoft.Azure.Devices** paketet och Godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.
   
    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices;
1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Lägg till följande metod i klassen **Program**:
   
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
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    Den **RegistryManager** klassen visar alla metoder som krävs för att interagera med enhetstvillingar från tjänsten. Föregående kod först initierar den **registryManager** objekt och sedan hämtar enhetstvillingen för **myDeviceId**, och uppdaterar slutligen taggarna med informationen som önskad plats.
   
    När du har uppdaterat, körs två frågor: först väljer endast enhetstvillingar av enheter som finns i den **Redmond43** anläggning och andra förfinar fråga för att välja endast de enheter som även är anslutna via mobilnät.
   
    Observera att den tidigare koden när det skapas den **fråga** objekt, anger ett maximalt antal returnerade dokument. Den **fråga** objektet innehåller en **HasMoreResults** boolesk egenskap som du kan använda för att anropa den **GetNextAsTwinAsync** metoder flera gånger för att hämta alla resultat. En metod som kallas **GetNextAsJson** är tillgänglig för resultat som är inte enhetstvillingar, till exempel resultat mängdfrågor.
1. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. I Solution Explorer öppnar du den **ange Startprojekt...**  och se till att den **åtgärd** för **AddTagsAndQuery** projektet är **starta**. Skapa lösningen.
1. Kör det här programmet genom att högerklicka på den **AddTagsAndQuery** projekt och välja **felsöka**, följt av **Starta ny instans**. Du bör se en enhet i resultaten för att fråga ställa för alla enheter i **Redmond43** och ingenting alls under den fråga som begränsar resultaten till enheter som använder ett mobilnät.
   
    ![Frågeresultaten i fönstret][img-addtagapp]

I nästa avsnitt skapar du en app för enheter som rapporterar anslutningsinformation och ändras resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa en app för enheter
I det här avsnittet skapar du en .NET-konsolapp som ansluter till hubben som **myDeviceId**, och uppdaterar sedan dess rapporterade egenskaper för att innehålla de uppgifter som att den är ansluten med hjälp av ett mobilt nätverk.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Ge projektet namnet **ReportConnectivity**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]
    
1. I Solution Explorer högerklickar du på den **ReportConnectivity** projektet och klicka sedan på **hantera NuGet-paket...** .
1. I den **NuGet-Pakethanteraren** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhetens SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![Klientappen fönstret för NuGet-Pakethanteraren][img-clientnuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen som du antecknade i föregående avsnitt.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Lägg till följande metod i klassen **Program**:

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    Den **klienten** objektet innehåller alla metoder som du behöver för att interagera med enhetstvillingar från enheten. Koden som visas ovan, initierar den **klienten** objekt och hämtar sedan enhetstvillingen för **myDeviceId**.

1. Lägg till följande metod i klassen **Program**:
   
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

   Koden ovan uppdateringar **myDeviceId**är rapporterade egenskap med anslutningsinformation.

1. Slutligen lägger du till följande rader till **Main**-metoden:
   
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

1. I Solution Explorer öppnar du den **ange Startprojekt...**  och se till att den **åtgärd** för **ReportConnectivity** projektet är **starta**. Skapa lösningen.
1. Kör det här programmet genom att högerklicka på den **ReportConnectivity** projekt och välja **felsöka**, följt av **Starta ny instans**. Du bör se den hämta twin information och sedan skicka anslutning som en *rapporterade egenskap*.
   
    ![Kör app för enheter till rapporten anslutning][img-rundeviceapp]
    
    
1. Nu när enheten rapporteras dess anslutningsinformation som det visas i båda frågor. Kör .NET **AddTagsAndQuery** app för att köra frågor igen. Den här gången **myDeviceId** ska visas i båda frågeresultatet.
   
    ![Enhetsanslutning som har rapporterats][img-tagappsuccess]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagts till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp till rapporten anslutning enhetsinformation i enhetstvillingen. Du också lärt dig hur du frågar efter den här informationen med hjälp av SQL-liknande IoT Hub-frågespråk.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT Hub] [ lnk-iothub-getstarted] självstudien
* Konfigurera enheter som använder enhetstvillingens egenskaper med den [Använd önskade egenskaper för att konfigurera enheter] [ lnk-twin-how-to-configure] självstudien
* Kontrollera enheter interaktivt (till exempel aktivera en fans, från en användarstyrd app) med den [Använd direkta metoder] [ lnk-methods-tutorial] självstudien.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: quickstart-send-telemetry-dotnet.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-how-to-configure]: tutorial-device-twins.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

