---
title: Ansluta en Windows IoT Core-enhet till Azure IoT Central programmet | Microsoft Docs
description: Lär dig hur du ansluter en enhet för MXChip IoT DevKit till Azure IoT Central programmet som utvecklare enheten.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617860"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Anslut en Windows IoT Core-enhet till din Azure IoT Central program

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta en Windows IoT Core-enhet till din Microsoft Azure IoT Central program.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

- Ett Azure IoT Central program som skapats från den **exempel Devkits** mall för program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).

- En enhet som kör operativsystemet Windows 10 IoT Core. Mer information finns i [ställer in din enhet för Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- En utvecklingsdator med [Node.js](https://nodejs.org/) version 8.0.0 eller senare installerat. Du kan köra `node --version` i kommandoraden för att kontrollera vilken version. Node.js är tillgängligt för många olika operativsystem.

## <a name="the-sample-devkits-application"></a>Exemplet Devkits programmet

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Windows IoT Core** enheten mallen med följande egenskaper:

- Telemetri mätning av faktisk användning för enheten: **Fuktighet**, **temperatur**, och **tryck**.
- Inställningen för att styra **fläkthastighet**.
- En enhetsegenskap **dör nummer** och en cloud-egenskap **plats**.

Fullständig information om konfigurationen av enheten mallen finns i [Windows IoT Core-enhet mallinformationen](#device-template-details).

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I programmets Azure IoT Central använder den **Device Explorer** sidan för att lägga till en riktig enhet från den **Windows 10 IoT Core** enheten mall. Anteckna enheten anslutningsinformation (**Scopeid**, **enhets-ID**, och **primärnyckel**). Mer information finns i [anslutningsinformation](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Förbered enheten

För att enheten ansluter till IoT Central, krävs en anslutningssträng.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Koden för enheten att åtkomst till anslutningssträngen, spara den i en fil med namnet **connection.string.iothub** i mappen `C:\Data\Users\DefaultAccount\Documents\` på din Windows 10 IoT Core-enhet.

Kopiera den **connection.string.iothub** fil från din stationära dator till den `C:\Data\Users\DefaultAccount\Documents\` mapp på din enhet som du kan använda den [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Använda webbläsaren för att navigera till Windows Device Portal på din enhet.
1. För att bläddra bland filer på enheten, Välj **appar > Utforskaren**.
1. Gå till **användaren Folders\Documents**. Ladda sedan upp den **connection.string.iothub** fil:

    ![Ladda upp anslutningssträng](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Distribuera och köra

Du kan använda för att distribuera och kör exempelprogrammet på din enhet, den [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Använda webbläsaren för att navigera till Windows Device Portal på din enhet.
1. Att distribuera och köra den **Azure IoT Hub Client** program, Välj **appar > Snabbstart och kör exempel**. Välj sedan **Azure IoT Hub Client**.
1. Välj sedan **distribuera och köra**.

    ![Distribuera och köra](media/howto-connect-windowsiotcore/quick-run.png)

Du kan även efter ett par minuter visa telemetri från din enhet i ditt IoT Central-program.

Den [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) innehåller verktyg som du kan använda för att felsöka enheten:

- Den **appar manager** sidan kan du kontrollera de appar som körs på din enhet.
- Om du inte har en Övervakare som är ansluten till din enhet kan du använda den **Enhetsinställningar** sidan för att hämta skärmbilder från din enhet. Exempel:

    ![Skärmbild av App](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Ladda ned källkoden

Om du vill utforska och ändra källkoden för klientprogrammet kan du hämta det från den [Windows-iotcore-samples GitHub-lagringsplatsen](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Mallen enhetsinformation

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Windows IoT Core** enheten mallen med följande egenskaper:

### <a name="telemetry-measurements"></a>Telemetri mätning av faktisk användning

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Inledande |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Fläkthastighet    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhetsegenskap | Dör nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Gäller inte       |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Raspberry Pi till programmet Azure IoT Central, föreslagna nästa steg är att lära dig hur du [konfigurera en anpassad enhet mall](howto-set-up-template.md) för dina egna IoT-enheter.