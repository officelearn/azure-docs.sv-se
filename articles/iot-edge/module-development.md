---
title: Utveckla moduler för Azure IoT Edge | Microsoft-dokument
description: Utveckla anpassade moduler för Azure IoT Edge som kan kommunicera med körningen och IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271387"
---
# <a name="develop-your-own-iot-edge-modules"></a>Utveckla dina egna IoT Edge-moduler

Azure IoT Edge-moduler kan ansluta till andra Azure-tjänster och bidra till din större molndatapipeline. I den här artikeln beskrivs hur du kan utveckla moduler för att kommunicera med IoT Edge-körningen och IoT Hub och därmed resten av Azure-molnet.

## <a name="iot-edge-runtime-environment"></a>IoT Edge körtid miljö

IoT Edge-körningen tillhandahåller infrastrukturen för att integrera funktionaliteten hos flera IoT Edge-moduler och distribuera dem på IoT Edge-enheter. Alla program kan paketeras som en IoT Edge-modul. För att dra full nytta av IoT Edge-kommunikations- och hanteringsfunktioner kan ett program som körs i en modul använda Azure IoT Device SDK för att ansluta till den lokala IoT Edge-hubben.

## <a name="using-the-iot-edge-hub"></a>Använda IoT Edge-hubben

IoT Edge-hubben innehåller två huvudfunktioner: proxy till IoT Hub och lokal kommunikation.

### <a name="iot-hub-primitives"></a>IoT Hub primitiver

IoT Hub ser en modulinstans som är jämförbar med en enhet, i den meningen att:

* Den har en modultvilling som är distinkt och isolerad från [enhetstvillingen](../iot-hub/iot-hub-devguide-device-twins.md) och de andra modultvillingarna på den enheten.
* Det kan skicka [meddelanden från enhet till moln;](../iot-hub/iot-hub-devguide-messaging.md)
* Den kan ta emot [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) som är specifikt inriktade på dess identitet.

Moduler kan för närvarande inte ta emot meddelanden från molnet till enheten eller använda filöverföringsfunktionen.

När du skriver en modul kan du använda [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) för att ansluta till IoT Edge-hubben och använda ovanstående funktioner som du gör när du använder IoT Hub med ett enhetsprogram. Den enda skillnaden mellan IoT Edge-moduler och IoT-enhetsprogram är att du måste referera till modulidentiteten i stället för enhetsidentiteten.

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet

För att möjliggöra komplex bearbetning av meddelanden från enhet till moln tillhandahåller IoT Edge-hubben deklarativ routning av meddelanden mellan moduler och mellan moduler och IoT Hub. Deklarativ routning gör det möjligt för moduler att fånga upp och bearbeta meddelanden som skickas av andra moduler och sprida dem till komplexa pipelines. Mer information finns i [distribuera moduler och upprätta vägar i IoT Edge](module-composition.md).

En IoT Edge-modul, i motsats till ett vanligt IoT Hub-enhetsprogram, kan ta emot meddelanden från enhet till moln som proxied av dess lokala IoT Edge-hubb för att bearbeta dem.

IoT Edge-hubben sprider meddelandena till din modul baserat på deklarativa vägar som beskrivs i [distributionsmanifestet](module-composition.md). När du utvecklar en IoT Edge-modul kan du ta emot dessa meddelanden genom att ange meddelandehanterare.

För att förenkla skapandet av vägar lägger IoT Edge till begreppet *modulindata* och *utdataslutpunkter.* En modul kan ta emot alla meddelanden från enhet till moln som dirigeras till den utan att ange någon indata och kan skicka meddelanden från enhet till moln utan att ange någon utdata. Om du använder explicita indata och utdata blir routningsreglerna enklare att förstå.

Slutligen stämplas meddelanden mellan enheter och moln som hanteras av Edge-hubben med följande systemegenskaper:

| Egenskap | Beskrivning |
| -------- | ----------- |
| $connectionDeviceId | Enhets-ID för klienten som skickade meddelandet |
| $connectionModuleId | Modulens ID för modulen som skickade meddelandet |
| $inputName | Indata som tog emot det här meddelandet. Kan vara tom. |
| $outputName | Utdata som används för att skicka meddelandet. Kan vara tom. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluta till IoT Edge-hubb från en modul

Att ansluta till det lokala IoT Edge-navet från en modul innebär två steg:

1. Skapa en ModuleClient-instans i ditt program.
2. Kontrollera att ditt program accepterar certifikatet som visas av IoT Edge-hubben på den enheten.

Skapa en ModuleClient-instans för att ansluta din modul till IoT Edge-hubben som körs på enheten, på samma sätt som DeviceClient-instanser ansluter IoT-enheter till IoT Hub. Mer information om klassen ModuleClient och dess kommunikationsmetoder finns i API-referensen för önskat SDK-språk: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)eller [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Stöd för språk och arkitektur

IoT Edge stöder flera operativsystem, enhetsarkitekturer och utvecklingsspråk så att du kan skapa det scenario som passar dina behov. Använd det här avsnittet för att förstå dina alternativ för att utveckla anpassade IoT Edge-moduler. Du kan läsa mer om verktygsstöd och krav för varje språk i [Förbered din utvecklings- och testmiljö för IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

För alla språk i följande tabell stöder IoT Edge utveckling för AMD64- och ARM32 Linux-enheter.

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visual Studio-koden<br>Visual Studio 2017/2019 |
| C# | Visual Studio-koden<br>Visual Studio 2017/2019 |
| Java | Visual Studio-koden |
| Node.js | Visual Studio-koden |
| Python | Visual Studio-koden |

>[!NOTE]
>Utveckla och felsöka stöd för ARM64 Linux-enheter är i [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [Utveckla och felsöka ARM64 IoT Edge-moduler i Visual Studio Code (förhandsversion).](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

### <a name="windows"></a>Windows

För alla språk i följande tabell stöder IoT Edge utveckling för AMD64 Windows-enheter.

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio-kod (inga felsökningsfunktioner)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Nästa steg

[Förbered din utvecklings- och testmiljö för IoT Edge](development-environment.md)

[Använd Visual Studio för att utveckla C#-moduler för IoT Edge](how-to-visual-studio-develop-module.md)

[Använd Visual Studio Code för att utveckla moduler för IoT Edge](how-to-vs-code-develop-module.md)

[Förstå och använda Azure IoT Hub SDK:er](../iot-hub/iot-hub-devguide-sdks.md)
