---
title: Ansluta en Raspberry Pi till ditt Azure IoT Central-program (python) | Microsoft Docs
description: Som enhets utvecklare kan du ansluta en Raspberry Pi till ditt Azure IoT Central-program med python.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3daa567a916bd0abeb407028c7d06bd1f2bd464b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454076"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Ansluta en Raspberry Pi till ditt Azure IoT Central-program (python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som enhets utvecklare, för att ansluta en Raspberry Pi till ditt Microsoft Azure IoT Central-program med hjälp av python-programmeringsspråket.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln behöver du följande komponenter:

* Ett Azure IoT Central-program som skapats från program mal len **äldre program** . Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En Raspberry Pi-enhet som kör operativ systemet Raspbian. Raspberry Pi måste kunna ansluta till Internet. Mer information finns i [Konfigurera din Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Om du vill veta mer om hur du konfigurerar och ansluter till en Raspberry Pi-enhet går du till [komma igång med Raspberry Pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

## <a name="add-a-device-template"></a>Lägga till en enhetsmall

I ditt Azure IoT Central-program lägger du till en ny **Raspberry Pi** -enhets mall med följande egenskaper:

- Telemetri, som omfattar följande mätningar som samlas in av enheten:
  - Fuktighet
  - Temperatur
  - Tryck
  - Magnetometer (X, Y, Z)
  - Accelerometer (X, Y, Z)
  - Gyroscope (X, Y, Z)
- Inställningar
  - Strömförsörjning
  - Aktuell
  - Fläkt hastighet
  - IR-växling.
- Egenskaper
  - Enhets egenskap för Die Number
  - Plats moln egenskap

1. Välj **+ nytt** från mallar ![enhets mal len](media/howto-connect-raspberry-pi-python/adddevicetemplate.png)
   

2. Välj **Raspberry Pi** och skapa Raspberry Pi-enhet mal len ![lägga till enhets mal len](media/howto-connect-raspberry-pi-python/newdevicetemplate.png)

Fullständig information om hur du konfigurerar enhets mal len finns i [Raspberry Pi Device Template information](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet från enhets mal len **Raspberry Pi** . Anteckna enhetens anslutnings information (**scope-ID**, **enhets-ID**och **primär nyckel**). Mer information finns i [lägga till en riktig enhet till ditt Azure IoT Central-program](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurera Raspberry Pi

Följande steg beskriver hur du hämtar och konfigurerar exempel på python-program från GitHub. Det här exempel programmet:

* Skickar telemetri-och egenskaps värden till Azure IoT Central.
* Svarar på att ställa in ändringar som gjorts i Azure IoT Central.

1. Anslut till en gränssnitts miljö på din Raspberry Pi, antingen från Raspberry Pi Desktop eller via fjärr anslutning med SSH.

1. Kör följande kommando för att installera IoT Central python-klienten:

    ```bash
    pip install iotc
    ```

1. Ladda ned python-exempel koden:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Redigera `app.py`-filen som du laddade ned och ersätt `DEVICE_ID`, `SCOPE_ID`och `PRIMARY/SECONDARY device KEY` plats hållarna med anslutnings värden som du antecknade tidigare. Spara ändringarna.

    > [!TIP]
    > I Shell på Raspberry Pi kan du använda antingen **nano** -eller **vi** -text redigerare.

1. Använd följande kommando för att köra exemplet:

    ```bash
    python app.py
    ```

    Raspberry Pi börjar skicka telemetri mått till Azure IoT Central.

1. I ditt Azure IoT Central-program kan du se hur koden som körs på Raspberry Pi interagerar med programmet:

    * På sidan **mått** för din riktiga enhet kan du se Telemetrin som skickas från Raspberry Pi.
    * På sidan **Egenskaper** kan du se enhets egenskapen för **Die Number** .
    * På sidan **Inställningar** kan du ändra inställningarna för Raspberry Pi, till exempel spänning och fläkt hastighet. När Raspberry Pi bekräftar ändringen visas inställningen som **synkroniserad**.

## <a name="raspberry-pi-device-template-details"></a>Information om Raspberry Pi-enhet

Ett program som har skapats från **Devkits** program mal len innehåller en **Raspberry Pi** -enhet med följande egenskaper:

### <a name="telemetry-measurements"></a>Mått för telemetri

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| styr           | °C     | -40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1 000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Initialt |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Strömförsörjning      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkt hastighet    | fanSpeed   | RPM   | 0              | 0       | 1 000    | 0       |

Växla inställningar

| Visningsnamn | Fältnamn | På text | Av text | Initialt |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Av     |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhets egenskap | Tärnings nummer   | dieNumber  | nummer    |
| Text            | Location     | location   | Gäller inte       |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Raspberry Pi till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [konfigurerar en anpassad enhets mall](howto-set-up-template.md) för din egen IoT-enhet.
