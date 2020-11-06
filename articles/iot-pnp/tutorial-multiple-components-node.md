---
title: Självstudie – ansluta IoT-Plug and Play exempel Node.js komponent enhets kod till Azure IoT Hub | Microsoft Docs
description: Självstudie – Skapa och kör IoT Plug and Play-exempel Node.js enhets kod som använder flera komponenter och ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js
ms.openlocfilehash: 70fa10f5319de072ee8ea7dad4e73c58d4bdeede
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421438"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-nodejs"></a>Självstudie: ansluta en exempel-IoT Plug and Play flera komponent enhets program till IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet är skrivet för Node.js och ingår i Azure IoT Hub Device SDK för Node.js. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

För att slutföra den här självstudien behöver du Node.js på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Ladda ned koden

Om du har slutfört [snabb starten: ansluta ett exempel på IoT plug and Play Device-program som körs på Windows till IoT Hub (Node)](quickstart-connect-device-node.md)har du redan klonat lagrings platsen.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Microsoft Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-lagringsplatsen på den här platsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installera nödvändiga bibliotek

Du kan använda enhets-SDK: n för att bygga den inkluderade exempel koden. Det program som du skapar simulerar en Plug and Play enhet med flera komponenter som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I ett lokalt terminalfönster går du till mappen för den klonade lagrings platsen och navigerar till mappen */Azure-IoT-SDK-Node/Device/samples/PnP* . Kör sedan följande kommando för att installera de bibliotek som krävs:

```cmd/sh
npm install
```

Detta kommer att installera relevanta NPM-filer som krävs för att köra exemplen i mappen.

## <a name="review-the-code"></a>Granska koden

Navigera till mappen *Azure-IoT-SDK-node\device\samples\pnp*

Mappen *Azure-IoT-SDK-node\device\samples\pnp* innehåller exempel koden för IoT plug and Play temperatur styrenhets enheten.

Koden i *pnpTemperatureController.js* -filen implementerar en IoT plug and Play-enhet för temperatur styrenhet. Modellen som det här exemplet implementerar använder [flera komponenter](concepts-components.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Öppna *pnpTemperatureController.js* -filen i valfri kod redigerare. Exempel koden visar hur du:

- Definiera `modelId` DTMI för den enhet som implementeras. Den här DTMI är användardefinierad och måste matcha DTMI för [temperatur styrenhetens DTDL-modell](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

- Implementera de komponenter som definieras i DTDL-modellen för temperatur styrenheten. Komponenterna i en riktig temperatur kontroll bör implementera dessa två gränssnitt. Dessa två gränssnitt har redan publicerats på en central lagrings plats. I det här exemplet är de två gränssnitten:

  - Termostat
  - Enhets information som har utvecklats av Azure

- Definiera komponent namn. Det här exemplet har två termostater och en komponent för enhets information.

- Definiera kommando namn. Detta är de kommandon som enheten svarar på.

- Definiera `serialNumber` konstanten. En `serialNumber` viss enhet korrigeras.

- Definiera kommando hanterare.

- Definiera funktionerna för att skicka kommando svar.

- Definiera hjälp funktioner för att logga kommando begär Anden.

- Definiera en hjälp funktion för att skapa egenskaperna.

- Definiera en lyssnare för egenskaps uppdateringar.

- Definiera en funktion för att skicka telemetri från den här enheten. Både termostater och standard komponenten skicka telemetri. Den här funktionen tar emot komponent namnet som parameter.

- Definiera en `main` funktion som:

  - Använder enhets-SDK för att skapa en enhets klient och ansluta till IoT Hub. Enheten tillhandahåller `modelId` så att IoT Hub kan identifiera enheten som en IoT plug and Play-enhet.

  - Börjar lyssna efter kommando begär Anden med hjälp av `onDeviceMethod` funktionen. Funktionen konfigurerar en lyssnare för kommando begär Anden från tjänsten:

    - Enhetens DTDL definierar `reboot` och- `getMaxMinReport` kommandon.
    - `commandHandler`Funktionen definierar hur enheten svarar på ett kommando.

  - Börjar skicka telemetri med hjälp av `setInterval` och `sendTelemetry` .

  - Använder `helperCreateReportedPropertiesPatch` funktionen för att skapa egenskaperna och `updateComponentReportedProperties` för att uppdatera egenskaperna.

  - Använder `desiredPropertyPatchListener` för att lyssna efter egenskaps uppdateringar.

  - Inaktiverar alla lyssnare och uppgifter och avslutar slingan när du trycker på **q** eller **q**.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

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
> [IoT Plug and Play Modeling Developer Guide](concepts-developer-guide-device-csharp.md)
