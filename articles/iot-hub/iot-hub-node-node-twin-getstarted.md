---
title: Kom igång med enhetstvillingar för Azure IoT Hub (Node) | Microsoft Docs
description: Så här använder enhetstvillingar för Azure IoT Hub för att lägga till taggar och sedan använda en IoT Hub-fråga. Du kan använda Azure IoT SDK för Node.js för att implementera app för simulerade enheter och en service-app som lägger till taggar och kör IoT Hub-frågan.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: 45a3b236c4bd603fffc248ffbd19a938ca9cb572
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188122"
---
# <a name="get-started-with-device-twins-node"></a>Kom igång med enhetstvillingar (Node)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du två Node.js-konsolappar:

* **AddTagsAndQuery.js**, en Node.js backend-app, som lägger till taggar och frågar enhetstvillingar.
* **TwinSimulatedDevice.js**, en Node.js-app som simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare och rapporterar tillståndet för anslutningen.

> [!NOTE]
> Artikeln [Azure IoT SDK: er] [ lnk-hub-sdks] innehåller information om Azure IoT SDK: er som du kan använda för att skapa appar för både enheten och backend-server.
> 
> 

Den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Skapa service-app
I det här avsnittet skapar du en Node.js-konsolapp som lägger till platsmetadata i enhetstvillingen som är associerade med **myDeviceId**. Därefter beordrar enhetstvillingar som lagras i IoT hub att välja de enheter som finns i USA och de som rapporterar en mobil anslutning.

1. Skapa en ny tom mapp med namnet **addtagsandqueryapp**. I den **addtagsandqueryapp** mapp, skapa en ny package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i den **addtagsandqueryapp** mapp, kör följande kommando för att installera den **azure-iothub** paketet:
   
    ```
    npm install azure-iothub --save
    ```
3. Använd en textredigerare och skapa en ny **AddTagsAndQuery.js** fil i den **addtagsandqueryapp** mapp.
4. Lägg till följande kod till den **AddTagsAndQuery.js** filen och ersätta den **{iot hub-anslutningssträngen}** med IoT Hub-anslutningssträngen som du kopierade när du skapade din hubb:
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    Den **registret** objektet innehåller alla metoder som krävs för att interagera med enhetstvillingar från tjänsten. Föregående kod först initierar den **registret** objekt och sedan hämtar enhetstvillingen för **myDeviceId**, och uppdaterar slutligen taggarna med informationen som önskad plats.
   
    När du har uppdaterat taggarna anropar den **queryTwins** funktion.
5. Lägg till följande kod i slutet av **AddTagsAndQuery.js** att implementera den **queryTwins** funktionen:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    Föregående kod körs två frågor: först väljer endast enhetstvillingar av enheter som finns i den **Redmond43** anläggning och andra förfinar fråga för att välja endast de enheter som även är anslutna via mobilnät.
   
    Föregående kod när det skapas den **fråga** objekt, anger ett maximalt antal returnerade dokument. Den **fråga** objektet innehåller en **hasMoreResults** boolesk egenskap som du kan använda för att anropa den **nextAsTwin** metoder flera gånger för att hämta alla resultat. En metod som kallas **nästa** är tillgänglig för resultat som är inte enhetstvillingar, till exempel resultat mängdfrågor.
6. Kör programmet med:
   
        node AddTagsAndQuery.js
   
    Du bör se en enhet i resultaten för att fråga ställa för alla enheter i **Redmond43** och ingenting alls under den fråga som begränsar resultaten till enheter som använder ett mobilnät.
   
    ![][1]

I nästa avsnitt skapar du en app för enheter som rapporterar anslutningsinformation och ändras resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa en app för enheter
I det här avsnittet skapar du en Node.js-konsolapp som ansluter till hubben som **myDeviceId**, och sedan de uppdateringar som dess enhetstvilling är rapporterade egenskaper som innehåller informationen om att den är ansluten med hjälp av ett mobilt nätverk.


1. Skapa en ny tom mapp med namnet **reportconnectivity**. I den **reportconnectivity** mapp, skapa en ny package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i den **reportconnectivity** mapp, kör följande kommando för att installera den **azure-iot-device**, och **azure-iot-device-mqtt** paketet:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Använd en textredigerare och skapa en ny **ReportConnectivity.js** fil i den **reportconnectivity** mapp.
4. Lägg till följande kod till den **ReportConnectivity.js** filen och ersätta den **{enhetsanslutningssträngen}** med enhetens anslutningssträng som du kopierade när du skapade **myDeviceId** enhetsidentitet:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    Den **klienten** objektet innehåller alla metoder som du behöver för att interagera med enhetstvillingar från enheten. Föregående kod när den initierar den **klienten** objekt, hämtar enhetstvillingen för **myDeviceId** och uppdaterar sin rapporterad egenskap med anslutningsinformation.
5. Kör app för enheter
   
        node ReportConnectivity.js
   
    Du bör se meddelandet `twin state reported`.
6. Nu när enheten rapporteras dess anslutningsinformation som det visas i båda frågor. Gå tillbaka i den **addtagsandqueryapp** mappen och kör frågorna igen:
   
        node AddTagsAndQuery.js
   
    Den här gången **myDeviceId** ska visas i båda frågeresultatet.
   
    ![][3]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagts till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp till rapporten anslutning enhetsinformation i enhetstvillingen. Du också lärt dig hur du frågar efter den här informationen med hjälp av SQL-liknande IoT Hub-frågespråk.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT Hub] [ lnk-iothub-getstarted] självstudien
* Konfigurera enheter som använder enhetstvillingens egenskaper med den [Använd önskade egenskaper för att konfigurera enheter] [ lnk-twin-how-to-configure] självstudien
* Kontrollera enheter interaktivt (till exempel aktivera en fans, från en användarstyrd app) och med den [Använd direkta metoder] [ lnk-methods-tutorial] självstudien.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: quickstart-send-telemetry-node.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/quickstart-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: tutorial-device-twins.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
