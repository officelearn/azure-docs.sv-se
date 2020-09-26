---
title: Ansluta IoT Plug and Play Preview-exempel Node.js komponent enhets kod till IoT Hub | Microsoft Docs
description: Skapa och kör IoT Plug and Play Preview-exempel Node.js enhets kod som använder flera komponenter och ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js
ms.openlocfilehash: 24bfbf4199671da497844444a57e566e66eb8c90
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308239"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Självstudie: ansluta en exempel-IoT Plug and Play förhandsgranska flera komponent enhets program för att IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter och rot gränssnitt, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet är skrivet för Node.js och ingår i Azure IoT Hub Device SDK för Node.js. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du Node.js på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här självstudien använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här självstudien:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Du kan också använda Azure IoT Explorer-verktyget för att hitta anslutnings strängen för IoT Hub.

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här självstudien:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Ladda ned koden

I den här självstudien förbereder du en utvecklings miljö som du kan använda för att klona och skapa Azure IoT Hub Device SDK för Node.js.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Microsoft Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-lagringsplatsen på den här platsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Den här åtgärden kan ta flera minuter att slutföra.

## <a name="install-required-libraries"></a>Installera nödvändiga bibliotek

Du kan använda enhets-SDK: n för att bygga den inkluderade exempel koden. Det program som du skapar simulerar en Plug and Play enhet med flera komponenter och rot gränssnitt som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I ett lokalt terminalfönster går du till mappen för den klonade lagrings platsen och navigerar till mappen */Azure-IoT-SDK-Node/Device/samples/PnP* . Kör sedan följande kommando för att installera de bibliotek som krävs:

```cmd/sh
npm install
```

Detta kommer att installera relevanta NPM-filer som krävs för att köra exemplen i mappen.

1. Konfigurera miljövariabeln med enhets anslutnings strängen som du antecknade tidigare:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>Granska koden

Navigera till mappen *Azure-IoT-SDK-node\device\samples\pnp*

Mappen *Azure-IoT-SDK-node\device\samples\pnp* innehåller exempel koden för IoT plug and Play temperatur styrenhets enheten.

Koden i *pnpTemperatureController.js* -filen implementerar en IoT plug and Play-enhet för temperatur styrenhet. Modellen som det här exemplet implementerar använder [flera komponenter](concepts-components.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Öppna *pnpTemperatureController.js* -filen i valfri kod redigerare. Exempel koden visar hur du:

1. Definiera `modelId` DTMI för den enhet som implementeras. Den här DTMI är användardefinierad och måste matcha DTMI för [temperatur styrenhetens DTDL-modell](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

2. Implementera de komponenter som definieras i DTDL-modellen för temperatur styrenheten. Komponenterna i en riktig temperatur kontroll bör implementera dessa två gränssnitt. Dessa två gränssnitt har redan publicerats på en central lagrings plats. I det här exemplet är de två gränssnitten:
  - Termostat
  - Enhets information som har utvecklats av Azure

3. Definiera komponent namn. Det här exemplet har två termostater och en komponent för enhets information.

4. Definiera kommando namn. Detta är de kommandon som enheten svarar på.

5. Definiera `serialNumber` konstanten. En `serialNumber` viss enhet korrigeras.

6. Definiera kommando hanterare.

7. Definiera funktionerna för att skicka kommando svar.

8. Definiera hjälp funktioner för att logga kommando begär Anden.

9. Definiera en hjälp funktion för att skapa egenskaperna.

10. Definiera en lyssnare för egenskaps uppdateringar.

11. Definiera en funktion för att skicka telemetri från den här enheten. Både termostater och rot komponenten skickar telemetri. Den här funktionen tar emot komponent namnet som parameter.

12. Definiera en `main` funktion som:

    1. Använder enhets-SDK för att skapa en enhets klient och ansluta till IoT Hub. Enheten tillhandahåller `modelId` så att IoT Hub kan identifiera enheten som en IoT plug and Play-enhet.

    1. Börjar lyssna efter kommando begär Anden med hjälp av `onDeviceMethod` funktionen. Funktionen konfigurerar en lyssnare för kommando begär Anden från tjänsten:
        - Enhetens DTDL definierar `reboot` och- `getMaxMinReport` kommandon.
        - `commandHandler`Funktionen definierar hur enheten svarar på ett kommando.

    1. Börjar skicka telemetri med hjälp av `setInterval` och `sendTelemetry` .

    1. Använder `helperCreateReportedPropertiesPatch` funktionen för att skapa egenskaperna och `updateComponentReportedProperties` för att uppdatera egenskaperna.

    1. Använder `desiredPropertyPatchListener` för att lyssna efter egenskaps uppdateringar.

    1. Inaktiverar alla lyssnare och uppgifter och avslutar slingan när du trycker på **q** eller **q**.

Nu när du har sett koden använder du följande kommando för att köra exemplet:

```cmd\sh
node pnpTemperatureController.js
```

Följande utdata visas, vilket anger att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar.

![Enhets bekräftelse meddelanden](media/tutorial-multiple-components-node/multiple-component.png)

Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter en IoT Plug and Play-enhet med komponenter till en IoT-hubb. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Preview Modeling Developer Guide](concepts-developer-guide.md)
