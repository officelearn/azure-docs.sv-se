---
title: Koppla de olika processtegen en hallon Pi till dina Azure IoT centralt program (Python) | Microsoft Docs
description: Som en enhet utvecklare, hur du ansluter en hallon Pi till dina Azure IoT centralt program använder Python.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 23ab31d6d2357bfcb184e5b3022155bef5ace658
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Ansluta en hallon Pi till dina Azure IoT centralt program (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Den här artikeln beskriver hur som utvecklare som enheten kan ansluta en hallon Pi till ditt Microsoft Azure IoT Central program använder Python programmeringsspråket.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT centrala program som skapas från den **exempel Devkits** mall för program. Mer information finns i [skapa Azure IoT centrala programmet](howto-create-application.md).
* En hallon Pi-enhet som kör operativsystemet Raspbian. Du måste bildskärm, tangentbord och mus som är anslutna till din hallon Pi för att få åtkomst till GUI-miljö. Hallon Pi måste kunna [ansluta till internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Du kan också en [mening Hat](https://www.raspberrypi.org/products/sense-hat/) tillägg board för hallon Pi. Det här kortet samlar in telemetridata från olika sensorer ska skickas till din Azure IoT centralt program. Om du inte har en **mening Hat** ändringar, kan du använda en emulator i stället.

Ett program som skapas från den **exempel Devkits** programmall innehåller en **hallon Pi** enheten mallen med följande egenskaper:

### <a name="telemetry-measurements"></a>Telemetri mått

| Fältnamn     | Enheter  | Minimum | Maximal | Decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| fuktighet       | %      | 0       | 100     | 0              |
| Temp           | ° C     | -40     | 120     | 0              |
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

| Visningsnamn | Fältnamn | Enheter | Decimaler | Minimum | Maximal | Inledande |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Spänning      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkthastighet    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Visa/Dölj inställningar

| Visningsnamn | Fältnamn | På text | Av text | Inledande |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | AV      | Av     |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhetsegenskap | Dör nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Gäller inte       |

### <a name="add-a-real-device"></a>Lägga till en verklig enhet

Azure IoT centrala programmet, lägga till en verklig enhet från den **hallon Pi** enheten mallen och gjort en notering om anslutningssträngen för enheten. Mer information finns i [lägger till en verklig enhet tillämpningsprogrammet Azure IoT Central](tutorial-add-device.md).

## <a name="configure-the-raspberry-pi"></a>Konfigurera Raspberry Pi

Följande steg beskriver hur du hämtar och konfigurera Python exempelprogrammet från GitHub. Det här exempelprogrammet:

* Skickar telemetri och egenskapsvärden till Azure IoT Central.
* Ange ändringar som gjorts i Azure IoT Central svarar.

> [!NOTE]
> Mer information om de hallon Pi Python-exempel finns i [viktigt](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) fil på GitHub.

1. Använd webbläsaren hallon Pi skrivbordet för att navigera till den [Azure IoT centrala firmware släpper](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) sidan.

1. Hämta zip-filen som innehåller den senaste inbyggda programvaran till arbetsmappen på hallon Pi. Filnamnet som ser ut som `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Om du vill packa upp filen med inbyggd programvara, Använd den **Filhanteraren** hallon Pi skrivbordet. Högerklicka på zip-filen och välj **extrahera här**. Den här åtgärden skapar en mapp med namnet `RaspberryPi-IoTCentral-X.X.X` i arbetsmappen.

1. Om du inte har en **mening Hat** kort som är ansluten till din hallon Pi, måste du aktivera emulatorn:
    1. I **Filhanteraren**i den `RaspberryPi-IoTCentral-X.X.X` mappen, högerklicka på den **config.iot** filen och välj **textredigerare**.
    1. Ändra raden `"simulateSenseHat": false,` till `"simulateSenseHat": true,`.
    1. Spara ändringarna och Stäng **textredigerare**.

1. Starta en **Terminal** session och använda den `cd` kommando för att navigera till mappen du skapade i föregående steg.

1. Du kan starta exempelprogrammet kör, skriva `./start.sh` i den **Terminal** fönster. Om du använder den **mening HAT emulatorn**, visar dess GUI. Du kan använda det grafiska Användargränssnittet för att ändra telemetri-värden skickas till din Azure IoT centralt program.

1. Den **Terminal** visas ett meddelande som ser ut som i fönstret `Device information being served at http://192.168.0.60:8080`. URL: en kan vara olika i din miljö. Kopiera Webbadressen och använder webbläsaren för att navigera till sidan för konfiguration:

    ![Konfigurera enhet](media/howto-connect-raspberry-pi-python/configure.png)

1. Ange anslutningssträng för enheten som du antecknade när du har lagt till en verklig enhet till din Azure IoT centralt program. Välj **konfigurera enhet**. Du ser ett meddelande **enheten konfigurerad, enheten ska börja skicka data till Azure IoT Central tillfälligt**.

1. I Azure IoT centrala programmet se du hur koden körs på hallon Pi interagerar med programmet:

    * På den **mätningar** sidan för enheten verkliga visas telemetri som skickas från hallon Pi. Om du använder den **mening HAT emulatorn**, du kan ändra telemetri värdena i det grafiska Användargränssnittet på hallon Pi.
    * På den **egenskaper** sida, ser du värdet för den rapporterade **dör nummer** egenskapen.
    * På den **inställningar** kan du ändra olika inställningar på hallon Pi, till exempel spänning och fläkt hastighet. När du har bekräftat ändringen hallon Pi, inställningen visas som **synkroniseras** i Azure IoT Central.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en hallon Pi till dina Azure IoT centralt program, är här de föreslagna nästa steg:

* [Ansluta ett allmänt Node.js-klientprogram till Azure IoT Central](howto-connect-nodejs.md)