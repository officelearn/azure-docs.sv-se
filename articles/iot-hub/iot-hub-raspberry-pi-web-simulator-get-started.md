---
title: Simulerade hallon Pi till molnet (Node.js) – ansluta hallon Pi web simulator som Azure IoT Hub | Microsoft Docs
description: Ansluta hallon Pi web simulator till Azure IoT-hubb för hallon Pi att skicka data till Azure-molnet.
author: rangv
manager: ''
keywords: Raspberry pi simulatorn, azure iot raspberry pi, raspberry pi iot-hubb, raspberry pi skickar data till molnet, raspberry pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: dc8216f6b0a610b556b94637970bfd3e721f38d2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636230"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Ansluta hallon Pi online simulator till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här kursen kan du börja genom att lära dig grunderna i att arbeta med hallon Pi online simulatorn. Du lär dig sedan sömlöst ansluter Pi simulatorn till molnet med hjälp av [Azure IoT Hub](iot-hub-what-is-iot-hub.md). 

Om du har fysiska enheter gå [ansluta hallon Pi till Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) att komma igång. 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>Vad du gör

* Lär dig grunderna i hallon Pi online simulatorn.
* Skapa en IoT-hubb.
* Registrera en enhet för Pi i din IoT-hubb.
* Kör ett exempelprogram på Pi simulerade sensordata ska skickas till din IoT-hubb.

Simulerade hallon Pi att ansluta till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram med simulatorn att generera sensordata. Slutligen kan du skicka dessa sensordata för din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure IoT-hubb och hämta din nya anslutningssträngen för enheten. Om du inte har ett Azure-konto [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) i bara några minuter.
* Hur du arbetar med hallon Pi online simulatorn.
* Hur du skickar sensordata till din IoT-hubb.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Översikt över hallon Pi web simulator

Klicka på knappen för att starta hallon Pi online simulatorn.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Starta Raspberry Pi Simulator</a>

Det finns tre områden i web-simulatorn.
1. Sammansättningen område - standard kretsen är att en Pi ansluter med en BME280 sensor och en Indikator. Området är låst i förhandsversionen så för närvarande kan du inte göra anpassning.
2. Kodning område - redigerare online koden du till kod med hallon Pi. Exempelprogrammet standard hjälper dig att samla in sensordata från BME280 sensor och skickar till din Azure IoT-hubb. Programmet är helt kompatibel med verkliga Pi-enheter. 
3. Integrerad konsolfönstret - visar utdata från din kod. Det finns tre knappar längst upp i det här fönstret.
   * **Kör** -köra programmet i området för kodning.
   * **Återställ** -återställa området kodning till exempelprogrammet standard.
   * **Vikning/Expandera** -det finns en knapp för du vikning/Expandera konsolfönstret på höger sida.

> [!NOTE] 
Web-simulatorn hallon Pi är nu tillgängliga i förhandsversionen. Vi hoppas att du hör din röst i den [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Källkoden är offentlig på [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Översikt över Pi online simulator](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Kör ett exempelprogram på Pi web simulator

1. Kontrollera att du arbetar med exempelprogrammet som standard i kodning område. Ersätt platshållaren i rad 15 med anslutningssträngen för Azure IoT hub-enhet.
   ![Ersätt anslutningssträngen enhet](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Klicka på **kör** eller typ `npm start` att köra programmet.


Du bör se följande utdata som visar dessa sensordata och meddelanden som skickas till din IoT-hubb ![utdata - sensordata som skickats från hallon Pi till din IoT-hubb](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in sensordata och skicka den till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
