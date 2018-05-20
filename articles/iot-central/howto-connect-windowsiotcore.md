---
title: Ansluter en Windows IoT Core-enhet till din Azure IoT centralt program | Microsoft Docs
description: Som en enhet utvecklare lär du dig hur du ansluter en MXChip IoT DevKit enhet till din Azure IoT centralt program.
services: iot-central
author: miriamb
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: c38231f97eeb1c4511702bf3e788f72918cab045
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Anslut en Windows IoT Core-enhet till din Azure IoT centralt program

Den här artikeln beskriver hur som utvecklare som enheten kan du ansluta en Windows IoT Core till Microsoft Azure IoT Central programmet.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT centrala program som skapas från den **exempel Devkits** mall för program. Mer information finns i [skapa Azure IoT centrala programmet](howto-create-application.md).
2. En enhet som kör operativsystemet Windows 10 IoT Core. Den här genomgången kommer vi att använda en hallon Pi

Ett program som skapas från den **exempel Devkits** programmall innehåller en **Windows IoT Core** enheten mallen med följande egenskaper:

### <a name="telemetry-measurements"></a>Telemetri mått

| Fältnamn     | Enheter  | Minimum | Maximal | Decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| fuktighet       | %      | 0       | 100     | 0              |
| Temp           | ° C     | -40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Decimaler | Minimum | Maximal | Inledande |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Fläkthastighet    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhetsegenskap | Dör nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Gäller inte       |

## <a name="add-a-real-device"></a>Lägga till en verklig enhet

Azure IoT centrala programmet, lägga till en verklig enhet från den **Windows IoT Core** enheten mallen och gjort en notering om anslutningssträngen för enheten. Mer information finns i [lägger till en verklig enhet tillämpningsprogrammet Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Förbered enheten Windows IoT Core

För att ställa in en enhet med Windows IoT Core Följ steg-för-steg-guide på [konfigurera en enhet med Windows IoT Core] (https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Lägga till en verklig enhet

Azure IoT centrala programmet, lägga till en verklig enhet från den **Windows IoT Core** enheten mallen och gjort en notering om anslutningssträngen för enheten. Mer information finns i [lägger till en verklig enhet tillämpningsprogrammet Azure IoT Central](tutorial-add-device.md).

## <a name="prepare-the-windows-10-iot-core-device"></a>Förbered enheten Windows 10 IoT Core

### <a name="what-youll-need"></a>Det här behöver du

Om du vill konfigurera en fysisk enhet för Windows 10 IoT Core, behöver du först har en enhet som kör Windows 10 IoT Core. Lär dig hur du konfigurerar en enhet med Windows 10 IoT Core [här](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

Du måste också ett klientprogram som kan kommunicera med Azure IoT Central. Du kan skapa egna anpassade program med hjälp av Azure SDK och distribuera den till din enhet med hjälp av Visual Studio, eller så kan du hämta en [förskapad exempel](https://developer.microsoft.com/en-us/windows/iot/samples) helt enkelt distribuera och köra den på enheten. 

### <a name="deploying-the-sample-client-application"></a>Distribuera klienten exempelprogrammet

Att distribuera klientprogrammet från föregående steg till din Windows 10 IoT-enhet för att förbereda den:

**Kontrollera anslutningssträngen lagras på enheten för klientprogrammet att använda**
* Spara anslutningssträngen i en textfil med namnet connection.string.iothub på skrivbordet.
* Kopiera filen till enhetens dokumentmapp: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

När du har gjort det, måste du öppna den [Windows Portal för enhetens](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal) genom att skriva i http://[device-IP-address]:8080 i en webbläsare.

Från det och som visas i om nedan vill du:
1. Expandera noden ”appar” till vänster.
2. Klicka på ”Kör Quick exempel”.
3. Klicka på ”Azure IoT-hubb klient”.
4. Klicka på ”distribuera och köra”.

![GIF för Azure IoT-hubb-klienten på Windows-enhet Portal](./media/howto-connect-windowsiotcore/iothubapp.gif)

När autentiseringen lyckas kan programmet startar på enheten och se ut så här:

![Skärmbild av Azure IoT-hubb-klientappen](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

Du kan se hur koden körs på hallon Pi interagerar med programmet i Azure IoT Central:

* På den **mätningar** sidan för enheten verkliga kan du se telemetrin.
* På den **egenskaper** sida, ser du värdet på egenskapen rapporterade dör nummer.
* På den **inställningar** kan du ändra olika inställningar på hallon Pi, till exempel spänning och fläkt hastighet.

## <a name="download-the-source-code"></a>Ladda ned källkoden

Om du vill utforska och ändra källkoden för klientprogrammet, kan du ladda ned det från GitHub [här](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Om du planerar att ändra koden, ska du följa instruktionerna i filen Viktigt [här](https://github.com/Microsoft/Windows-iotcore-samples) för ditt operativsystem.

> [!NOTE]
> Om **git** har inte installerats i din utvecklingsmiljö kan du ladda ned det från [ https://git-scm.com/download ](https://git-scm.com/download).
