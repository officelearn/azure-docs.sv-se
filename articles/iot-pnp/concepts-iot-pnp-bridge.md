---
title: IoT Plug and Play-brygga | Microsoft Docs
description: Förstå IoT Plug and Play Bridge och hur du använder den för att ansluta befintliga enheter som är anslutna till en Windows-eller Linux-gateway som IoT Plug and Play-enheter.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580722"
---
# <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-brygga

IoT Plug and Play Bridge är ett program med öppen källkod för att ansluta befintliga enheter som är anslutna till Windows eller Linux gateway som IoT Plug and Play-enheter. När du har installerat och konfigurerat programmet på din Windows-eller Linux-dator kan du använda det för att ansluta anslutna enheter till en IoT-hubb. Du kan använda bryggan för att mappa IoT Plug and Play-gränssnitt till den telemetri som de anslutna enheterna skickar, arbeta med enhets egenskaper och anropa kommandon.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Till vänster finns det ett par befintliga sensorer anslutna (både kabelanslutna och trådlösa) till en Windows-eller Linux-dator som innehåller IoT Plug and Play Bridge. IoT Plug and Play Bridge ansluter sedan till en IoT-hubb på höger sida":::

IoT Plug and Play Bridge kan distribueras som en fristående körbar fil på alla IoT-enheter, industriella datorer, servrar eller gateway som kör Windows 10 eller Linux. Den kan också kompileras till din program kod. En enkel konfigurations-JSON-fil talar om IoT Plug and Play Bridge som anslutna enheter/kring utrustning ska exponeras på upp till Azure.

## <a name="supported-protocols-and-sensors"></a>Protokoll och sensorer som stöds

IoT Plug and Play Bridge stöder följande typer av kring utrustning som standard, med länkar till kort dokumentationen:

|Routrar|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Ja|Inga|
|[Kameror](https://aka.ms/iot-pnp-bridge-camera)               |Ja|Inga|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Ja|Ja|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Ja|Ja|
|[Nummer](https://aka.ms/iot-pnp-bridge-serial)                |Ja|Ja|
|[Windows USB-kringutrustning](https://aka.ms/iot-pnp-bridge-usb)  |Ja|Ej tillämpligt|

>[!Important]
>Utvecklare kan utöka IoT Plug and Play-bryggan till att stödja ytterligare enhets protokoll via instruktionerna i **[IoT plug and Play Bridge Developer-dokumentationen här](https://aka.ms/iot-pnp-bridge-dev-doc)**.

## <a name="prerequisites"></a>Förutsättningar

### <a name="os-platform"></a>OS-plattform

Följande OS-plattformar och versioner stöds:

|Plattform  |Versioner som stöds  |
|---------|---------|
|Windows 10 |     Alla Windows SKU: er stöds. Exempel: IoT Enterprise, Server, Desktop, IoT Core. *För övervakning av kamera hälsa rekommenderas 20H1 eller senare build. Alla andra funktioner är tillgängliga på alla Windows 10-versioner.*  |
|Linux     |Funktioner för andra distributioner har testats och stöds på Ubuntu 18,04.         |
||

### <a name="hardware"></a>Maskinvara

- Maskin varu plattform som stöder ovanstående OS-SKU: er och versioner.
- Serie-, USB-, Bluetooth-och kamera-kring utrustning och sensorer stöds internt. IoT Plug and Play Bridge kan utökas för att stödja anpassad kring utrustning eller sensor ([Se avsnittet kring utrustning ovan](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Utvecklingsmiljö

För att skapa, utöka och utveckla IoT Plug and Play-bryggan behöver du:  

- En utvecklings miljö som stöder kompilering av C++, till exempel: [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/)– se till att du inkluderar Skriv bords utveckling med C++-arbetsbelastning när du installerar Visual Studio.
- [Cmake](https://cmake.org/download/) – när du installerar cmake väljer du alternativet `Add CMake to the system PATH` .
- Om du bygger på Windows måste du också hämta Windows 17763 SDK: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Azure IoT Hub Device C SDK](https://github.com/Azure/azure-iot-sdk-c). De build-skript som ingår i den här lagrings platsen kommer automatiskt att klona den nödvändiga Azure IoT C SDK: n åt dig.

### <a name="azure-iot-products-and-tools"></a>Azure IoT-produkter och-verktyg

- **Azure-IoT Hub** – du behöver en [Azure IoT-hubb](https://docs.microsoft.com/azure/iot-hub/) i din Azure-prenumeration för att ansluta enheten till. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar. Om du inte har en IoT-hubb [följer du de här anvisningarna för att skapa en](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> IoT Plug and Play är för närvarande tillgängligt på IoT-hubbar som skapats i de centrala regionerna USA, norra Europa och Östra Japan. IoT Plug and Play-support ingår inte i IoT-hubbar på Basic-nivå. Om du vill interagera med din IoT Plug and Play-enhet kan du använda Azure IoT Explorer-verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

## <a name="iot-plug-and-play-bridge-architecture"></a>Arkitektur för IoT Plug and Play Bridge

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Till vänster finns det ett par befintliga sensorer anslutna (både kabelanslutna och trådlösa) till en Windows-eller Linux-dator som innehåller IoT Plug and Play Bridge. IoT Plug and Play Bridge ansluter sedan till en IoT-hubb på höger sida":::

## <a name="download-iot-plug-and-play-bridge"></a>Hämta IoT Plug and Play Bridge

Du kan ladda ned en fördefinierad version av bryggan med stödda kort i [IoT plug and Play Bridge-versioner](https://aka.ms/iot-pnp-bridge-releases) och expandera listan med till gångar för den senaste versionen. Hämta den senaste versionen av programmet för ditt operativ system.

Du kan också hämta och Visa käll koden för [IoT plug and Play Bridge på GitHub](https://aka.ms/bridge).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över arkitekturen i IoT Plug and Play Bridge är nästa steg att lära dig mer om:

- [Använda IoT Plug and Play Bridge](./howto-use-iot-pnp-bridge.md)
- [Se GitHub Developer Reference för IoT Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
- [IoT Plug and Play-brygga på GitHub](https://aka.ms/iotplugandplaybridge)
