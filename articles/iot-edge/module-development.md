---
title: Utveckla moduler för Azure IoT Edge | Microsoft Docs
description: Utveckla anpassade moduler för Azure IoT Edge som kan kommunicera med körningen och IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eae36f6b4baabdcc9831b084602d340a299a7bac
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047632"
---
# <a name="develop-your-own-iot-edge-modules"></a>Utveckla dina egna IoT Edge-moduler

Azure IoT Edge moduler kan ansluta till andra Azure-tjänster och bidra till din större data pipeline i molnet. I den här artikeln beskrivs hur du kan utveckla moduler för att kommunicera med IoT Edge Runtime och IoT Hub, och därmed resten av Azure-molnet.

## <a name="iot-edge-runtime-environment"></a>IoT Edge körnings miljö

IoT Edge runtime tillhandahåller infrastrukturen för att integrera funktionerna i flera IoT Edge moduler och distribuera dem till IoT Edge enheter. Alla program kan paketeras som en IoT Edge modul. För att dra full nytta av IoT Edge kommunikations-och hanterings funktioner kan ett program som körs i en modul använda Azure IoT-enhetens SDK för att ansluta till den lokala IoT Edge Hub.

## <a name="using-the-iot-edge-hub"></a>Använda IoT Edge Hub

IoT Edge Hub tillhandahåller två huvudsakliga funktioner: proxy till IoT Hub och lokal kommunikation.

### <a name="iot-hub-primitives"></a>IoT Hub primitiver

IoT Hub ser en analogously till en enhet, i den mening som:

* den har en modul med dubbla som är distinkt och isolerad från [enheten](../iot-hub/iot-hub-devguide-device-twins.md) , och den andra modulen är sammanflätad med enheten.
* den kan skicka [meddelanden från enheten till molnet](../iot-hub/iot-hub-devguide-messaging.md).
* den kan ta emot [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) som riktar sig specifikt till identiteten.

För närvarande kan inte moduler ta emot meddelanden från molnet till enheten eller använda fil uppladdnings funktionen.

När du skriver en modul kan du använda [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md) för att ansluta till IoT Edge Hub och använda funktionen ovan på samma sätt som när du använde IoT Hub med ett enhets program. Den enda skillnaden mellan IoT Edge moduler och IoT-enhets program är att du måste referera till modulens identitet i stället för enhetens identitet.

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet

För att möjliggöra komplex bearbetning av meddelanden från enheten till molnet tillhandahåller IoT Edge Hub deklarativ routning av meddelanden mellan moduler, och mellan moduler och IoT Hub. Deklarativ routning gör att moduler kan fånga upp och bearbeta meddelanden som skickas av andra moduler och sprida dem till komplexa pipeliner. Mer information finns i [distribuera moduler och upprätta vägar i IoT Edge](module-composition.md).

En IoT Edge modul, i stället för ett normalt IoT Hub enhets program, kan ta emot meddelanden från enheten till molnet som anropas via proxy med den lokala IoT Edge hubben för att bearbeta dem.

IoT Edge Hub sprider meddelandena till modulen baserat på deklarativ vägar som beskrivs i [distributions manifestet](module-composition.md). När du utvecklar en IoT Edge-modul kan du ta emot dessa meddelanden genom att ange meddelande hanterare.

För att förenkla skapandet av vägar IoT Edge lägger till konceptet *indata* och *utdata* -slutpunkter för moduler. En modul kan ta emot alla meddelanden från enhet till molnet som dirigeras till den utan att ange några indata och kan skicka meddelanden från enheten till molnet utan att ange några utdata. Om du använder explicita indata och utdata blir det enklare att förstå routningsregler.

Slutligen stämplas meddelanden från enhet till molnet som hanteras av Edge Hub med följande system egenskaper:

| Egenskap | Beskrivning |
| -------- | ----------- |
| $connectionDeviceId | Enhets-ID för den klient som skickade meddelandet |
| $connectionModuleId | Modul-ID för den modul som skickade meddelandet |
| $inputName | De inmatade inmeddelandena. Kan vara tom. |
| $outputName | De utdata som används för att skicka meddelandet. Kan vara tom. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluta till IoT Edge hubb från en modul

Att ansluta till den lokala IoT Edge hubben från en modul omfattar två steg:

1. Skapa en ModuleClient-instans i ditt program.
2. Kontrol lera att ditt program accepterar certifikatet som presenteras av IoT Edge Hub på enheten.

Skapa en ModuleClient-instans för att ansluta modulen till IoT Edge Hub som körs på enheten, på liknande sätt som DeviceClient-instanser ansluter IoT-enheter till IoT Hub. Mer information om klassen ModuleClient och dess kommunikations metoder finns i API-referensen för ditt prioriterade SDK-språk: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)eller [Node.js](/javascript/api/azure-iot-device/moduleclient).

## <a name="language-and-architecture-support"></a>Stöd för språk och arkitektur

IoT Edge stöder flera operativ system, enhets arkitekturer och utvecklings språk så att du kan bygga upp det scenario som passar dina behov. Använd det här avsnittet för att förstå alternativen för att utveckla anpassade IoT Edge-moduler. Du kan lära dig mer om verktygs support och krav för varje språk för [att förbereda utvecklings-och test miljön för IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

För alla språk i följande tabell IoT Edge stöder utveckling för AMD64-och ARM32 Linux-enheter.

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visual Studio-koden<br>Visual Studio 2017/2019 |
| C# | Visual Studio-koden<br>Visual Studio 2017/2019 |
| Java | Visual Studio-koden |
| Node.js | Visual Studio-koden |
| Python | Visual Studio-koden |

>[!NOTE]
>Att utveckla och felsöka stöd för ARM64 Linux-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [utveckla och FELSÖKA ARM64 IoT Edge moduler i Visual Studio Code (för hands version)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

För alla språk i följande tabell IoT Edge stöder utveckling för AMD64 Windows-enheter.

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (inga fel söknings funktioner)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Nästa steg

[Förbered din utvecklings-och test miljö för IoT Edge](development-environment.md)

[Använd Visual Studio för att utveckla C#-moduler för IoT Edge](how-to-visual-studio-develop-module.md)

[Använd Visual Studio Code för att utveckla moduler för IoT Edge](how-to-vs-code-develop-module.md)

[Förstå och använda SDK:er för Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md)