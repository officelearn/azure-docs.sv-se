---
title: Anslut Raspberry Pi-webbsimulatorn till Azure IoT Hub (Node.js)
description: Anslut Raspberry Pi-webbsimulatorn till Azure IoT Hub för Raspberry Pi för att skicka data till Azure-molnet.
author: wesmc7777
manager: philmea
keywords: raspberry pi simulator, azure iot raspberry pi, raspberry pi iot hub, raspberry pi skicka data till molnet, raspberry pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954534"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Anslut Raspberry Pi onlinesimulator till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här guiden börjar du med att lära dig grunderna i att arbeta med Raspberry Pi online simulator. Du lär dig sedan hur du sömlöst ansluter Pi-simulatorn till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md).

Om du har fysiska enheter besöker du [Anslut Raspberry Pi till Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) för att komma igång.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>Vad du gör

* Lär dig grunderna i Raspberry Pi online simulator.

* Skapa en IoT-hubb.

* Registrera en enhet för Pi i din IoT-hubb.

* Kör ett exempelprogram på Pi för att skicka simulerade sensordata till din IoT-hubb.

Anslut simulerad Raspberry Pi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram med simulatorn för att generera sensordata. Slutligen skickar du sensordata till din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en Azure IoT-hubb och hämtar din nya enhetsanslutningssträng. Om du inte har ett Azure-konto [skapar du ett kostnadsfritt Azure-utvärderingskonto](https://azure.microsoft.com/free/) på bara några minuter.

* Hur man arbetar med Raspberry Pi online simulator.

* Så här skickar du sensordata till din IoT-hubb.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Översikt över Raspberry Pi webbsimulator

Klicka på knappen för att starta Raspberry Pi online simulator.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Starta Raspberry Pi Simulator</a>

Det finns tre områden i webbsimulatorn.

1. Sammansättningsområdeet – standardkretsen är att en Pi ansluts till en BME280-sensor och en LED. Området är låst i förhandsversionen, så för tillfället går det inte att göra anpassningar.

2. Kodningsområdet – en onlinekodredigerare så att du kan köra med Raspberry Pi. Standardexempelprogrammet hjälper till att samla in sensordata från BME280-sensorn och skickar till din Azure IoT Hub. Programmet är helt kompatibelt med verkliga Pi-enheter. 

3. Integrerat konsolfönster – visar utdata för din kod. Längst upp i det här fönstret finns tre knappar.

   * **Run** (Kör) – kör programmet i kodningsområdet.

   * **Reset** (Återställ) – återställ kodningsområdet till standardexempelprogrammet.

   * **Fold/Expand** (Vik/expandera) – på höger sida finns det en knapp som viker/expanderar konsolfönstret.

> [!NOTE]
> Raspberry Pi webbsimulatorn finns nu i förhandsversion. Vi vill höra din röst i [Gitter Chatroom.](https://gitter.im/Microsoft/raspberry-pi-web-simulator) Källkoden är offentlig på [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Översikt över Pi online simulator](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Köra ett exempelprogram på Pi-webbsimulatorn

1. I kodningsområdet ser du till att du arbetar med det standardmässiga exempelprogrammet. Ersätt platshållaren på rad 15 med Azure IoT-hubbens anslutningssträng.
1. 
   ![Ersätta enhetens anslutningssträng](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Välj **Kör** `npm start` eller skriv för att köra programmet.

Du bör se följande utdata som visar sensordata och meddelanden som ![skickas till din IoT-hubbutdata - sensordata som skickas från Raspberry Pi till din IoT-hubb](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs de meddelanden som tas emot av din hubb

Ett sätt att övervaka meddelanden som tas emot av din IoT-hubb från den simulerade enheten är att använda Azure IoT Tools for Visual Studio Code. Mer information finns i [Använda Azure IoT Tools for Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Om du vill ha fler sätt att bearbeta data som skickas av enheten fortsätter du till nästa avsnitt.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in sensordata och skicka dem till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
