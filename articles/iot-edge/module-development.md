---
title: Utveckla moduler för Azure IoT Edge | Microsoft Docs
description: Utveckla anpassade moduler för Azure IoT Edge som kan kommunicera med runtime och IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ea2d1dbcf991ce858f07905bc1bb41809f9bcdd8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882747"
---
# <a name="develop-your-own-iot-edge-modules"></a>Utveckla dina egna IoT Edge-moduler

Azure IoT Edge-moduler kan ansluta med andra Azure-tjänster och bidrar till din större datapipeline i molnet. Den här artikeln beskrivs hur du kan utveckla moduler för att kommunicera med IoT Edge-körningen och IoT Hub och därför resten av Azure-molnet. 

## <a name="iot-edge-runtime-environment"></a>IoT Edge-körningsmiljö
IoT Edge-runtime tillhandahåller infrastrukturen som att integrera funktionerna i flera IoT Edge-moduler och distribuera dem till IoT Edge-enheterna.  På hög nivå, kan alla program vara paketerad som en IoT Edge-modul. Om du vill dra full nytta av IoT Edge kommunikations- och hanteringsfunktioner, men anslutas ett program som körs i en modul till lokala IoT Edge hub, integrerade i IoT Edge-körningen.

## <a name="using-the-iot-edge-hub"></a>Med hjälp av IoT Edge hub
IoT Edge hub ger två huvudsakliga funktioner: proxy till IoT Hub och lokala kommunikation.

### <a name="iot-hub-primitives"></a>IoT Hub primitiver
IoT-hubben ser en modulinstans på samma sätt som en enhet i meningen som:

* den har en modultvilling som är unik och isolerade från de [enhetstvillingen](../iot-hub/iot-hub-devguide-device-twins.md) och andra modultvillingar för en enhet;
* den kan skicka [meddelanden från enheten till molnet](../iot-hub/iot-hub-devguide-messaging.md);
* Det kan ta emot [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) specifikt mot sin identitet.

För närvarande kan inte en modul ta emot meddelanden från moln till enhet eller använda filuppladdningsfunktionen.

När du skriver en modul, du kan använda den [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) att ansluta till IoT Edge hub och använda funktionen ovan som du använder när du använder IoT Hub med ett enhetprogram, den enda skillnaden är att från ditt program backend-du måste referera till modulen identitet i stället för enhetens identitet.

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet
Om du vill aktivera komplexa bearbetning av meddelanden från enheten till molnet, IoT Edge hub ger deklarativ meddelanderoutning mellan moduler och mellan moduler och IoT Hub. Deklarativ routning låter moduler att fånga upp och bearbeta meddelanden som skickas av andra moduler och sprida dem till komplexa pipelines. Mer information finns i [distribuerar moduler och upprätta vägar i IoT Edge](module-composition.md).

En IoT Edge-modul, till skillnad från en normal IoT Hub device program, kan ta emot meddelanden från enheten till molnet som körs via en proxy av sin lokala IoT Edge hub för att bearbeta dem på.

IoT Edge hub sprider meddelanden till din modul som baseras på deklarativa vägar som beskrivs i den [distribution manifest](module-composition.md). När du utvecklar en IoT Edge-modul, kan du ta emot dessa meddelanden genom att ange meddelandehanterare.

IoT Edge för att underlätta skapandet av vägar, lägger till begreppet modulen *inkommande* och *utdata* slutpunkter. En modul kan ta emot alla meddelanden från enheten till molnet dirigeras till den utan att ange några indata och kan skicka meddelanden från enheten till molnet utan att ange några utdata. Med hjälp av explicita indata och utdata, men gör routningsregler enklare att förstå. 

Slutligen är enhet-till-moln-meddelanden som hanteras av Edge hub stämplad med följande Systemegenskaper för:

| Egenskap  | Beskrivning |
| -------- | ----------- |
| $connectionDeviceId | Enhets-ID för den klient som skickade meddelandet |
| $connectionModuleId | Modul-ID för den modul som skickade meddelandet |
| $inputName | Indata som har fått det här meddelandet. Kan vara tom. |
| $outputName | De utdata som används för att skicka meddelandet. Kan vara tom. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluter till IoT Edge hub från en modul
Ansluter till lokala IoT Edge hub från en modul omfattar två steg: 
1. Skapa en ModuleClient-instans i ditt program.
2. Kontrollera att ditt program godkänner certifikatet som presenteras av IoT Edge-hubben på den enheten.

Skapa en ModuleClient-instans för att ansluta din modulen till IoT Edge hub som körs på enheten, liknar hur DeviceClient instanser ansluta IoT-enheter till IoT Hub. Mer information om klassen ModuleClient och dess kommunikationsmetoder finns i API-referens för SDK det språk du föredrar: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C och Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), eller [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>Nästa steg

[Förbereda utvecklings- och testmiljö för IoT Edge](development-environment.md)

[Använd Visual Studio 2017 för att utveckla C# moduler för IoT Edge](how-to-visual-studio-develop-csharp-module.md)

[Använd Visual Studio Code för att utveckla moduler för IoT Edge](how-to-vs-code-develop-module.md)

