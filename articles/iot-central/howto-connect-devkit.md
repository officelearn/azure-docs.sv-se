---
title: Ansluter en DevKit enhet till din Azure IoT centralt program | Microsoft Docs
description: Som en enhet utvecklare lär du dig hur du ansluter en MXChip IoT DevKit enhet till din Azure IoT centralt program.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d7b92359e8875c281fd460f1f5307a7941c11c1f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261584"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Anslut en MXChip IoT DevKit enhet till din Azure IoT centralt program

Den här artikeln beskriver hur som utvecklare som enheten kan du ansluta en MXChip IoT DevKit (DevKit) till Microsoft Azure IoT Central programmet.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT centrala program som skapas från den **exempel Devkits** mall för program. Mer information finns i [skapa Azure IoT centrala programmet](howto-create-application.md).
1. En DevKit enhet. Du kan köpa en DevKit enhet [MXChip IoT DevKit](http://mxchip.com/az3166).

Ett program som skapas från den **exempel Devkits** programmall innehåller en **MXChip** enheten mallen med följande egenskaper:

### <a name="measurements"></a>Mått

#### <a name="telemetry"></a>Telemetri 

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
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

#### <a name="states"></a>Tillstånd 

| Namn          | Visningsnamn   | NORMAL | VARNING | RISK | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Enhetens tillstånd   | Grön  | Orange  | Röd    | 

#### <a name="events"></a>Händelser 

| Namn             | Visningsnamn      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Knappen B aktiverad  | 



### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Inledande |
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


### <a name="add-a-real-device"></a>Lägga till en riktig enhet

Azure IoT centrala programmet, lägga till en verklig enhet från den **MXChip** enheten mallen och gjort en notering om anslutningssträngen för enheten. Mer information finns i [lägger till en verklig enhet tillämpningsprogrammet Azure IoT Central](tutorial-add-device.md).

## <a name="prepare-the-devkit-device"></a>Förbered enheten DevKit

> [!TIP]
> DevKit enheten felsökningsanvisningar finns [IoT DevKit Kom igång](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/).

Förbereda enheten DevKit:

1. Ladda ned den senaste förskapad Azure IoT centrala inbyggda programvaran för MXChip från den [släpper](https://github.com/Azure/iot-central-firmware/releases) sidan på GitHub. Hämta filnamn på sidan versioner ser ut som `AZ3166-IoT-Central-X.X.X.bin`.

1. Ansluta enheten DevKit till utvecklingsdatorn via USB-kabel. I Windows öppnas en fil explorer på en enhet som är mappad till lagring på DevKit enheten. Till exempel enheten kan anropas **AZ3166 (D:)**.

1. Dra den **iotCentral.bin** fil till fönstret enhet. När kopieringen är klar startar om enheten med den nya inbyggda programvaran.

1. När DevKit enheten startas om, visas följande skärm:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Om skärmen visar något annat, trycker du på den **återställa** knappen på enheten. 

1. Enheten är nu i åtkomstläge åtkomstpunkten (AP). Du kan ansluta till den här WiFi-åtkomstpunkt från din dator eller mobil enhet.

1. På datorn ansluta telefonen eller surfplattan till Wi-Fi-nätverksnamn som visas på skärmen för enheten. När du ansluter till det här nätverket har inte tillgång till internet. Det här tillståndet är förväntat och du bara är ansluten till nätverket under en kort period när du konfigurerar enheten.

1. Öppna webbläsaren och gå till [ http://192.168.0.1/start ](http://192.168.0.1/start). I följande webbsida visas:

    ![Konfigurationssidan för enheten](media/howto-connect-devkit/configpage.png)

    På webbsidan: 
    - Lägg till namnet på din Wi-Fi-nätverk 
    - lösenordet för Wi-Fi-nätverk 
    - PIN-koden som visas på enheten LCD 
    - anslutningssträngen för enheten. 
      Du kan hitta anslutningssträngen @ `https://apps.iotcentral.com`  ->  `Device Explorer`  ->  `Device`  ->  `Select or Create a new Real Device`  ->  `Connect this device` (på upp till höger) 
    - Välj all tillgänglig telemetri mått! 

1. När du har valt **konfigurera enhet**, du kan se den här sidan:

    ![Enheter som har konfigurerats](media/howto-connect-devkit/deviceconfigured.png)

1. Tryck på den **återställa** knappen på enheten.

> [!NOTE]
> Om du vill konfigurera om enheten för att använda ett annat Wi-Fi-nätverk, anslutningssträngen eller telemetri mätning trycker du på både den **A** och **B** knappar på kortet samtidigt. Om det inte fungerar trycker du på **återställa** knappen och försök igen. 

## <a name="view-the-telemetry"></a>Visa telemetrin

När DevKit enheten startas om visas skärmen på enheten:

* Antal telemetri meddelanden som skickas.
* Antal fel.
* Antalet önskade egenskaper som tagits emot och antalet rapporterade egenskaper som skickas.

Skaka enheten öka antalet rapporterade egenskaper som skickas. Enheten skickar ett slumptal som den **dör nummer** enhetsegenskap.

Du kan visa telemetri mått och rapporterade egenskapsvärden och konfigurera inställningarna i Azure IoT Central:

1. Använd **enheten Explorer** att navigera till den **mätningar** för den faktiska MXChip enheten som du har lagt till:

    ![Navigera till verkliga enhet](media/howto-connect-devkit/realdevice.png)

1. På den **mätningar** sidan som du kan se telemetri som kommer från MXChip enhet:

    ![Visa telemetri från verkliga enhet](media/howto-connect-devkit/realtelemetry.png)

1. På den **egenskaper** kan du visa den senaste nummer som rapporterar:

    ![Visa egenskaper för enhet](media/howto-connect-devkit/deviceproperties.png)

1. På den **inställningar** kan du uppdatera inställningarna på enheten MXChip:

    ![Visa inställningar för enheter](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>Ladda ned källkoden

Om du vill utforska och ändra koden för enheten kan du ladda ned det från GitHub. Om du planerar att ändra koden, ska du följa instruktionerna [förbereda utvecklingsmiljön](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) för ditt operativsystem.

Ladda ned källkoden, kör du följande kommando på den stationära datorn:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Föregående kommando hämtar källkoden till en mapp med namnet `iot-central-firmware`. 

> [!NOTE]
> Om **git** har inte installerats i din utvecklingsmiljö kan du ladda ned det från [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Granska koden

Använd Visual Studio-koden som installerades när du förberedde din utvecklingsmiljö för att öppna den `AZ3166` mapp i den `iot-central-firmware` mapp: 

![Visual Studio-koden](media/howto-connect-devkit/vscodeview.png)

Om du vill se hur telemetrin skickas till programmet Azure IoT Central, öppna den **main_telemetry.cpp** filen i mappen källa.

Funktionen `buildTelemetryPayload` skapar JSON telemetri-nyttolast med data från sensorerna på enheten.

Funktionen `sendTelemetryPayload` anrop `sendTelemetry` i den **iotHubClient.cpp** skicka JSON-nyttolast till IoT-hubben Azure IoT centrala programmet använder.

Om du vill se hur egenskapsvärden rapporteras programmet Azure IoT Central, öppna den **main_telemetry.cpp** filen i mappen källa.

Funktionen `telemetryLoop` skickar den **doubleTap** rapporterade egenskapen när accelerationsmätare identifierar dubbla knacka. Den använder den `sendReportedProperty` fungera i den **iotHubClient.cpp** källfilen.

Koden i den **iotHubClient.cpp** källfilen använder funktioner från den [ Microsoft Azure IoT-SDK: er och bibliotek för C](https://github.com/Azure/azure-iot-sdk-c) att interagera med IoT-hubben.

Information om hur du ändrar, skapa och ladda upp exempelkoden till din enhet finns i **readme.md** filen i den `AZ3166` mapp.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT centralt program, är här de föreslagna nästa steg:

* [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)