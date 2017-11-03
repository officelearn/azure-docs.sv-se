---
title: Enheten firmware-uppdatering med Azure IoT Hub (.NET/nod) | Microsoft Docs
description: "Hur du använder hantering av enheter på Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Du kan använda Azure IoT-enhet SDK för Node.js för att implementera en simulerad enhetsapp och tjänsten Azure IoT SDK för .NET att implementera ett service-appen som utlöser firmware-uppdatering."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: 157f112869f0042e330e6b281367632ca015e890
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Använd enhetshantering för att starta en enhet på en firmware-uppdatering (.NET/nod)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudiekursen du sett hur du använder den [enheten dubbla] [ lnk-devtwin] och [direkt metoder] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här kursen visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering använder samma IoT-hubb primitiver.  Det här mönstret används i uppdatering av inbyggd hanteringsprogramvara för den [hallon Pi enheten implementering exempel][lnk-rpi-implementation].

I den här självstudiekursen lär du dig att:

* Skapa en .NET-konsolapp som anropar metoden firmwareUpdate direkt i appen simulerade enheten via din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen inbyggd programvara, laddar ned avbildningen av inbyggd programvara och slutligen använder inbyggd avbildningen. Under varje steg i uppdateringen använder enheten rapporterade egenskaperna för att rapportera förlopp.

I slutet av den här kursen har du en enhet för Node.js konsolapp och en serverdel för .NET (C#) konsolapp:

**dmpatterns_fwupdate_service.js**, som anropar en direkt metod i appen simulerade enheten visar svaret och regelbundet (varje 500ms) visar den uppdaterade rapporterade egenskaper.

**TriggerFWUpdate**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare, tar emot en firmwareUpdate direkt metod, körs via en process som flera tillstånd för att simulera en firmware-uppdatering inklusive: väntar på avbildningen hämtas, hämta den nya avbildningen och slutligen avbildningen används.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Node.js-version 4.0.x eller senare <br/>  [Förbered din utvecklingsmiljö] [ lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen på Windows- eller Linux.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Följ den [Kom igång med enhetshantering](iot-hub-csharp-node-device-management-get-started.md) artikel för att skapa din IoT-hubb och hämta anslutningssträngen för IoT-hubb.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en .NET-konsolapp (med C#) som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder direkta metoden för att initiera uppdateringen och använder enheten dubbla frågor för att hämta status för aktiva firmware-uppdatering med jämna mellanrum.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **TriggerFWUpdate**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]

1. I Solution Explorer högerklickar du på den **TriggerFWUpdate** projektet och klicka sedan på **hantera NuGet-paket...** .
1. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Lägg till följande fält i klassen **Program**. Ersätt flera platshållarvärdena med IoT-hubb anslutningssträngen för hubben som du skapade i föregående avsnitt och Id för din enhet.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Lägg till följande metod i klassen **Program**:
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Lägg till följande metod i klassen **Program**:

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

1. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. I Solution Explorer, öppna den **ange Startprojekt...**  och kontrollera att den **åtgärd** för **TriggerFWUpdate** projektet är **starta**.

1. Skapa lösningen.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken i den **manageddevice** mapp, kör följande kommando för att börja lyssna efter omstart direkta metoden.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. I Visual Studio högerklickar du på den **TriggerFWUpdate** projektet, Välj **felsöka** och **Starta ny instans**.

3. Svaret från enheten till den direkta metoden i konsolen visas.

    ![Inbyggd programvara har uppdaterats][img-fwupdate]

## <a name="next-steps"></a>Nästa steg
I den här kursen används direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används egenskaperna rapporterade för att följa förloppet för firmware-uppdatering.

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/