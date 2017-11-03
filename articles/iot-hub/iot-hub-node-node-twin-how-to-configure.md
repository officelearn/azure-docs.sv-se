---
title: "Använd Azure IoT Hub dubbla enhetsegenskaper (nod) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub-enhet twins för att konfigurera enheter. Du kan använda Azure IoT-SDK för Node.js för att implementera en simulerad enhetsapp och en service-app som ändrar en konfiguration för enheter med hjälp av en enhet dubbla."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: 6ff6f1c331d5a77e7ac0a47af6806f5d90fb0fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices-node"></a>Använd önskade egenskaper för att konfigurera enheter (nod)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

I slutet av den här kursen har du två Node.js-konsolappar:

* **SimulateDeviceConfiguration.js**, en simulerad enhetsapp som väntar på en uppdatering för önskad konfiguration och rapporterar status för en simulerad konfigurationsprocessen för uppdateringen.
* **SetDesiredConfigurationAndQuery.js**, en Node.js backend-app, vilket anger du önskad konfiguration på en enhet och frågar konfigurationsprocessen för uppdateringen.

> [!NOTE]
> Artikeln [Azure IoT SDK] [ lnk-hub-sdks] innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.
> 
> 

Den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Om du har följt den [Kom igång med enheten twins] [ lnk-twin-tutorial] kursen har du redan en IoT-hubb och en enhetsidentitet kallas **myDeviceId**, och du kan hoppa till [Skapa den simulerade enhetsapp] [ lnk-how-to-configure-createapp] avsnitt.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>Skapa den simulerade enhetsapp
I det här avsnittet skapar du en Node.js-konsolprogram som ansluter till din hubb som **myDeviceId**, väntar på en uppdatering för önskad konfiguration och rapporterar uppdateringar på uppdateringsprocessen simulerade konfiguration.

1. Skapa en ny tom mapp som kallas **simulatedeviceconfiguration**. I den **simulatedeviceconfiguration** mapp, skapa en ny package.json-fil med följande kommando vid en kommandotolk. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **simulatedeviceconfiguration** mapp, kör följande kommando för att installera den **azure iot-enhet**, och **azure-iot-enhet-mqtt** paket:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Med hjälp av en textredigerare, skapa en ny **SimulateDeviceConfiguration.js** filen i den **simulatedeviceconfiguration** mapp.
4. Lägg till följande kod i den **SimulateDeviceConfiguration.js** filen och Ersätt den **{enheten anslutningssträngen}** platshållaren med den anslutningssträng för enheten som du kopierade när du skapade den **myDeviceId** enhets-ID:
   
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
   
    Den **klienten** objektet innehåller de metoder som krävs för att interagera med enheten twins från enheten. Föregående kod när den initieras den **klienten** objekt, hämtar enheten dubbla för **myDeviceId**, och bifogar en hanterare för uppdatering på Egenskaper. Hanteraren verifierar att det är en verklig configuration ändringsbegäran genom att jämföra configIds sedan anropar en metod som startar konfigurationsändringen.
   
    Observera att för enkelhetens skull, föregående kod använder en hårdkodad standard för den inledande konfigurationen. En verklig app skulle förmodligen att läsa in konfigurationen från en lokal lagring.
   
   > [!IMPORTANT]
   > Önskad egenskapen Ändringshändelser släpps alltid en gång vid enhetsanslutning, se till att kontrollera att det finns en verklig ändring i egenskaperna innan du utför några åtgärder.
   > 
   > 
5. Lägg till följande metoder innan den `client.open()` anrop:
   
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
   
    Den **initConfigChange** metoden uppdaterar rapporterade egenskaper för lokal enhet dubbla objektet med begäran om uppdatering konfiguration och anger statusen till **väntande**, uppdaterar sedan enheten dubbla på den tjänsten. När en uppdatering av enheten dubbla, den simulerar en tidskrävande process som slutar i körningen av **completeConfigChange**. Den här metoden uppdaterar den lokala enheten dubbla rapporterade egenskaper som anger status till **lyckade** och ta bort den **pendingConfig** objekt. Sedan uppdaterar den enheten dubbla på tjänsten.
   
    Observera att, för att spara bandbredd, rapporteras egenskaper uppdateras genom att ange egenskaperna som ska ändras (med namnet **korrigering** i koden ovan), i stället för att ersätta hela dokumentet.
   
   > [!NOTE]
   > Den här självstudiekursen simulerar inte någon funktion för samtidiga konfigurationsuppdateringar. Vissa processer för uppdatering av konfiguration kanske kan hantera förändringar av mål-konfiguration när uppdateringen körs, andra behöva placeras i kö och andra kan avvisa dem med ett feltillstånd. Se till att tänka på önskat beteende för din specifika konfigurationsprocessen och Lägg till lämpliga logiken innan du påbörjar konfigurationsändringen.
   > 
   > 
6. Kör appen enhet:
   
        node SimulateDeviceConfiguration.js
   
    Du bör se meddelandet `retrieved device twin`. Fortsätt att köra appen.

## <a name="create-the-service-app"></a>Skapa service-appen
I det här avsnittet skapar du en Node.js-konsolprogram som uppdaterar det *önskade egenskaper* på enhet-dubbla som är associerade med **myDeviceId** med ett nytt telemetri configuration-objekt. Sedan frågar enheten-twins lagras i IoT-hubb och visar skillnaden mellan önskade och rapporterade konfigurationer av enheten.

1. Skapa en ny tom mapp som kallas **setdesiredandqueryapp**. I den **setdesiredandqueryapp** mapp, skapa en ny package.json-fil med följande kommando vid en kommandotolk. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **setdesiredandqueryapp** mapp, kör följande kommando för att installera den **azure iothub** paketet:
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. Med hjälp av en textredigerare, skapa en ny **SetDesiredAndQuery.js** filen i den **setdesiredandqueryapp** mapp.
4. Lägg till följande kod i den **SetDesiredAndQuery.js** filen och Ersätt den **{anslutningssträngen för iot-hubb}** med IoT-hubb anslutningssträngen som du kopierade när du skapade din hubb:
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    Den **registret** objektet innehåller de metoder som krävs för att interagera med enheten twins från tjänsten. Föregående kod när den initieras den **registret** objekt, hämtar enheten dubbla för **myDeviceId**, och uppdaterar dess egenskaper med ett nytt telemetri configuration-objekt. Därefter anropar den **queryTwins** fungerar händelse 10 sekunder.

    > [!IMPORTANT]
    > Det här programmet frågar IoT-hubb var 10: e sekund som illustration. Använda frågor för att generera användarinriktad rapporter över flera enheter och inte för att identifiera ändringar. Om din lösning kräver meddelanden i realtid av enhetshändelser, använder [dubbla meddelanden][lnk-twin-notifications].
    > 
    >.

1. Lägg till följande kod behörigheten innan den `registry.getDeviceTwin()` anrop för att implementera den **queryTwins** funktionen:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    Föregående kod frågorna enheten twins lagras i IoT-hubb och skriver ut önskad och rapporterade telemetri konfigurationer. Referera till den [IoT-hubb frågespråket] [ lnk-query] att lära dig hur du skapar omfattande rapporter på alla enheter.
2. Med **SimulateDeviceConfiguration.js** körs, köra program med:
   
        node SetDesiredAndQuery.js 5m
   
    Du bör se rapporterade konfigurationsändringen från **lyckade** till **väntande** till **lyckade** igen med den nya aktivt frekvens som skickar fem minuter i stället för 24 timmar.
   
   > [!IMPORTANT]
   > Det finns en fördröjning på upp till en minut mellan enheten rapporten igen och frågeresultatet. Detta är att aktivera query-infrastruktur för att arbeta med mycket hög skala. Att hämta konsekvent vyer för en enskild enhet dubbel användning av **getDeviceTwin** metod i den **registret** klass.
   > 
   > 

## <a name="next-steps"></a>Nästa steg
I kursen får du ange en önskad konfiguration som *önskade egenskaper* från en backend-app och skrev en simulerad enhetsapp för att identifiera den ändringen och simulera en flera steg uppdateringsprocess reporting statusen  *rapporterade egenskaper* till dubbla för enheten.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb] [ lnk-iothub-getstarted] självstudiekursen
* schemalägga eller utföra åtgärder på stora mängder enheter finns i [schema och broadcast jobb] [ lnk-schedule-jobs] kursen.
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app), med den [metoder från direkt] [ lnk-methods-tutorial] kursen.

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

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
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
