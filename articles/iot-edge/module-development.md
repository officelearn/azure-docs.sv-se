---
title: Utveckla moduler för Azure IoT Edge | Microsoft Docs
description: Lär dig hur du skapar anpassade moduler för Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2513624aecff652e8a952b3255faf2ab9366f21a
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288704"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Förstå de krav och verktyg för att utveckla IoT Edge-moduler

Den här artikeln beskriver vilka funktioner som finns tillgängliga när du skriver program som körs som IoT Edge-modul och hur du drar nytta av dem.

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

Se [utveckla och distribuera en IoT Edge-modul till en simulerad enhet](tutorial-csharp-module.md) ett exempel på en modul-program som skickar meddelanden från enheten till molnet och använder modultvillingen.

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet
För att aktivera komplexa bearbetning av meddelanden från enheten till molnet, IoT Edge hub ger deklarativ meddelanderoutning mellan moduler och mellan moduler och IoT Hub. Deklarativ routning låter moduler att fånga upp och bearbeta meddelanden som skickas av andra moduler och sprida dem till komplexa pipelines. Artikeln [modulsammansättningen](module-composition.md) förklarar hur du ordna datalagrings-moduler i komplexa pipelines med vägar.

En IoT Edge-modul, till skillnad från en normal IoT Hub device program, kan ta emot meddelanden från enheten till molnet som körs via en proxy av sin lokala IoT Edge hub för att bearbeta dem på.

IoT Edge hub sprider meddelanden till din modul som baseras på deklarativa vägar som beskrivs i den [modulsammansättningen](module-composition.md) artikeln. När du utvecklar en IoT Edge-modul, kan du ta emot dessa meddelanden genom att ange meddelandehanterare.

IoT Edge för att underlätta skapandet av vägar, lägger till begreppet modulen *inkommande* och *utdata* slutpunkter. En modul kan ta emot alla meddelanden från enheten till molnet dirigeras till den utan att ange några indata och kan skicka meddelanden från enheten till molnet utan att ange några utdata.
Med hjälp av explicita indata och utdata, men gör routningsregler enklare att förstå. Mer information om regler för Routning och indata- och slutpunkter för moduler finns [modulsammansättningen](module-composition.md).

Slutligen är enhet-till-moln-meddelanden som hanteras av Edge hub stämplad med följande Systemegenskaper för:

| Egenskap  | Beskrivning |
| -------- | ----------- |
| $connectionDeviceId | Enhets-ID för den klient som skickade meddelandet |
| $connectionModuleId | Modul-ID för den modul som skickade meddelandet |
| $inputName | Indata som har fått det här meddelandet. Kan vara tom. |
| $outputName | De utdata som används för att skicka meddelandet. Kan vara tom. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluter till IoT Edge hub från en modul
Ansluter till lokala IoT Edge hub från en modul omfattar två steg: 
1. Använd anslutningssträngen som tillhandahålls av IoT Edge-körningen när din modul startar.
2. Kontrollera att ditt program godkänner certifikatet som presenteras av IoT Edge-hubben på den enheten.

Anslutande strängen som ska använda matas in av IoT Edge-körning i miljövariabeln `EdgeHubConnectionString`. Detta gör dem tillgängliga för alla program som vill använda den.

På samma sätt kan certifikatet som ska använda för att validera IoT Edge hubbanslutning matas in av IoT Edge-körning i en fil vars sökväg är tillgänglig i miljövariabeln `EdgeModuleCACertificateFile`.

## <a name="next-steps"></a>Nästa steg

När du utvecklar en modul kan lära dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).

