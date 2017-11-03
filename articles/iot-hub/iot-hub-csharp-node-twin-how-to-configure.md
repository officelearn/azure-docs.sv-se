---
title: "Använd Azure IoT Hub dubbla enhetsegenskaper (.NET/nod) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub-enhet twins för att konfigurera enheter. Du kan använda Azure IoT-enhet SDK för Node.js för att implementera en simulerad enhetsapp och tjänsten Azure IoT SDK för .NET att implementera ett service-appen som ändrar en konfiguration för enheter med hjälp av en enhet dubbla."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Använd önskade egenskaper för att konfigurera enheter
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

I slutet av den här kursen har du två konsolappar:

* **SimulateDeviceConfiguration.js**, en simulerad enhetsapp som väntar på en uppdatering för önskad konfiguration och rapporterar status för en simulerad konfigurationsprocessen för uppdateringen.
* **SetDesiredConfigurationAndQuery**, en .NET backend-app, vilket anger du önskad konfiguration på en enhet och frågar konfigurationsprocessen för uppdateringen.

> [!NOTE]
> Artikeln [Azure IoT SDK] [ lnk-hub-sdks] innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.
> 
> 

Den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.

Om du har följt den [Kom igång med enheten twins] [ lnk-twin-tutorial] kursen har du redan en IoT-hubb och en enhetsidentitet kallas **myDeviceId**. I så fall kan du hoppa till den [skapa den simulerade enhetsapp] [ lnk-how-to-configure-createapp] avsnitt.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Skapa den simulerade enhetsapp
I det här avsnittet skapar du en Node.js-konsolprogram som ansluter till din hubb som **myDeviceId**, väntar på en uppdatering för önskad konfiguration och rapporterar uppdateringar på uppdateringsprocessen simulerade konfiguration.

1. Skapa en ny tom mapp som kallas **simulatedeviceconfiguration**. I den **simulatedeviceconfiguration** mapp, skapa en ny package.json-fil med följande kommando vid en kommandotolk. Acceptera alla standardvärden.
   
    ```
    npm init
    ```
1. Vid en kommandotolk i den **simulatedeviceconfiguration** mapp, kör följande kommando för att installera den **azure iot-enhet** och **azure-iot-enhet – mqtt** paket:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Med hjälp av en textredigerare, skapa en ny **SimulateDeviceConfiguration.js** filen i den **simulatedeviceconfiguration** mapp.
1. Lägg till följande kod i den **SimulateDeviceConfiguration.js** filen och Ersätt den **{enheten anslutningssträngen}** platshållaren med den anslutningssträng för enheten som du kopierade när du skapade den **myDeviceId** enhets-ID:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    Den **klienten** objektet innehåller de metoder som krävs för att interagera med enheten twins från enheten. Den här koden initierar den **klienten** objekt, hämtar enheten dubbla för **myDeviceId**, och sedan kopplar en hanterare för uppdateringen på *önskade egenskaper*. Hanteraren verifierar att det är en verklig configuration ändringsbegäran genom att jämföra configIds sedan anropar en metod som startar konfigurationsändringen.
   
    Observera att för enkelhetens skull, den här koden använder en hårdkodad standard för den inledande konfigurationen. En verklig app skulle förmodligen att läsa in konfigurationen från en lokal lagring.
   
   > [!IMPORTANT]
   > Önskad egenskapen Ändringshändelser släpps alltid en gång vid enhetsanslutning. Se till att kontrollera att det finns en verklig ändring i egenskaperna innan du utför några åtgärder.
   > 
   > 
1. Lägg till följande metoder innan den `client.open()` anrop:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    Den **initConfigChange** metoden uppdaterar rapporterade egenskaper på lokal dubbla enhetsobjektet med begäran om uppdatering konfiguration och anger statusen till **väntande**, uppdaterar enheten dubbla på tjänsten. När en uppdatering av enheten dubbla, den simulerar en tidskrävande process som slutar i körningen av **completeConfigChange**. Den här metoden uppdaterar egenskaperna lokala rapporterade statusen angetts som **lyckade** och ta bort den **pendingConfig** objekt. Sedan uppdaterar den enheten dubbla på tjänsten.
   
    Observera att, för att spara bandbredd, rapporteras egenskaper uppdateras genom att ange egenskaperna som ska ändras (med namnet **korrigering** i koden ovan), i stället för att ersätta hela dokumentet.
   
   > [!NOTE]
   > Den här självstudiekursen simulerar inte någon funktion för samtidiga konfigurationsuppdateringar. Vissa processer för uppdatering av konfiguration kanske kan hantera förändringar av mål-konfiguration när uppdateringen körs, kanske vissa måste placeras i kö och vissa kan avvisa dem med ett feltillstånd. Se till att tänka på önskat beteende för din specifika konfigurationsprocessen och Lägg till lämpliga logiken innan du påbörjar konfigurationsändringen.
   > 
   > 
1. Kör appen enhet:
   
        node SimulateDeviceConfiguration.js
   
    Du bör se meddelandet `retrieved device twin`. Fortsätt att köra appen.

## <a name="create-the-service-app"></a>Skapa service-appen
I det här avsnittet skapar du en .NET-konsolapp som uppdaterar det *önskade egenskaper* på enhet-dubbla som är associerade med **myDeviceId** med ett nytt telemetri configuration-objekt. Sedan frågar enheten-twins lagras i IoT-hubb och visar skillnaden mellan önskade och rapporterade konfigurationer av enheten.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **SetDesiredConfigurationAndQuery**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]
1. I Solution Explorer högerklickar du på den **SetDesiredConfigurationAndQuery** projektet och klicka sedan på **hantera NuGet-paket...** .
1. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.
   
    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Lägg till följande metod i klassen **Program**:
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    Den **registret** objektet innehåller de metoder som krävs för att interagera med enheten twins från tjänsten. Den här koden initierar den **registret** objekt, hämtar enheten dubbla för **myDeviceId**, och uppdaterar sedan dess egenskaper med ett nytt telemetri configuration-objekt.
    Efter att den frågar enheten-twins lagras i IoT-hubben var 10: e sekund och skriver ut önskad och rapporterade telemetri konfigurationer. Referera till den [IoT-hubb frågespråket] [ lnk-query] att lära dig hur du skapar omfattande rapporter på alla enheter.
   
   > [!IMPORTANT]
   > Det här programmet frågar IoT-hubb var 10: e sekund som illustration. Använda frågor för att generera användarinriktad rapporter över flera enheter och inte för att identifiera ändringar. Om din lösning kräver meddelanden i realtid av enhetshändelser, använder [dubbla meddelanden][lnk-twin-notifications].
   > 
   > 
1. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. I Solution Explorer, öppna den **ange Startprojekt...**  och kontrollera att den **åtgärd** för **SetDesiredConfigurationAndQuery** projektet är **starta**. Skapa lösningen.
1. Med **SimulateDeviceConfiguration.js** körs, kör .NET-program från Visual Studio med hjälp av **F5** och du bör se rapporterade konfigurationsändringen från **lyckade** till **väntande** till **lyckade** igen med den nya aktivt frekvens som skickar fem minuter i stället för 24 timmar.

 ![Enheten har konfigurerats][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Det finns en fördröjning på upp till en minut mellan enheten rapporten igen och frågeresultatet. Detta är att aktivera query-infrastruktur för att arbeta med mycket hög skala. Att hämta konsekvent vyer för en enskild enhet dubbel användning av **getDeviceTwin** metod i den **registret** klass.
   > 
   > 

## <a name="next-steps"></a>Nästa steg
I kursen får du ange en önskad konfiguration som *önskade egenskaper* från lösningen serverdel och skrev en enhetsapp för att identifiera den ändringen och simulera en flera steg uppdateringsprocess reporting dess status i rapporterade egenskaper.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb] [ lnk-iothub-getstarted] självstudiekursen
* schemalägga eller utföra åtgärder på stora mängder enheter finns i [schema och broadcast jobb] [ lnk-schedule-jobs] kursen.
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app), med den [metoder från direkt] [ lnk-methods-tutorial] kursen.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app
