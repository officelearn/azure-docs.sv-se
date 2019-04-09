---
title: Ansluta en Raspberry Pi till Azure IoT Central programmet (Python) | Microsoft Docs
description: Som utvecklare av enheten, hur du ansluter en Raspberry Pi till ditt Azure IoT Central-program med Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 6ac16651e2d49dd903ff994b18a8f571bd92fbf6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272367"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Ansluta en Raspberry Pi till Azure IoT Central programmet (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta en Raspberry Pi till ditt Microsoft Azure IoT Central program med hjälp av Python som programmeringsspråk.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln, måste följande komponenter:

* Ett Azure IoT Central program som skapats från den **exempel Devkits** mall för program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En Raspberry Pi-enhet som kör operativsystemet Raspbian. Raspberry Pi måste kunna ansluta till internet. Mer information finns i [konfigurerar Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Exempel på Devkits** program

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Raspberry Pi** enheten mallen med följande egenskaper:

- Telemetri, vilket inkluderar följande mått som samlar in enheten:
  - Fuktighet
  - Temperatur
  - Tryck
  - Magnetometer (X, Y, Z)
  - Accelerometer (X, Y, Z)
  - Gyroskop (X, Y, Z)
- Inställningar
  - Spänning
  - Aktuell
  - Fläkthastighet
  - IR växlingsknappen.
- Egenskaper
  - Dör nummer enhetsegenskap
  - Platsegenskapen för molnet

Fullständig information om konfigurationen av enheten mallen finns i [Raspberry Pi enhetsinformation mallen](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **Raspberry Pi** enheten mall. Anteckna enheten anslutningsinformation (**Scopeid**, **enhets-ID**, och **primärnyckel**). Mer information finns i [ge en riktig enhet till Azure IoT Central programmet](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurera Raspberry Pi

Följande steg beskriver hur du hämtar och konfigurerar du exempelprogrammet Python från GitHub. Det här exempelprogrammet:

* Skickar telemetri och egenskapsvärden till Azure IoT Central.
* Svarar på inställningen ändringar som gjorts i Azure IoT Central.

Konfigurera enheten, [följa de stegvisa anvisningarna på GitHub](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. När enheten har konfigurerats startar enheten skickar telemetri mätning av faktisk användning till Azure IoT Central.
1. Du kan se hur koden körs på Raspberry Pi interagerar med programmet i Azure IoT Central programmet:

    * På den **mätningar** sidan för din riktig enhet visas telemetri som skickas från Raspberry Pi.
    * På den **inställningar** kan du kan ändra inställningarna på Raspberry Pi, till exempel spänning och fläkt hastighet. När Raspberry Pi om ändringen, inställningen visas som **synkroniserats**.

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi mall enhetsinformation

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Raspberry Pi** enheten mallen med följande egenskaper:

### <a name="telemetry-measurements"></a>Telemetri mätning av faktisk användning

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Inledande |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Spänning      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkthastighet    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Visa/Dölj inställningar

| Visningsnamn | Fältnamn | Text | Av text | Inledande |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | AV      | Av     |

### <a name="properties"></a>Egenskaper

| Type            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhetsegenskap | Dör nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Gäller inte       |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Raspberry Pi till programmet Azure IoT Central, är här nästa föreslagna steg:

* [Ansluta en allmän Node.js-klientprogram till Azure IoT Central](howto-connect-nodejs.md)
