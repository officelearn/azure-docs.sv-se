---
title: Kom igång med Azure IoT Hub-enhet twins (.NET/.NET) | Microsoft Docs
description: Hur du använder Azure IoT Hub-enhet twins att lägga till taggar och sedan använda en IoT-hubb-fråga. Du kan använda Azure IoT-enhet SDK för .NET för att implementera appen simulerade enheten och tjänsten Azure IoT SDK för .NET att implementera en service-app som lägger till taggar och IoT-hubb-frågan körs.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 9419f15f2876a36769bdea84cc28537435148cac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634863"
---
# <a name="get-started-with-device-twins-netnet"></a>Kom igång med enheten twins (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här kursen har du apparna .NET-konsolen:

* **CreateDeviceIdentity**, en .NET-app som skapar en enhets-ID och associerade säkerhetsnyckeln ansluta din simulerade enhetsapp.
* **AddTagsAndQuery**, en .NET backend-app som lägger till taggar och frågar enheten twins.
* **ReportConnectivity**, en enhet .NET-app som simulerar en enhet som ansluter till din IoT-hubb med enhetens identitet skapade tidigare och rapporterar tillståndet anslutningen.

> [!NOTE]
> Artikeln [Azure IoT SDK] [ lnk-hub-sdks] innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.
> 
> 

Den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Skapa service-appen
I det här avsnittet skapar du en .NET-konsolapp (med C#) som lägger till platsen metadata i enheten-dubbla som är associerade med **myDeviceId**. Frågar sedan enheten-twins lagras i IoT-hubb som att markera de enheter som finns i USA och de som rapporterats av en mobil anslutning.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **AddTagsAndQuery**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]
1. I Solution Explorer högerklickar du på den **AddTagsAndQuery** projektet och klicka sedan på **hantera NuGet-paket...** .
1. I den **NuGet Package Manager** väljer **Bläddra** och Sök efter **microsoft.azure.devices**. Välj **installera** att installera den **Microsoft.Azure.Devices** paketet och Godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.
   
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
   
    Den **RegistryManager** klassen innehåller alla metoder som krävs för att interagera med enheten twins från tjänsten. Föregående kod först initierar den **registryManager** objekt och hämtar enheten dubbla för **myDeviceId**, och slutligen uppdateras taggarna med informationen som önskad plats.
   
    När du har uppdaterat, körs två frågor: först väljer enheten twins av enheter som finns i den **Redmond43** anläggningen och andra förfinar frågan för att markera de enheter som även är anslutna via mobilnät.
   
    Observera att föregående kod när det skapas den **frågan** objekt, anger ett maximalt antal returnerade dokument. Den **frågan** objektet innehåller en **HasMoreResults** boolesk egenskap som du kan använda för att anropa den **GetNextAsTwinAsync** metoder flera gånger för att hämta alla resultat. En metod som kallas **GetNextAsJson** är tillgänglig för resultat som är inte enheten twins, till exempel resultatet av aggregering frågor.
1. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. I Solution Explorer, öppna den **ange Startprojekt...**  och kontrollera att den **åtgärd** för **AddTagsAndQuery** projektet är **starta**. Skapa lösningen.
1. Kör det här programmet genom att högerklicka på den **AddTagsAndQuery** projekt och välja **felsöka**, följt av **Starta ny instans**. Du bör se en enhet i resultaten för frågan ställa för alla enheter i **Redmond43** och ingen för frågan som begränsar resultat till enheter som använder ett mobilnät.
   
    ![Resultatet av frågan i fönstret][img-addtagapp]

I nästa avsnitt skapar du en enhetsapp som rapporterar information om anslutningar och ändras resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsapp
I det här avsnittet skapar du en .NET-konsolapp som ansluter till din hubb som **myDeviceId**, och sedan uppdaterar egenskaperna rapporterade att innehålla den information som den är ansluten med hjälp av ett mobilnät.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **ReportConnectivity**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]
    
1. I Solution Explorer högerklickar du på den **ReportConnectivity** projektet och klicka sedan på **hantera NuGet-paket...** .
1. I den **NuGet Package Manager** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhet SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![NuGet-Pakethanteraren fönstret-klientappen][img-clientnuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med den anslutningssträng för enheten som du antecknade i föregående avsnitt.
   
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

    Den **klienten** objektet innehåller de metoder som du behöver för att interagera med enheten twins från enheten. Koden som visas ovan, initierar den **klienten** objekt och hämtar sedan enheten dubbla för **myDeviceId**.

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

   Koden ovan uppdateringar **myDeviceId**har rapporterats egenskapen med anslutningsinformation om.

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

1. I Solution Explorer, öppna den **ange Startprojekt...**  och kontrollera att den **åtgärd** för **ReportConnectivity** projektet är **starta**. Skapa lösningen.
1. Kör det här programmet genom att högerklicka på den **ReportConnectivity** projekt och välja **felsöka**, följt av **Starta ny instans**. Du bör se får dubbla information och sedan skickar anslutning som en *rapporterade egenskapen*.
   
    ![Kör enhetsapp till rapporten-anslutning][img-rundeviceapp]
    
    
1. Nu när enheten rapporteras dess anslutningsinformation, bör den visas i båda frågor. Kör .NET **AddTagsAndQuery** app att köras frågorna igen. Den här gången **myDeviceId** ska visas i båda frågeresultaten.
   
    ![Enhetsanslutning som har rapporterats][img-tagappsuccess]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du lagt till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp till enheten anslutningsinformation i dubbla för enheten. Du också fått lära dig hur man frågar den här informationen med hjälp av frågespråket i SQL-liknande IoT-hubb.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb] [ lnk-iothub-getstarted] självstudiekursen
* Konfigurera enheter med hjälp av enheten två egenskaper med den [Använd önskad egenskaper att konfigurera enheter] [ lnk-twin-how-to-configure] självstudiekursen
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app) med den [metoder från direkt] [ lnk-methods-tutorial] kursen.

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

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

