---
title: Anslut Windows IoT Core till ditt Azure IoT Central-program | Microsoft Docs
description: Som enhets utvecklare lär du dig att ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 43e99c54249738436f24369ed3525e78ff971a12
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930211"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Ansluta en Windows IoT Core-enhet till ditt Azure IoT Central-program

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som enhets utvecklare, för att ansluta en Windows IoT Core-enhet till din Microsoft Azure IoT Central program.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

- Ett Azure IoT Central-program som skapats från programmallen **exempel Devkits** . Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).

- En enhet som kör operativ systemet Windows 10 IoT Core. Mer information finns i [Konfigurera din Windows 10 IoT Core-enhet](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- En utvecklings dator med [Node. js](https://nodejs.org/) version 8.0.0 eller senare installerad. Du kan kontrol lera din version genom att köra `node --version` på kommando raden. Node.js är tillgängligt för många olika operativsystem.

## <a name="the-sample-devkits-application"></a>Devkits-programmet för exempel

Ett program som har skapats från **Devkits** program mal len innehåller en mall för **Windows IoT Core** -enheter med följande egenskaper:

- Mått för telemetri för enheten: **fuktighet**, **temperatur**och **tryck**.
- Inställning för att kontrol lera **fläkt hastigheten**.
- Ett **tärnings nummer** för enhets egenskapen och en **plats**för moln egenskaper.

Fullständig information om konfigurationen av enhets mal len finns i [information om Windows IoT Core-enhetens mall](#device-template-details).

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program använder du sidan **Device Explorer** för att lägga till en riktig enhet från mallen **Windows 10 IoT Core** Device. Anteckna enhetens anslutnings information (**scope-ID**, **enhets-ID**och **primär nyckel**).

## <a name="prepare-the-device"></a>Förbered enheten

För att enheten ska kunna ansluta till IoT Central krävs en anslutnings sträng:

1. Använd `dps-keygen` kommando rads verktyg för att skapa en anslutnings sträng:

    Kör följande kommando för att installera [verktyget för nyckel Generator](https://github.com/Azure/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Skapa en anslutnings sträng genom att köra följande kommando med hjälp av anslutnings informationen som du antecknade tidigare:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Kopiera anslutnings strängen från `dps-keygen` utdata som ska användas i enhets koden.

För enhets koden för att få åtkomst till anslutnings strängen sparar du den i en fil med namnet **Connection. String. iothub** i mappen `C:\Data\Users\DefaultAccount\Documents\` på din Windows 10 IoT Core-enhet.

Om du vill kopiera filen **Connection. String. iothub** från din station ära dator till mappen `C:\Data\Users\DefaultAccount\Documents\` på enheten kan du använda [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Använd webbläsaren för att navigera till Windows Device Portal på din enhet.
1. Om du vill bläddra bland filerna på enheten väljer du **appar >** Utforskaren.
1. Navigera till **User Folders\Documents**. Ladda sedan upp filen **Connection. String. iothub** :

    ![Ladda upp anslutnings sträng](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Distribuera och köra

Om du vill distribuera och köra exempel programmet på enheten kan du använda [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Använd webbläsaren för att navigera till Windows Device Portal på din enhet.
1. Om du vill distribuera och köra **klient programmet Azure IoT Hub** väljer du **appar > snabb körnings exempel**. Välj sedan **Azure IoT Hub-klienten**.
1. Välj sedan **distribuera och kör**.

    ![Distribuera och köra](media/howto-connect-windowsiotcore/quick-run.png)

Efter ett par minuter kan du Visa Telemetrin från enheten i ditt IoT Central program.

[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) innehåller verktyg som du kan använda för att felsöka enheten:

- På sidan **Apps Manager** kan du styra vilka appar som körs på enheten.
- Om du inte har en övervakare ansluten till din enhet kan du använda sidan **enhets inställningar** för att avbilda skärm bilder från enheten. Exempel:

    ![Bild skärm för app](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Ladda ned käll koden

Om du vill utforska och ändra käll koden för klient programmet kan du hämta den från [Windows-iotcore-samples GitHub-lagringsplatsen](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Information om enhets mal len

Ett program som har skapats från **Devkits** program mal len innehåller en mall för **Windows IoT Core** -enheter med följande egenskaper:

### <a name="telemetry-measurements"></a>Mått för telemetri

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| styr           | C     | – 40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Grund |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Fläkt hastighet    | fanSpeed   | VARVTAL   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhets egenskap | Tärnings nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Saknas       |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Windows IoT Core-enhet till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [konfigurerar en anpassad enhets mall](howto-set-up-template.md) för din egen IoT-enhet.
