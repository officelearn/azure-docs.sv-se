---
title: Ansluta en SensorTile.box enhet till Azure IoT Central programmet | Microsoft Docs
description: Lär dig hur du ansluter en SensorTile.box enhet till Azure IoT Central programmet som utvecklare enheten.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472198"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Anslut SensorTile.box enhet till Azure IoT Central programmet

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta SensorTile.box enheter till Microsoft Azure IoT Central programmet.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln behöver du följande resurser:

* En SensorTile.box-enhet. Mer information finns i [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* ST BLE Sensor-appen är installerad på din Android-enhet kan du [ladda ned den här](https://play.google.com/store/apps/details?id=com.st.bluems). Mer information finns: [ST BLE Sensor](https://www.st.com/stblesensor)
* Ett Azure IoT Central program som skapats från den **DevKits** programmall. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* Lägg till den **SensorTile.box** enheten mallen i ditt IoT Central-program genom att besöka den **enheten mallar** sidan, klickar på **+ ny**, och välja **SensorTile.box** mall.

### <a name="get-your-device-connection-details"></a>Hämta anslutningsinformationen för din enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **SensorTile.box** enheten mallen och gjort en notering enhetsinformation för anslutning: **Scope-ID**, **enhets-ID**, och **primärnyckel**:

1. Lägg till en enhet från Device Explorer. Välj **+ Ny > verkliga** att lägga till en riktig enhet.

    * Ange en gemen **enhets-ID**, eller använda de föreslagna **enhets-ID**.
    * Ange en **enhetsnamn**, eller använda det föreslagna namnet

    ![Lägg till enhet](media/howto-connect-sensortile/real-device.png)

1. Att hämta anslutningsinformation för enheten **Scopeid**, **enhets-ID**, och **primärnyckel**väljer **Connect** på enhetssidan.

    ![Anslutningsinformation](media/howto-connect-sensortile/connect-device.png)

1. Anteckna anslutningsinformationen. Du är bortkopplad från internet när du förbereder enheten DevKit i nästa steg.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Konfigurera SensorTile.box med mobila program

Du lär dig hur du push-programmet inbyggd programvara på enheten i det här avsnittet. Du sedan hur du skickar enhetens data till IoT Central via ST BLE Sensor-mobilapp med hjälp av Bluetooth låg energiförbrukning (tabell)-anslutning.

1. Öppna ST BLE Sensor appen och tryck på den **skapar en ny app** knappen.

    ![Skapa app](media/howto-connect-sensortile/create-app.png)

1. Välj den **utvärdering** program.
1. Tryck på knappen ladda upp.

    ![Ladda upp utvärdering](media/howto-connect-sensortile/barometer-upload.png)

1. Tryck på knappen Spela upp som är associerade med din SensorTile.box.
1. När processen är klar, strömmas i SensorTile.box temperaturen, minnesbelastning och fuktigheten över tillgängliga.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Anslut SensorTile.box till molnet

Du lär dig hur du ansluter SensorTile.box till mobilprogrammet och ansluter det mobila programmet till molnet i det här avsnittet.

1. Den vänstra menyn, Välj den **molnet loggning** knappen.

    ![Cloud-loggning](media/howto-connect-sensortile/cloud-logging.png)

1. Välj **Azure IoT Central** som molnleverantör.
1. Infoga enhets-ID och Scope-ID som noterades tidigare.

    ![Autentiseringsuppgifter](media/howto-connect-sensortile/credentials.png)

1. Välj den **Programnyckel** alternativknappen.
1. Klicka på **Connect** och välj telemetridata som du vill ladda upp.
1. Efter några sekunder visas data på instrumentpanelen för IoT Central-program.

## <a name="sensortilebox-device-template-details"></a>Mall för SensorTile.box enhetsinformation

Ett program som har skapats med mallen SensorTile.box enhet med följande egenskaper:

### <a name="telemetry"></a>Telemetri

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| tryck       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en SensorTile.box till programmet Azure IoT Central, föreslagna nästa steg är att lära dig [hur du ställer in en anpassad enhet mall](howto-set-up-template.md) för dina egna IoT-enheter.
