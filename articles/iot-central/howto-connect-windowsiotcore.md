---
title: Ansluta en Windows IoT Core-enhet till Azure IoT Central programmet | Microsoft Docs
description: Lär dig hur du ansluter en enhet för MXChip IoT DevKit till Azure IoT Central programmet som utvecklare enheten.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ff45ecc00d1c5c810015c8007d194b882f32f3da
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412576"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Anslut en Windows IoT Core-enhet till din Azure IoT Central program

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta en Windows IoT Core-enhet till din Microsoft Azure IoT Central program.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT Central program som skapats från den **exempel Devkits** mall för program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
2. En enhet som kör operativsystemet Windows 10 IoT Core. Den här genomgången använder vi en Raspberry Pi.


## <a name="sample-devkits-application"></a>**Exempel på Devkits** program

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Windows IoT Core** enheten mallen med följande egenskaper: 

- Telemetri som innehåller mätningarna för enheten **fuktighet**, **temperatur** och **tryck**. 
- Inställningar som visar **fläkthastighet**.
- Egenskaper som innehåller enhetsegenskap **dör nummer** och **plats** egenskap i molnet.


Fullständig information om konfigurationen av enheten mallen finns i [mallinformationen för Windows IoT Core-enhet](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **Windows IoT Core** enheten mallen och gjort en notering enhetens anslutningssträng. Mer information finns i [ge en riktig enhet till Azure IoT Central programmet](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Förbereda Windows IoT Core-enhet

Konfigurera en Windows IoT Core-enhet genom att följa steg-för-steg-instruktioner i [ställer in en enhet med Windows IoT Core](https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Lägga till en riktig enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **Windows IoT Core** enheten mallen och gjort en notering enhetsinformation för anslutningen (**Scope-ID, enhets-ID, primärnyckel**). Mer information finns i [ge en riktig enhet till Azure IoT Central programmet](tutorial-add-device.md).

 > [!NOTE]
   > Azure IoT Central har övergått till med hjälp av Azure IoT Hub Device Provisioning-tjänsten (DPS) för alla anslutningar på enheten, följer du dessa instrustions till [hämta enhetens anslutningssträng](concepts-connectivity.md#getting-device-connection-string) och fortsätta med resten av kursen.

## <a name="prepare-the-windows-10-iot-core-device"></a>Förbereda Windows 10 IoT Core-enhet

### <a name="what-youll-need"></a>Det här behöver du

Om du vill konfigurera en riktig Windows 10 IoT Core-enhet behöver du först ha en enhet som kör Windows 10 IoT Core. Lär dig hur du konfigurerar en Windows 10 IoT Core-enhet [här](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

Du måste också ett klientprogram som kan kommunicera med Azure IoT Central. Du kan skapa egna anpassade program med Azure SDK och distribuera den till din enhet med hjälp av Visual Studio eller du kan ladda ned en [färdiga exemplet](https://developer.microsoft.com/windows/iot/samples) och helt enkelt distribuera och köra den på enheten. 

### <a name="deploying-the-sample-client-application"></a>Distribuera exempelprogrammet för klienten

För att distribuera klientprogrammet från föregående steg till din Windows 10 IoT-enhet för att förbereda den:

**Se till att anslutningssträngen lagras på enheten för att klientprogrammet kan använda**
* Spara anslutningssträngen i en textfil med namnet connection.string.iothub på skrivbordet.
* Kopiera filen till enhetens dokumentmapp: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

När du har gjort det, måste du öppna den [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) genom att skriva i http://[device-IP-address]:8080 i alla webbläsare.

Från det och, som visas i om nedan, bör du:
1. Expandera noden ”appar” till vänster.
2. Klicka på ”snabbt och kör exempel”.
3. Klicka på ”Azure IoT Hub-klient”.
4. Klicka på ”distribuera och kör”.

![GIF av Azure IoT Hub-klienten på Windows Device Portal](./media/howto-connect-windowsiotcore/iothubapp.gif)

När återställningen har genomförts, programmet startar på enheten och se ut så här:

![Skärmbild av Azure IoT Hub-klientappen](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

I Azure IoT Central, kan du se hur koden körs på Raspberry Pi interagerar med programmet:

* På den **mätningar** sidan för din riktig enhet visas telemetri.
* På den **egenskaper** sidan ser du värdet för egenskapen rapporterade dör tal.
* På den **inställningar** kan du kan ändra olika inställningar på Raspberry Pi, till exempel spänning och fläkt hastighet.

## <a name="download-the-source-code"></a>Ladda ned källkoden

Om du vill utforska och ändra källkoden för klientprogrammet kan du hämta det från GitHub [här](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Om du planerar att ändra koden, ska du följa dessa instruktioner i filen Viktigt [här](https://github.com/Microsoft/Windows-iotcore-samples) för ditt operativsystem för stationära datorer.

> [!NOTE]
> Om **git** har inte installerats i din utvecklingsmiljö kan du hämta det från [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Windows IoT Core-enhet mallinformationen

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
