---
title: Komma igång med Azure IoT Hub-enhetstvillingar (nod) | Microsoft-dokument
description: Så här använder du Azure IoT Hub-enhetstvillingar för att lägga till taggar och sedan använda en IoT Hub-fråga. Du använder Azure IoT SDK:er för Node.js för att implementera den simulerade enhetsappen och en tjänstapp som lägger till taggarna och kör IoT Hub-frågan.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt
ms.openlocfilehash: e65c781bd5cb62bdaa693b854caafd5f91fd497e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732284"
---
# <a name="get-started-with-device-twins-nodejs"></a>Komma igång med enhetstvillingar (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du två Node.js-konsolappar:

* **AddTagsAndQuery.js**, en Node.js backend-app, som lägger till taggar och frågor enhetstvillingar.

* **TwinSimulatedDevice.js**, en Node.js-app, som simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapats tidigare och rapporterar dess anslutningstillstånd.

> [!NOTE]
> Artikeln [Azure IoT SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK:er som du kan använda för att skapa både enhets- och backend-appar.
>

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Node.js version 10.0.x eller senare.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänstappen

I det här avsnittet skapar du en Node.js-konsolapp som lägger till platsmetadata till enhetstvillingen som är associerad med **myDeviceId**. Sedan frågar de enhetstvillingar som lagras i IoT-hubben och väljer de enheter som finns i USA och sedan de som rapporterar en mobilanslutning.

1. Skapa en ny tom mapp med **namnet addtagsandqueryapp**. Skapa en ny package.json-fil med följande kommando i kommandotolken i mappen **addtagsandqueryapp.** Parametern `--yes` accepterar alla standardvärden.

    ```cmd/sh
    npm init --yes
    ```

2. Kör följande kommando i kommandotolken i mappen **addtagsandqueryapp** för att installera **azure-iothub-paketet:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Skapa en ny **AddTagsAndQuery.js-fil** med hjälp av en textredigerare i mappen **addtagsandqueryapp.**

4. Lägg till följande kod i filen **AddTagsAndQuery.js.** Ersätt `{iot hub connection string}` med anslutningssträngen för IoT Hub som du kopierade i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

   ``` javascript
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
   ```

    **Registerobjektet** exponerar alla metoder som krävs för att interagera med enhetstvillingar från tjänsten. Den föregående koden initierar först **Registry-objektet,** hämtar sedan enhetstvillingen för **myDeviceId**och uppdaterar slutligen sina taggar med önskad platsinformation.

    När du har uppdaterat taggarna **anropas funktionen queryTwins.**

5. Lägg till följande kod i slutet av **AddTagsAndQuery.js** för att implementera **funktionen queryTwins:**

   ```javascript
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
   ```

    Den tidigare koden kör två frågor: den första väljer bara enhetstvillingar för enheter som finns i **Redmond43-anläggningen** och den andra förfinar frågan för att välja endast de enheter som också är anslutna via mobilnätet.

    När koden skapar **frågeobjektet** anger den det maximala antalet returnerade dokument i den andra parametern. **Frågeobjektet** innehåller en boolesk egenskap **hasMoreResults** som du kan använda för att anropa **nextAsTwin-metoderna** flera gånger för att hämta alla resultat. En metod som kallas **nästa** är tillgänglig för resultat som inte är enhetstvillingar, till exempel resultatet av aggregeringsfrågor.

6. Kör programmet med:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för frågan som begränsar resultaten till enheter som använder ett mobilnät.

   ![Se en enhet i frågeresultatet](media/iot-hub-node-node-twin-getstarted/service1.png)

I nästa avsnitt skapar du en enhetsapp som rapporterar anslutningsinformationen och ändrar resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsappen

I det här avsnittet skapar du en Node.js-konsolapp som ansluter till hubben som **myDeviceId**och uppdaterar sedan enhetstvillingens rapporterade egenskaper så att den innehåller den information som den är ansluten med ett mobilnät.

1. Skapa en ny tom mapp med namnet **reportconnectivity**. Skapa en ny package.json-fil i **mappen reportconnectivity** med följande kommando i kommandotolken. Parametern `--yes` accepterar alla standardvärden.

    ```cmd/sh
    npm init --yes
    ```

2. Kör följande kommando i kommandotolken i **mappen reportconnectivity** för att installera **azure-iot-device-** och **azure-iot-device-mqtt-paket:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Skapa en ny **ReportConnectivity.js-fil** i **mappen reportconnectivity** med hjälp av en textredigerare.

4. Lägg till följande kod i filen **ReportConnectivity.js.** Ersätt `{device connection string}` med enhetsanslutningssträngen som du kopierade när du skapade **myDeviceId-enhetsidentiteten** i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```javascript
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
    ```

    **Klientobjektet** exponerar alla metoder som du behöver för att interagera med enhetstvillingar från enheten. Den tidigare koden, efter att **Client** den initierar klientobjektet, hämtar enhetstvillingen för **myDeviceId** och uppdaterar den rapporterade egenskapen med anslutningsinformationen.

5. Köra enhetsappen

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Du bör se meddelandet `twin state reported`.

6. Nu när enheten rapporterade sin anslutningsinformation bör den visas i båda frågorna. Gå tillbaka i **mappen addtagsandqueryapp** och kör frågorna igen:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Den här gången **myDeviceId** ska visas i båda frågeresultaten.

    ![Visa myDeviceId i båda frågeresultaten](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp för att rapportera enhetsanslutningsinformation i enhetstvillingen. Du har också lärt dig hur du frågar den här informationen med hjälp av frågespråket SQL-liknande IoT Hub.

Använd följande resurser för att lära dig hur du:

* skicka telemetri från enheter med i-guiden [Kom igång med IoT Hub,](quickstart-send-telemetry-node.md)

* konfigurera enheter med hjälp av enhetstvillingens önskade egenskaper med de [önskade egenskaperna Använd för att konfigurera enhetsdator,](tutorial-device-twins.md)

* styra enheter interaktivt (till exempel slå på en fläkt från en användarstyrd app), med [självstudiekursen Använd direkta metoder.](quickstart-control-device-node.md)
