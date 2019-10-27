---
title: Anslut en SensorTile. Box-enhet till ditt Azure IoT Central-program | Microsoft Docs
description: Som enhets utvecklare lär du dig att ansluta en SensorTile. Box-enhet till ditt Azure IoT Central-program.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 0969ee6dbc035ffa105dd54d34f3b4711d4915cf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951216"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Anslut SensorTile. Box-enhet till ditt Azure IoT Central-program

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som enhets utvecklare, för att ansluta en SensorTile. Box-enhet till ditt Microsoft Azure IoT Central-program.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln behöver du följande resurser:

* En SensorTile. Box-enhet. Mer information finns i [SensorTile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Med den ST. a-sensor appen installerad på din Android-enhet kan du [Ladda ned den här](https://play.google.com/store/apps/details?id=com.st.bluems). Mer information finns på: [S:T Bell-sensor](https://www.st.com/stblesensor)
* Ett Azure IoT Central-program som skapats från program mal len **DevKits** . Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* Lägg till enhets mal len **SensorTile. Box** i IoT Central-programmet genom att gå till sidan **mallar för enheter** , klicka på **+ ny**och välja mallen **SensorTile. Box** .

### <a name="get-your-device-connection-details"></a>Hämta information om din enhets anslutning

I ditt Azure IoT Central-program lägger du till en riktig enhet från enhets mal len **SensorTile. Box** och noterar enhetens anslutnings information: **scope-ID**, **enhets-ID**och **primär nyckel**:

1. Lägg till en enhet från enheter. Välj **+ New > Real** för att lägga till en riktig enhet.

    * Ange ett gement **enhets-ID**eller Använd det föreslagna **enhets-ID: t**.
    * Ange ett **enhets namn**eller Använd det föreslagna namnet

    ![Lägg till enhet](media/howto-connect-sensortile/real-device.png)

1. Om du vill hämta information om enhets anslutning, **scope-ID**, **enhets-ID**och **primär nyckel**väljer du **Anslut** på enhets sidan.

    ![Anslutningsinformation](media/howto-connect-sensortile/connect-device.png)

1. Anteckna anslutnings informationen. Du är tillfälligt frånkopplad från Internet när du förbereder din DevKit-enhet i nästa steg.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Konfigurera SensorTile. Box med mobil programmet

I det här avsnittet får du lära dig hur du push-överför program varans inbyggda program vara på enheten. Sedan skickar du enhets data till IoT Central via den ST. a-sensorbaserade mobilappen med hjälp av anslutning till Bluetooth låg energi (Bell).

1. Öppna appen S:T Bell-sensor och klicka på knappen **skapa en ny app** .

    ![Skapa app](media/howto-connect-sensortile/create-app.png)

1. Välj **barometer** -programmet.
1. Tryck på knappen Överför.

    ![Barometer uppladdning](media/howto-connect-sensortile/barometer-upload.png)

1. Tryck på uppspelnings knappen som är kopplad till din SensorTile. Box.
1. När processen är klar strömmar SensorTile. Box temperaturen, trycket och fuktigheten över tabell.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Anslut SensorTile. Box till molnet

I det här avsnittet får du lära dig hur du ansluter SensorTile. Box till det mobila programmet och ansluter det mobila programmet till molnet.

1. Välj knappen **moln loggning** i det vänstra fönstret.

    ![Moln loggning](media/howto-connect-sensortile/cloud-logging.png)

1. Välj **Azure-IoT Central** som moln leverantör.
1. Ange enhets-ID och omfångs-ID som tidigare nämnts.

    ![Autentiseringsuppgifter](media/howto-connect-sensortile/credentials.png)

1. Välj alternativ knappen **program nyckel** .
1. Klicka på **Anslut** och välj de telemetridata som du vill ladda upp.
1. Efter några sekunder visas data på instrument panelen för IoT Central-programmet.

## <a name="sensortilebox-device-template-details"></a>Information om enhets mal len för SensorTile. Box

Ett program som skapats från enhets mal len SensorTile. Box med följande egenskaper:

### <a name="telemetry"></a>Telemetri

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 30       | 90     | 1              |
| styr           | C     | 0     | 40     | 1              |
| tryck       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1 000    | 0              |
| accelerometerX | MB     | – 2000   | 2000    | 0              |
| accelerometerY | MB     | – 2000   | 2000    | 0              |
| accelerometerZ | MB     | – 2000   | 2000    | 0              |
| gyroscopeX     | –   | – 3276   | 3276    | 1              |
| gyroscopeY     | –   | – 3276   | 3276    | 1              |
| gyroscopeZ     | –   | – 3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en SensorTile. Box till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig [hur du konfigurerar en anpassad enhets mall](howto-set-up-template.md) för din egen IoT-enhet.
