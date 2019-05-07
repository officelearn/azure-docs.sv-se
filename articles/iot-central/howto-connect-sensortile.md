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
ms.openlocfilehash: 580a8baa19e8ed4fc3f4449ead9d8aedbc4c039a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160912"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Anslut SensorTile.box enhet till Azure IoT Central programmet

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta SensorTile.box enheter till Microsoft Azure IoT Central programmet.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln behöver du följande resurser:

* En SensorTile.box-enhet, se [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/SensorTile.box) för mer information.
* ST BLE Sensor appen är installerad på din Android-enhet, du kan [ladda ned den här] (https://play.google.com/store/apps/details?id=com.st.bluems). Mer information: () [ST BLE Sensor]http://www.st.com/stblesensor)
* Ett Azure IoT Central program som skapats från den **DevKits** programmall. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* Lägg till den **SensorTile.box** enheten mallen i ditt IoT Central-program genom att besöka den **enheten mallar** sidan, klickar på **+ ny**, och välja **SensorTile** mall.

### <a name="get-your-device-connection-details"></a>Hämta anslutningsinformationen för din enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **SensorTile.box** enheten mallen och gjort en notering enhetsinformation för anslutning: **Scope-ID, enhets-ID och primärnyckel**:

1. Lägg till en enhet från Device Explorer. Välj **+ Ny > verkliga** att lägga till en riktig enhet.

    * Ange en gemen **enhets-ID**, eller använda de föreslagna **enhets-ID**.
    * Ange en **enhetsnamn**, eller använda det föreslagna namnet

    ![Lägg till enhet](media/howto-connect-sensortile/real-device.png)

1. Att hämta anslutningsinformation för enheten **Scopeid**, **enhets-ID**, och **primärnyckel**väljer **Connect** på enhetssidan.

    ![Anslutningsinformation](media/howto-connect-sensortile/connect-device.png)

1. Anteckna anslutningsinformationen. Du är bortkopplad från internet när du förbereder enheten DevKit i nästa steg.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Konfigurera SensorTile.box med mobila program

I det här avsnittet får lära du dig tryck programmet inbyggd programvara på enheten och skicka enhetens data till IoT Central via ST BLE Sensor mobilapp med hjälp av Bluetooth låg energiförbrukning (tabell)-anslutning.
1. Öppna ST BLE Sensor appen och tryck på den **skapar en ny app** knappen.

    ![Skapa app](media/howto-connect-sensortile/create-app.png)

1. Välj den **utvärdering** program.
1. Tryck på knappen ladda upp.

    ![Ladda upp utvärdering](media/howto-connect-sensortile/barometer-upload.png)

1. Tryck på knappen Spela upp som är associerade med din SensorTile.box.
1. När processen är klar, kommer SensorTile.box strömma temperatur och tryck och fuktighet över tillgängliga.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Anslut SensorTile.box till molnet

I det här avsnittet kommer du lära dig hur du ansluter SensorTile.box till mobilprogrammet och ansluter det mobila programmet till molnet.
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

Nu när du har lärt dig hur du ansluter en SensorTile.box till programmet Azure IoT Central, föreslagna nästa steg är att lära dig hur du [konfigurera en anpassad enhet mall](howto-set-up-template.md) för dina egna IoT-enheter.
