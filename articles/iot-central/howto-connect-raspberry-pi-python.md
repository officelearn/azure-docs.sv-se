---
title: Connnect en Raspberry Pi till Azure IoT Central programmet (Python) | Microsoft Docs
description: Som utvecklare av enheten, hur du ansluter en Raspberry Pi till ditt Azure IoT Central-program med Python.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: a3d6ad9f2f442481908bc02252fcc8ab1a74419e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205596"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Ansluta en Raspberry Pi till Azure IoT Central programmet (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta en Raspberry Pi till ditt Microsoft Azure IoT Central program med hjälp av Python som programmeringsspråk.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central program som skapats från den **exempel Devkits** mall för program. Mer information finns i [skapa Azure IoT Central programmet](howto-create-application.md).
* En Raspberry Pi-enhet som kör operativsystemet Raspbian. Du behöver en bildskärm, tangentbord och mus som är anslutna till Raspberry Pi till GUI-miljö. Raspberry Pi måste kunna [ansluta till internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Du kan också en [mening Hat](https://www.raspberrypi.org/products/sense-hat/) tillägg tavla för Raspberry Pi. Den här tavla samlar in telemetridata från olika sensorer för att skicka till din Azure IoT Central-App. Om du inte har en **mening Hat** tavla, du kan använda en emulator i stället.

## <a name="sample-devkits-application"></a>**Exempel på Devkits** program

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Raspberry Pi** enheten mallen med följande egenskaper: 

- Telemetri som innehåller mätningarna för enheten **fuktighet**, **temperatur**, **tryck**, **Magnometer** (mätt längs X Y, Z-axeln), **Accelorometer** (mätt längs X, Y, Z-axeln) och **gyroskop** (mätt längs X, Y, Z-axeln).
- Inställningar som visar **Voltage**, **aktuella**,**fläkthastighet** och en **IR** växlingsknappen.
- Egenskaper som innehåller enhetsegenskap **dör nummer** och **plats** egenskap i molnet.


Fullständig information om konfigurationen av enheten mallen finns i [Raspberry PI enhetsinformation mall](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **Raspberry Pi** enheten mallen och gjort en notering enhetens anslutningssträng. Mer information finns i [ge en riktig enhet till Azure IoT Central programmet](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurera Raspberry Pi

Följande steg beskriver hur du hämtar och konfigurerar du exempelprogrammet Python från GitHub. Det här exempelprogrammet:

* Skickar telemetri och egenskapsvärden till Azure IoT Central.
* Svarar på inställningen ändringar som gjorts i Azure IoT Central.

> [!NOTE]
> Mer information om de Raspberry Pi Python-exempel finns i den [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) fil på GitHub.

1. Använda webbläsaren i desktop Raspberry Pi för att navigera till den [Azure IoT Central firmware släpper](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) sidan.

1. Ladda ned zip-filen som innehåller den senaste inbyggda programvaran arbetsmappen på Raspberry Pi. Namnet på filen som ser ut som `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Om du vill packa upp filen inbyggd programvara, använda den **hanteraren** i desktop Raspberry Pi. Högerklicka på zip-filen och välj **extrahera här**. Den här åtgärden skapar en mapp med namnet `RaspberryPi-IoTCentral-X.X.X` i arbetsmappen.

1. Om du inte har en **mening Hat** tavlan som är kopplade till Raspberry Pi, måste du aktivera emulatorn:
    1. I **hanteraren**i den `RaspberryPi-IoTCentral-X.X.X` mapp, högerklickar du på den **config.iot** filen och välj **textredigerare**.
    1. Ändrar du raden `"simulateSenseHat": false,` till `"simulateSenseHat": true,`.
    1. Spara ändringarna och Stäng **textredigerare**.

1. Starta en **Terminal** session och använda den `cd` kommando för att navigera till mappen du skapade i föregående steg.

1. Om du vill starta exempelprogrammet körs skriver `./start.sh` i den **Terminal** fönster. Om du använder den **mening HAT emulatorn**, visar dess GUI. Du kan använda det grafiska Användargränssnittet för att ändra värdena för telemetri som skickas till Azure IoT Central programmet.

1. Den **Terminal** visas ett meddelande som ser ut som i fönstret `Device information being served at http://192.168.0.60:8080`. URL: en kan skilja sig i din miljö. Kopiera URL: en och använda webbläsaren för att navigera till konfigurationssidan:

    ![Konfigurera enhet](media/howto-connect-raspberry-pi-python/configure.png)

1. Ange enhetens anslutningssträng som du antecknade när du har lagt till en riktig enhet till Azure IoT Central programmet. Välj sedan **– konfigurera enhet**. Du ser ett meddelande **enheten konfigurerad, enheten ska börja skicka data till Azure IoT Central tillfälligt**.

1. I Azure IoT Central programmet se du hur koden körs på Raspberry Pi interagerar med programmet:

    * På den **mätningar** sidan för din riktig enhet visas telemetri som skickas från Raspberry Pi. Om du använder den **mening HAT emulatorn**, du kan ändra telemetrivärden i det grafiska Användargränssnittet på Raspberry Pi.
    * På den **egenskaper** sidan ser du värdet för det rapporterade **dör nummer** egenskapen.
    * På den **inställningar** kan du kan ändra olika inställningar på Raspberry Pi, till exempel spänning och fläkt hastighet. När Raspberry Pi om ändringen, inställningen visas som **synkroniserats** i Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Raspberry PI mall enhetsinformation

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Raspberry Pi** enheten mallen med följande egenskaper:

### <a name="telemetry-measurements"></a>Telemetri mätning av faktisk användning

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| Temp           | ° C     | -40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | där-1 000   | 1000    | 0              |
| magnetometerY  | mgauss | där-1 000   | 1000    | 0              |
| magnetometerZ  | mgauss | där-1 000   | 1000    | 0              |
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

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhetsegenskap | Dör nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Gäller inte       |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Raspberry Pi till programmet Azure IoT Central, är här nästa föreslagna steg:

* [Ansluta en allmän Node.js-klientprogram till Azure IoT Central](howto-connect-nodejs.md)
