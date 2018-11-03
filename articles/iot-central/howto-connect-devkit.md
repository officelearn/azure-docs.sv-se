---
title: En ansluts DevKit till programmet Azure IoT Central | Microsoft Docs
description: Lär dig hur du ansluter en enhet för MXChip IoT DevKit till Azure IoT Central programmet som utvecklare enheten.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: cca475f552495a42aabc60faf0bd57382f148120
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958591"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Anslut en enhet för MXChip IoT DevKit till programmet Azure IoT Central

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta MXChip IoT DevKit (DevKit) enheter till Microsoft Azure IoT Central programmet.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT Central program som skapats från den **exempel Devkits** mall för program. Mer information finns i den [en program-snabbstarten för att skapa](quick-deploy-iot-central.md).
1. En DevKit-enhet. Du kan köpa en DevKit enhet [MXChip IoT DevKit](http://mxchip.com/az3166).


## <a name="sample-devkits-application"></a>**Exempel på Devkits** program

Ett program som skapats från den **exempel Devkits** programmall innehåller en **MXChip** enheten mallen med följande egenskaper: 

- Telemetri som innehåller mätningarna för enheten **fuktighet**, **temperatur**, **tryck**, **Magnometer** (mätt längs X Y, Z-axeln), **Accelorometer** (mätt längs X, Y, Z-axeln) och **gyroskop** (mätt längs X, Y, Z-axeln).
- Tillstånd som innehåller ett exempel mått för **enhetstillstånd**.
- Mätning av händelse med en **knappen B nedtryckt** händelse. 
- Inställningar som visar **Voltage**, **aktuella**, **fläkthastighet**, och en **IR** växlingsknappen.
- Egenskaper som innehåller enhetsegenskap **dör nummer** och **enhetsplats** som är en Platsegenskapen samt som i en **tillverkade i** egenskap i molnet. 


Fullständig information om konfigurationen finns i [MXChip mall enhetsinformation](howto-connect-devkit.md#mxchip-device-template-details)


## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **MXChip** enheten mallen och gjort en notering enhetsinformation för anslutningen (**Scope-ID, enhets-ID och primära nyckel**).

1. Lägg till en **riktig enhet** från Device Explorer klickar du på **+ Ny > verkliga** att lägga till en riktig enhet.
    * Ange enhets-Id **<span style="color:Red">(bör vara gemener)</span>** eller använder den föreslagna enhets-Id.
    * Ange namnet på enheten eller använda det föreslagna namnet
    
    ![Lägg till enhet](media\concepts-connectivity\add-device.png)


1. Hämta anslutningsinformationen som **Scope-ID, enhets-ID och primära nyckel** för tillagd enhet genom att klicka på **Connect** på enhetssidan.
 
    ![Anslutningsinformation](media\concepts-connectivity\device-connect.PNG)

3. Se till att spara dessa uppgifter som du kommer temporaritly hämta ansluten till internet när du förbereder DevKit enheten. 


### <a name="prepare-the-devkit-device"></a>Förbered enheten DevKit

> [!NOTE]
> Om du tidigare har använt enheten och har wifi autentiseringsuppgifter lagras och vill konfigurera om enheten för att använda ett annat Wi-Fi-nätverk eller anslutningssträng telemetri mätning, trycker du på både den **A** och **B** knappar på tavlan samtidigt. Om det inte fungerar, trycker du på **återställa** knappen och försök igen.



#### <a name="to-prepare-the-devkit-device"></a>Förbereda enheten DevKit:


1. Ladda ned den senaste färdiga Azure IoT Central inbyggda programvaran för MXChip från den [släpper](http://aka.ms/iotcentral-docs-MXChip-releases) sidan på GitHub.
1. Anslut DevKit enheten till din utvecklingsdator med hjälp av en USB-kabel. I Windows öppnas en fil explorer på en enhet som har mappats till lagring på DevKit enheten. Exempelvis kan enheten kan anropas **AZ3166 (D:)**.
1. Dra den **iotCentral.bin** fil till fönstret enhet. När kopieringen är klar, startar enheten om med den nya inbyggda programvaran.

1. När DevKit enheten startas om, visas följande skärm:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Om skärmen visar allt annat, återställa enheten och tryck på den **A** och **B** knappar på enheten på samma gång för att starta om enheten. 

1. Enheten är nu i åtkomstläge åtkomstpunkten (AP). Du kan ansluta till den här Wi-Fi-åtkomstpunkt från din dator eller mobil enhet.

1. Anslut till namnet på Wi-Fi-nätverket som visas på skärmen för enheten på din dator, telefon eller surfplatta. När du ansluter till nätverket kan har du inte tillgång till internet. Det här tillståndet är förväntat och du endast är ansluten till nätverket under en kort period när du konfigurerar enheten.

1. Öppna webbläsaren och navigera till [ http://192.168.0.1/start ](http://192.168.0.1/start). I följande sida visas:

    ![Konfigurationssidan för enhet](media/howto-connect-devkit/configpage.png)

    På webbsidan: 
    - Lägg till namnet på ditt Wi-Fi-nätverk 
    - lösenordet för Wi-Fi-nätverk
    - PIN-koden som visas på enheten LCD 
    - anslutningsinformationen **Scope-Id, enhets-Id och primära nyckel** för enheten (du bör redan har sparat den här följa stegen)      
    - Välj all tillgänglig telemetri mätningar! 

1. När du har valt **– konfigurera enhet**, visas den här sidan:

    ![Enheter som har konfigurerats](media/howto-connect-devkit/deviceconfigured.png)

1. Tryck på den **återställa** knappen på din enhet.


## <a name="view-the-telemetry"></a>Visa telemetrin

När DevKit enheten startas om, visar skärmen på enheten:

* Antal telemetrimeddelanden som skickas.
* Antal fel.
* Antalet önskade egenskaper som tagits emot och antalet rapporterade egenskaper som skickas.

> [!NOTE]
> Om enheten verkar vara slingor under connect kontrollera om enheten är *blockerad* i IoT Central och *avblockera* enheten så att den kan ansluta till appen.

Skaka enheten öka antalet rapporterade egenskaper som skickas. Enheten skickar ett slumptal som den **dör nummer** enhetsegenskap.

Du kan visa telemetri mätning av faktisk användning och rapporterade egenskapsvärden och konfigurera inställningar i Azure IoT Central:

1. Använd **Device Explorer** att navigera till den **mätningar av** för verkliga MXChip enheten som du har lagt till:

    ![Gå till riktig enhet](media/howto-connect-devkit/realdevicenew.png)

1. På den **mätningar av** kan du se telemetri som kommer från MXChip-enhet:

    ![Visa telemetri från riktig enhet](media/howto-connect-devkit/devicetelemetrynew.png)

1. På den **egenskaper** kan du visa den senaste nummer och enhetens plats som rapporteras av enheten:

    ![Visa egenskaper för enhet](media/howto-connect-devkit/devicepropertynew.png)

1. På den **inställningar** kan du uppdatera inställningarna på enheten MXChip:

    ![Visa Enhetsinställningar](media/howto-connect-devkit/devicesettingsnew.png)

1. På den **instrumentpanelen** kan du se platsen mappa

    ![Visa instrumentpanelen](media/howto-connect-devkit/devicedashboardnew.png)


## <a name="download-the-source-code"></a>Ladda ned källkoden

Om du vill utforska och ändra koden för enheten kan hämta du det från GitHub. Om du planerar att ändra koden bör du följa instruktionerna [förbereda utvecklingsmiljön](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) för ditt operativsystem för stationära datorer.

Om du vill ladda ned källkoden, kör du följande kommando på din stationära dator:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Föregående kommando hämtar källkoden till en mapp med namnet `iot-central-firmware`. 

> [!NOTE]
> Om **git** har inte installerats i din utvecklingsmiljö kan du hämta det från [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Granska koden

Använd Visual Studio Code, som installerades när du har förberett din utvecklingsmiljö för att öppna den `AZ3166` mapp i den `iot-central-firmware` mapp: 

![Visual Studio-koden](media/howto-connect-devkit/vscodeview.png)

Om du vill se hur telemetri som skickas till programmet Azure IoT Central, öppna den **main_telemetry.cpp** filen i källmappen.

Funktionen `buildTelemetryPayload` skapar JSON-telemetri-nyttolast med hjälp av data från sensorerna på enheten.

Funktionen `sendTelemetryPayload` anrop `sendTelemetry` i den **iotHubClient.cpp** att skicka JSON-nyttolasten till IoT Hub ditt Azure IoT Central program använder.

Om du vill se hur egenskapsvärden rapporteras till programmet Azure IoT Central, öppna den **main_telemetry.cpp** filen i källmappen.

Funktionen `telemetryLoop` skickar den **doubleTap** rapporterade egenskap när accelerometer identifierar en dubbelknacka. Den använder den `sendReportedProperty` fungera i den **iotHubClient.cpp** källfilen.

Koden i den **iotHubClient.cpp** källfilen använder funktioner från den [ Microsoft Azure IoT SDK: er och bibliotek för C](https://github.com/Azure/azure-iot-sdk-c) att interagera med IoT Hub.

Information om hur du ändrar, skapa och överföra kod till din enhet finns i den **readme.md** fil i den `AZ3166` mapp.

## <a name="mxchip-device-template-details"></a>MXChip mall enhetsinformation 

Ett program som skapats med mallen för exemplet Devkits program innehåller en MXChip enhet mall med följande egenskaper:

### <a name="measurements"></a>Mått

#### <a name="telemetry"></a>Telemetri 

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


#### <a name="states"></a>Tillstånd 
| Namn          | Visningsnamn   | NORMAL | VARNING | RISK | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Enhetens tillstånd   | Grön  | Orange  | Röd    | 

#### <a name="events"></a>Händelser 
| Namn             | Visningsnamn      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Knappen B är nedtryckt  | 

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
| Enhetsegenskap | Enhetsplats   | location  | location    |
| Text            | Tillverkas på     | manufacturedIn   | Gäller inte       |



## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en enhet för DevKit till programmet Azure IoT Central, är här nästa föreslagna steg:

* [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)
