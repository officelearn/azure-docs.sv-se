---
title: Kom igång med Azure IoT Hub enhetshantering (.NET/nod) | Microsoft Docs
description: Hur du använder hantering av Azure IoT Hub-enheter för att initiera en omstart av fjärranslutna enheter. Du kan använda Azure IoT-enhet SDK för Node.js för att implementera en simulerad enhetsapp som innehåller en direkt metod och Azure IoT SDK för .NET att implementera ett service-appen som anropar metoden direkt.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/05/2017
ms.author: juanpere
ms.openlocfilehash: df41d8b88cf630183afc314dafdc1e898f4e02d9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632245"
---
# <a name="get-started-with-device-management-netnode"></a>Komma igång med hantering av enheter (.NET/nod)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använda Azure portal för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.
* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.
* Skapa en .NET-konsolapp som anropar metoden omstart direkt i appen simulerade enheten via din IoT-hubb.

I slutet av den här kursen har du en enhet för Node.js konsolapp och en serverdel för .NET (C#) konsolapp:

**dmpatterns_getstarted_device.js**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tid för den senaste omstarten.

**TriggerReboot**, som anropar en direkt metod i appen simulerade enheten visar svaret och visar den uppdaterade rapporterade egenskaper.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Node.js-version 4.0.x eller senare <br/>  [Förbered din utvecklingsmiljö] [ lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen på Windows- eller Linux.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten omstart på enheten med en direkt metod
I det här avsnittet skapar du en .NET-konsolapp (med C#) som initierar en fjärransluten omstart på en enhet med en direkt metod. Appen använder enheten dubbla frågor för att identifiera senast omstart för enheten.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den nya lösningen med hjälp av projektmallen **Konsolapp (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Namnge projektet **TriggerReboot**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]

2. I Solution Explorer högerklickar du på den **TriggerReboot** projektet och klicka sedan på **hantera NuGet-paket**.
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med IoT-hubb anslutningssträngen för hubben som du skapade i avsnittet ”Skapa en IoT-hubb”. 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "myDeviceId";
        
6. Lägg till följande metod i klassen **Program**.  Den här koden hämtar enheten dubbla för om enheten och matar ut rapporterade egenskaper.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Lägg till följande metod i klassen **Program**.  Den här koden initierar omstart på enheten med en direkt metod.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Skapa lösningen.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet kommer du att

* Skapa en Node.js-konsolapp som svarar på en direkt metod som anropas via molnet
* Utlös en simulerad enhet-omstart
* Använda egenskaperna rapporterade för att aktivera enheten dubbla frågor för att identifiera enheter och när de senaste startas om

1. Skapa en ny tom mapp som kallas **manageddevice**.  I mappen **manageddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken.  Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **manageddevice** mapp, kör följande kommando för att installera den **azure iot-enhet** enheten SDK-paketet och **azure-iot-enhet – mqtt** paketet:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Med hjälp av en textredigerare, skapa en ny **dmpatterns_getstarted_device.js** filen i den **manageddevice** mapp.
4. Lägg till följande 'krävs, instruktioner i början av den **dmpatterns_getstarted_device.js** fil:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**.  Ersätt anslutningssträngen med anslutningssträngen enhet.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Lägg till följande funktion för att implementera metoden direkt på enheten
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Lägg till följande kod för att öppna anslutningen till din IoT-hubb och starta lyssnaren direkta metoden:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Spara och Stäng den **dmpatterns_getstarted_device.js** fil.
   
> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].


## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken i den **manageddevice** mapp, kör följande kommando för att börja lyssna efter omstart direkta metoden.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Kör C#-konsolapp **TriggerReboot**. Högerklicka på den **TriggerReboot** projektet, Välj **felsöka**, och välj sedan **Starta ny instans**.

3. Svaret från enheten till den direkta metoden i konsolen visas.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
