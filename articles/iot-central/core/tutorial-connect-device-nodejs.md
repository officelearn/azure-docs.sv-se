---
title: Självstudie – ansluta en allmän Node.js klient-app till Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du, som enhets utvecklare, ansluter en enhet som kör en Node.js-klient till ditt Azure IoT Central-program. Du ändrar den automatiskt genererade enhets mal len genom att lägga till vyer som låter en operatör interagera med en ansluten enhet.
author: dominicbetts
ms.author: dobett
ms.date: 11/03/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
- devx-track-js
ms.openlocfilehash: 6175be702f0824ad2cd2b146e96641037407ca0b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "96018808"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Självstudie: Skapa och anslut ett klientprogram till ditt Azure IoT Central-program (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Den här självstudien visar hur du, som enhets utvecklare, ansluter ett Node.js klient program till ditt Azure IoT Central-program. I Node.jss programmet simuleras beteendet för en termostat-enhet. När programmet ansluter till IoT Central, skickas modell-ID: t för enhets modellen termostat. IoT Central använder modell-ID: t för att hämta enhets modellen och skapa en enhets mall åt dig. Du kan lägga till anpassningar och vyer i enhets mal len så att en operatör kan interagera med en enhet.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa och kör Node.js enhets koden och se hur den ansluter till ditt IoT Central-program.
> * Visa den simulerade telemetri som skickas från enheten.
> * Lägg till anpassade vyer i en enhets mall.
> * Publicera enhets mal len.
> * Använd en vy för att hantera enhets egenskaper.
> * Anropa ett kommando för att kontrol lera enheten.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av den **anpassade program** mal len. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md). Programmet måste ha skapats den 14 juli 2020 eller senare.
* En utvecklings dator med [Node.js](https://nodejs.org/) version 6 eller senare installerad. Du kan köra `node --version` på kommando raden för att kontrol lera din version. Anvisningarna i den här självstudien förutsätter att du kör **Node** -kommandot i kommando tolken i Windows. Du kan dock använda Node.js på många andra operativ system.
* En lokal kopia av [Microsoft Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-lagringsplatsen som innehåller exempel koden. Använd den här länken för att ladda ned en kopia av lagrings platsen: [Ladda ned ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Zippa sedan upp filen till en lämplig plats på den lokala datorn.

## <a name="review-the-code"></a>Granska koden

I kopian av Microsoft Azure IoT SDK för Node.js som du har hämtat tidigare öppnar du filen *Azure-IoT-SDK-Node/Device/samples/PnP/simple_thermostat.js* i en text redigerare.

När du kör exemplet för att ansluta till IoT Central, används enhets etablerings tjänsten (DPS) för att registrera enheten och skapa en anslutnings sträng. Exemplet hämtar den information om DPS-anslutningen som krävs från kommando rads miljön.

`main`Metoden:

* Skapar ett- `client` objekt och anger `dtmi:com:example:Thermostat;1` modell-ID: t innan det öppnar anslutningen.
* Skapar en kommando hanterare.
* Startar en slinga för att skicka temperatur telemetri var 10: e sekund.
* Skickar `maxTempSinceLastReboot` egenskapen till IoT Central. IoT Central ignorerar `serialNumber` egenskapen eftersom den inte är en del av enhets modellen.
* Skapar en skrivbar egenskaps hanterare.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice`Funktionen visar hur enheten använder DPS för att registrera och ansluta till IoT Central. Nytto lasten inkluderar modell-ID:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry`Funktionen visar hur enheten skickar en temperatur telemetri till IoT Central. `getCurrentTemperatureObject`Metoden returnerar ett objekt som ser ut så här `{ temperature: 45.6 }` :

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main`Metoden använder följande två metoder för att skicka `maxTempSinceLastReboot` egenskapen till IoT Central. `main`Metoden anropar `createReportPropPatch` med ett objekt som ser ut så här `{maxTempSinceLastReboot: 80.9}` :

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

`main`Metoden använder följande två metoder för att hantera uppdateringar av den skrivbara egenskapen för _mål temperaturen_ från IoT Central. Observera hur `propertyUpdateHandle` skapar svaret med version och status kod:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

`main`Metoden använder följande två metoder för att hantera anrop till `getMaxMinReport` kommandot. `getMaxMinReportObject`Metoden genererar rapporten som ett JSON-objekt:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>Hämta anslutningsinformation

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Kör koden

Kör exempel programmet genom att öppna en kommando rads miljö och navigera till mappen *Azure-IoT-SDK-Node/enhet/samples/PnP* som innehåller *simple_thermostat.js* exempel filen.

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Installera de nödvändiga paketen:

```cmd/sh
npm install
```

Kör exemplet:

```cmd/sh
node simple_thermostat.js
```

Följande utdata visar enhets registrering och anslutning till IoT Central. Exemplet skickar sedan `maxTempSinceLastReboot` egenskapen innan den börjar skicka telemetri:

```cmd/sh
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

Du kan se hur enheten svarar på kommandon och egenskaps uppdateringar:

```cmd/sh
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```

## <a name="view-raw-data"></a>Visa rå data

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## <a name="next-steps"></a>Nästa steg

Om du föredrar att fortsätta med en uppsättning IoT Central själv studie kurser och lära dig mer om hur du skapar en IoT Central lösning, se:

> [!div class="nextstepaction"]
> [Mall för att skapa en gateway-enhet](./tutorial-define-gateway-device-type.md)

Som en enhets utvecklare har du nu lärt dig grunderna för hur du skapar en enhet med hjälp av Node.js, men vissa föreslagna nästa steg är att:

* Läs [Vad är enhets mallar?](./concepts-device-templates.md) om du vill lära dig mer om rollen hets mallar när du implementerar din enhets kod.
* Läs [bli ansluten till Azure IoT Central](./concepts-get-connected.md) om du vill veta mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhets anslutningar.
* Läs [telemetri, egenskaper och kommando nytto laster](concepts-telemetry-properties-commands.md) för att lära dig mer om de data som enheten utbyter med IoT Central.
