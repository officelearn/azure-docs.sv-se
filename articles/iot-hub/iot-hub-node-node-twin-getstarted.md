---
title: "Kom igång med Azure IoT Hub-enhet twins (nod) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub-enhet twins att lägga till taggar och sedan använda en IoT-hubb-fråga. Azure IoT-SDK för Node.js används för att implementera simulerade enheten appen och en service-app som lägger till taggar och IoT-hubb-frågan körs."
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: df49f054b5eb26c3d68f088bc05f5209cf2ebccf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-node"></a>Kom igång med enheten twins (nod)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här kursen har du två Node.js-konsolappar:

* **AddTagsAndQuery.js**, en Node.js backend-app som lägger till taggar och frågar enheten twins.
* **TwinSimulatedDevice.js**, en Node.js-app som simulerar en enhet som ansluter till din IoT-hubb med enhetens identitet skapade tidigare och rapporterar tillståndet anslutningen.

> [!NOTE]
> Artikeln [Azure IoT SDK] [ lnk-hub-sdks] innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.
> 
> 

Den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Skapa service-appen
I det här avsnittet skapar du en Node.js-konsolprogram som lägger till platsen metadata i enheten-dubbla som är associerade med **myDeviceId**. Frågar sedan enheten-twins lagras i IoT-hubb som att markera de enheter som finns i USA och de som rapporterar en mobil anslutning.

1. Skapa en ny tom mapp som kallas **addtagsandqueryapp**. I den **addtagsandqueryapp** mapp, skapa en ny package.json-fil med följande kommando vid en kommandotolk. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **addtagsandqueryapp** mapp, kör följande kommando för att installera den **azure iothub** paketet:
   
    ```
    npm install azure-iothub --save
    ```
3. Med hjälp av en textredigerare, skapa en ny **AddTagsAndQuery.js** filen i den **addtagsandqueryapp** mapp.
4. Lägg till följande kod i den **AddTagsAndQuery.js** filen och Ersätt den **{anslutningssträngen för iot-hubb}** med IoT-hubb anslutningssträngen som du kopierade när du skapade din hubb:
   
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
   
    Den **registret** objektet innehåller de metoder som krävs för att interagera med enheten twins från tjänsten. Föregående kod först initierar den **registret** objekt och hämtar enheten dubbla för **myDeviceId**, och slutligen uppdateras taggarna med informationen som önskad plats.
   
    Efter uppdatering av taggarna anropar den **queryTwins** funktion.
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
   
    Föregående kod körs två frågor: först väljer enheten twins av enheter som finns i den **Redmond43** anläggningen och andra förfinar frågan för att markera de enheter som även är anslutna via mobilnät.
   
    Föregående kod när det skapas den **frågan** objekt, anger ett maximalt antal returnerade dokument. Den **frågan** objektet innehåller en **hasMoreResults** boolesk egenskap som du kan använda för att anropa den **nextAsTwin** metoder flera gånger för att hämta alla resultat. En metod som kallas **nästa** är tillgänglig för resultat som är inte enheten twins, till exempel resultatet av aggregering frågor.
6. Kör programmet med:
   
        node AddTagsAndQuery.js
   
    Du bör se en enhet i resultaten för frågan ställa för alla enheter i **Redmond43** och ingen för frågan som begränsar resultat till enheter som använder ett mobilnät.
   
    ![][1]

I nästa avsnitt skapar du en enhetsapp som rapporterar information om anslutningar och ändras resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsapp
I det här avsnittet skapar du en Node.js-konsolprogram som ansluter till din hubb som **myDeviceId**, och sedan uppdaterar sin enhet dubbla har rapporterat att innehålla den information som den är ansluten med hjälp av ett mobilnät.


1. Skapa en ny tom mapp som kallas **reportconnectivity**. I den **reportconnectivity** mapp, skapa en ny package.json-fil med följande kommando vid en kommandotolk. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **reportconnectivity** mapp, kör följande kommando för att installera den **azure iot-enhet**, och **azure-iot-enhet – mqtt** paketet:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Med hjälp av en textredigerare, skapa en ny **ReportConnectivity.js** filen i den **reportconnectivity** mapp.
4. Lägg till följande kod i den **ReportConnectivity.js** filen och Ersätt den **{enheten anslutningssträngen}** platshållaren med den anslutningssträng för enheten som du kopierade när du skapade **myDeviceId** enhets-ID:
   
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
   
    Den **klienten** objektet innehåller de metoder som du behöver för att interagera med enheten twins från enheten. Föregående kod när den initieras den **klienten** objekt, hämtar enheten dubbla för **myDeviceId** och uppdaterar egenskapen rapporterade med information om anslutningar.
5. Kör enhetsapp
   
        node ReportConnectivity.js
   
    Du bör se meddelandet `twin state reported`.
6. Nu när enheten rapporteras dess anslutningsinformation, bör den visas i båda frågor. Gå tillbaka i den **addtagsandqueryapp** mappen och kör frågorna igen:
   
        node AddTagsAndQuery.js
   
    Den här gången **myDeviceId** ska visas i båda frågeresultaten.
   
    ![][3]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du lagt till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp till enheten anslutningsinformation i dubbla för enheten. Du också fått lära dig hur man frågar den här informationen med hjälp av frågespråket i SQL-liknande IoT-hubb.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb] [ lnk-iothub-getstarted] självstudiekursen
* Konfigurera enheter med hjälp av enheten två egenskaper med den [Använd önskad egenskaper att konfigurera enheter] [ lnk-twin-how-to-configure] självstudiekursen
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app), med den [metoder från direkt] [ lnk-methods-tutorial] kursen.

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

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
