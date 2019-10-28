---
title: Ansluta en DevKit-enhet till ditt Azure IoT Central-program | Microsoft Docs
description: Som enhets utvecklare lär du dig att ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 24ce6de63d06402e24abdeb33996a3853175ce91
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954414"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som en enhets utvecklare, för att ansluta en DevKit-enhet (MXChip IoT DevKit) till Microsoft Azure IoT Central programmet.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln behöver du följande resurser:

1. Ett Azure IoT Central-program som skapats från programmallen **exempel Devkits** . Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
1. En DevKit-enhet. Om du vill köpa en DevKit-enhet går du till [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Exempel på Devkits-program

Ett program som har skapats från **Devkits** program mal len innehåller en **MXChip** som definierar följande enhets egenskaper:

- Mätningar för mätning av **fuktighet**, **temperatur**, **tryck**, **magnetometer** (uppmätt utmed x, y, z-axeln), **accelerometer** (mäts utmed x, y, z-axeln) och **Gyroscope** (mäts längs x, y, z-axeln).
- Tillstånds mått för **enhets tillstånd**.
- Händelse mätning för **knapp B nedtryckt**.
- Inställningar för **spänning**, **aktuell**, **fläkt hastighet**och en **IR** -växling.
- Enhets egenskaper **chips nummer** och **enhets plats**, vilket är en plats egenskap.
- Moln egenskap **tillverkad i**.
- Kommandon för **eko** och **nedräkning**. När en riktig enhet tar emot ett **eko** kommando, visas det skickade värdet på enhetens skärm. När en riktig enhet tar emot ett **nedräknings** kommando, växlar indikatorn genom ett mönster och enheten skickar nedräknings värden till IoT Central.

Fullständig information om konfigurationen finns i [MXChip information om enhets mal len](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

### <a name="get-your-device-connection-details"></a>Hämta information om din enhets anslutning

I ditt Azure IoT Central-program lägger du till en riktig enhet från **MXChip** enhets mal len och noterar enhetens anslutnings information: **scope-ID, enhets-ID och primär nyckel**:

1. Lägg till en **riktig enhet** från Device Explorer och välj **+ Ny > Real** för att lägga till en riktig enhet.

    * Ange ett gement **enhets-ID**eller Använd det föreslagna **enhets-ID: t**.
    * Ange ett **enhets namn**eller Använd det föreslagna namnet

    ![Lägg till enhet](media/howto-connect-devkit/add-device.png)

1. Om du vill hämta information om enhets anslutning, **scope-ID**, **enhets-ID**och **primär nyckel**väljer du **Anslut** på enhets sidan.

    ![Anslutningsinformation](media/howto-connect-devkit/device-connect.png)

1. Anteckna anslutnings informationen. Du är tillfälligt frånkopplad från Internet när du förbereder din DevKit-enhet i nästa steg.

### <a name="prepare-the-devkit-device"></a>Förbereda DevKit-enheten

Om du tidigare har använt enheten och vill konfigurera om den för att använda en annan WiFi-nätverk, anslutnings sträng eller telemetri, trycker du på knapparna **a** och **B** samtidigt. Om den inte fungerar trycker du på knappen **Återställ** och försöker igen.

#### <a name="to-prepare-the-devkit-device"></a>Så här förbereder du DevKit-enheten

1. Ladda ned den senaste inbyggda Azure IoT Central-programvaran för MXChip från sidan [versioner](https://aka.ms/iotcentral-docs-MXChip-releases) på GitHub.
1. Anslut DevKit-enheten till utvecklings datorn med en USB-kabel. I Windows öppnas ett Utforskaren-fönster på en enhet som är mappad till lagringen på DevKit-enheten. Enheten kan till exempel kallas **AZ3166 (D:)** .
1. Dra filen **iotCentral. bin** till enhets fönstret. När kopieringen är klar startas enheten om med den nya inbyggda program varan.

1. När DevKit-enheten startar om visas följande skärm bild:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Om skärmen visar något annat återställer du enheten och trycker på knapparna **A** och **B** på enheten samtidigt för att starta om enheten.

1. Enheten är nu i AP-läge (Access Point). Du kan ansluta till den här WiFi-åtkomst punkten från din dator eller mobila enhet.

1. På din dator, telefon eller surfplatta ansluter du till det WiFi-nätverks namn som visas på enhetens skärm. När du ansluter till det här nätverket har du inte till gång till Internet. Detta tillstånd förväntas och du är bara ansluten till det här nätverket under en kort tid medan du konfigurerar enheten.

1. Öppna webbläsaren och gå till [http://192.168.0.1/start](http://192.168.0.1/start). Följande webb sida visas:

    ![Sidan enhets konfiguration](media/howto-connect-devkit/configpage.png)

    På webb sidan anger du:
    - Namnet på ditt WiFi-nätverk
    - Lösen ordet för WiFi-nätverket
    - PIN-koden som visas på enhetens skärm
    - Anslutnings informationens **omfattnings-ID**, **enhets-ID**och **primär nyckel** (du bör redan ha sparat följande steg)
    - Välj alla tillgängliga mått för telemetri

1. När du har valt **Konfigurera enhet**visas den här sidan:

    ![Enhet konfigurerad](media/howto-connect-devkit/deviceconfigured.png)

1. Tryck på knappen **Återställ** på enheten.

## <a name="view-the-telemetry"></a>Visa Telemetrin

När DevKit-enheten startas om visar skärmen på enheten:

* Antalet meddelanden om telemetri som skickats.
* Antalet felaktiga försök.
* Antalet önskade egenskaper som har tagits emot och antalet rapporterade egenskaper som skickats.

> [!NOTE]
> Om enheten verkar upprepas när den försöker ansluta kontrollerar du om enheten är **blockerad** i IoT Central och **avblockerar** enheten så att den kan ansluta till appen.

Skaka enheten för att skicka en rapporterad egenskap. Enheten skickar ett slumptal som enhets egenskap för **chips nummer** .

Du kan visa mått för telemetri och rapporterade egenskaps värden och konfigurera inställningar i Azure IoT Central:

1. Använd **Device Explorer** för att navigera till sidan **mått** för den verkliga MXChip-enhet som du har lagt till:

    ![Navigera till den riktiga enheten](media/howto-connect-devkit/realdevicenew.png)

1. På sidan **mätningar** kan du se att Telemetrin kommer från MXChip-enheten:

    ![Visa telemetri från verklig enhet](media/howto-connect-devkit/devicetelemetrynew.png)

1. På sidan **Egenskaper** kan du Visa det senaste tärnings numret och enhets platsen som rapporteras av enheten:

    ![Visa enhets egenskaper](media/howto-connect-devkit/devicepropertynew.png)

1. På sidan **Inställningar** kan du uppdatera inställningarna på MXChip-enheten:

    ![Visa enhets inställningar](media/howto-connect-devkit/devicesettingsnew.png)

1. På sidan **kommandon** kan du anropa kommandona **eko** och **nedräkning** :

    ![Anrops kommandon](media/howto-connect-devkit/devicecommands.png)

1. På **instrument panels** sidan kan du se plats kartan

    ![Visa enhets instrument panel](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Ladda ned käll koden

Om du vill utforska och ändra enhets koden kan du ladda ned den från GitHub. Om du planerar att ändra koden bör du följa dessa instruktioner för att [förbereda utvecklings miljön](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) för ditt Skriv bords operativ system.

Hämta käll koden genom att köra följande kommando på din station ära dator:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Föregående kommando hämtar käll koden till en mapp med namnet `iot-central-firmware`.

> [!NOTE]
> Om **git** inte är installerat i utvecklings miljön kan du ladda ned det från [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Granska koden

Använd Visual Studio Code för att öppna mappen `MXCHIP/mxchip_advanced` i mappen `iot-central-firmware`:

![Visual Studio-kod](media/howto-connect-devkit/vscodeview.png)

Om du vill se hur telemetri skickas till Azure IoT Central-programmet öppnar du filen **telemetri. cpp** i mappen `src`:

- Funktionen `TelemetryController::buildTelemetryPayload` skapar nytto lasten för JSON-telemetri med hjälp av data från sensorer på enheten.

- Funktionen `TelemetryController::sendTelemetryPayload` anropar `sendTelemetry` i **AzureIOTClient. cpp** för att skicka JSON-nyttolasten till den IoT Hub ditt Azure IoT Central-program använder.

Om du vill se hur egenskaps värden rapporteras till Azure IoT Central-programmet öppnar du filen **telemetri. cpp** i mappen `src`:

- Funktionen `TelemetryController::loop` skickar **plats** rapporteringen ungefär var 30: e sekund. Den använder funktionen `sendReportedProperty` i käll filen **AzureIOTClient. cpp** .

- Funktionen `TelemetryController::loop` skickar egenskapen **dieNumber** som rapporteras när enheten accelerometer identifierar en dubbel knackning. Den använder funktionen `sendReportedProperty` i käll filen **AzureIOTClient. cpp** .

Om du vill se hur enheten svarar på kommandon som anropas från IoT Central-programmet öppnar du filen **registeredMethodHandlers. cpp** i mappen `src`:

- Funktionen **dmEcho** är hanterare för kommandot **ECHO** . Den visar **displayedValue** som har arkiverats i nytto lasten på enhetens skärm.

- Funktionen **dmCountdown** är hanterare för kommandot för **nedräknings** kommandot. Den ändrar färgen på enhetens indikator och använder en rapporterad egenskap för att skicka nedräkning svärdet tillbaka till IoT Central programmet. Den rapporterade egenskapen har samma namn som kommandot. Funktionen använder funktionen `sendReportedProperty` i käll filen **AzureIOTClient. cpp** .

Koden i käll filen **AzureIOTClient. cpp** använder Functions från [Microsoft Azure IoT SDK: er och bibliotek för C](https://github.com/Azure/azure-iot-sdk-c) för att interagera med IoT Hub.

Information om hur du ändrar, skapar och laddar upp exempel koden till din enhet finns i **Readme.MD** -filen i mappen `MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>MXChip information om enhets mal len

Ett program som skapats från Devkits program mal len innehåller en MXChip enhets mal len med följande egenskaper:

### <a name="measurements"></a>Mått

#### <a name="telemetry"></a>Telemetri

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| styr           | C     | – 40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1 000    | 0              |
| accelerometerX | MB     | – 2000   | 2000    | 0              |
| accelerometerY | MB     | – 2000   | 2000    | 0              |
| accelerometerZ | MB     | – 2000   | 2000    | 0              |
| gyroscopeX     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeY     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | – 2000   | 2000    | 0              |

#### <a name="states"></a>Tillstånd 
| Namn          | Visningsnamn   | GÄNGSE | NOGA | FARLIGA | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Enhetstillstånd   | Grön  | Orange  | Röd    | 

#### <a name="events"></a>Events 
| Namn             | Visningsnamn      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Knapp B nedtryckt  | 

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Grund |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Strömförsörjning      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Tillfället      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkt hastighet    | fanSpeed   | VARVTAL   | 0              | 0       | 1 000    | 0       |

Växla inställningar

| Visningsnamn | Fältnamn | På text | Av text | Grund |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | Rund      | Av     |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhets egenskap | Tärnings nummer   | dieNumber  | nummer    |
| Enhets egenskap | Enhets plats   | location  | location    |
| Text            | Tillverkad i     | tillverkade   | Gäller inte       |

### <a name="commands"></a>Kommandon

| Visningsnamn | Fältnamn | Returtyp | Visnings namn för Indatatyp | Fält namn för indatamängd | Typ av Indatatyp |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Funktionen         | eko       | text        | värde att Visa         | displayedValue   | text             |
| Tids    | Tids  | nummer      | Räkna från               | countFrom        | nummer           |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en MXChip IoT-DevKit till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [konfigurerar en anpassad enhets mall](howto-set-up-template.md) för din egen IoT-enhet.
