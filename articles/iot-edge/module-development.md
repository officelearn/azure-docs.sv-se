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
ms.openlocfilehash: 761485de4bf52b7261ac8f1f8c3d937486c66546
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248008"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Förstå de krav och verktyg för att utveckla IoT Edge-moduler

Den här artikeln beskriver vilka funktioner som finns tillgängliga när du skriver program som körs som IoT Edge-modul och hur du drar nytta av dem.

## <a name="iot-edge-runtime-environment"></a>IoT Edge-körningsmiljö
IoT Edge-runtime tillhandahåller infrastrukturen som att integrera funktionerna i flera IoT Edge-moduler och distribuera dem till IoT Edge-enheterna.  På hög nivå, kan alla program vara paketerad som en IoT Edge-modul. Om du vill dra full nytta av IoT Edge kommunikations- och hanteringsfunktioner, men anslutas ett program som körs i en modul till lokala IoT Edge hub, integrerade i IoT Edge-körningen.

## <a name="using-the-iot-edge-hub"></a>Med hjälp av IoT Edge hub
IoT Edge hub ger två huvudsakliga funktioner: proxy till IoT Hub och lokala kommunikation.

### <a name="iot-hub-primitives"></a>IoT Hub primitiver
IoT Hub ser en modul instans på samma sätt till en enhet, i meningen att den:

* den har en modultvilling som är unik och isolerade från de [enhetstvillingen](../iot-hub/iot-hub-devguide-device-twins.md) och andra modultvillingar för en enhet;
* den kan skicka [meddelanden från enheten till molnet](../iot-hub/iot-hub-devguide-messaging.md);
* Det kan ta emot [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) specifikt mot sin identitet.

För närvarande kan inte en modul ta emot meddelanden från moln till enhet eller använda filuppladdningsfunktionen.

När du skriver en modul ska du använda den [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) att ansluta till IoT Edge hub och använda funktionen ovan som du använder när du använder IoT Hub med ett enhetprogram, den enda skillnaden är att från din serverdelen av programmet som du har att referera till modulen identitet i stället för enhetens identitet.

Se [utveckla och distribuera en IoT Edge-modul till en simulerad enhet](tutorial-csharp-module.md) ett exempel på en modul-program som skickar meddelanden från enheten till molnet och använder modultvillingen.

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet
För att aktivera komplexa bearbetning av meddelanden från enheten till molnet, IoT Edge hub ger deklarativ meddelanderoutning mellan moduler och mellan moduler och IoT Hub.
På så sätt kan moduler att fånga upp och bearbeta meddelanden som skickas av andra moduler och sprida dem till komplexa pipelines.
Artikeln [modulsammansättningen](module-composition.md) förklarar hur du ordna datalagrings-moduler i komplexa pipelines med vägar.

Annorlunda än en normal IoT Hub device program, kan en IoT Edge-modul, ta emot meddelanden från enheten till molnet som körs via en proxy av sin lokala IoT Edge hub för att bearbeta dem.

IoT Edge hub sprider meddelanden till din modul som baseras på deklarativa vägar som beskrivs i den [modulsammansättningen](module-composition.md) artikeln. När du utvecklar en IoT Edge-modul, du kan ta emot dessa meddelanden genom att ange meddelandehanterare, som visas i självstudien [utveckla och distribuera en IoT Edge-modul till en simulerad enhet] [lnk-tutorial2].

IoT Edge för att underlätta skapandet av vägar, lägger till begreppet modulen *inkommande* och *utdata* slutpunkter. En modul kan ta emot alla meddelanden från enheten till molnet dirigeras till den utan att ange några indata och kan skicka meddelanden från enheten till molnet utan att ange några utdata.
Med hjälp av explicita indata och utdata, men gör routningsregler enklare att förstå. Se [modulsammansättningen](module-composition.md) mer information om regler för Routning och indata- och slutpunkter för moduler.

Slutligen är enhet-till-moln-meddelanden som hanteras av Edge hub stämplad med följande Systemegenskaper för:

| Egenskap  | Beskrivning |
| -------- | ----------- |
| $connectionDeviceId | Enhets-ID för den klient som skickade meddelandet |
| $connectionModuleId | Modul-ID för den modul som skickade meddelandet |
| $inputName | Indata som har fått det här meddelandet. Kan vara tom. |
| $outputName | De utdata som används för att skicka meddelandet. Kan vara tom. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluter till IoT Edge hub från en modul
Ansluter till lokala IoT Edge hub från en modul omfattar två steg: använda anslutningssträngen förutsatt att genom IoT Edge-körningen när din modul startar och kontrollera att ditt program godkänner certifikatet som presenteras av IoT Edge-hubben på den enheten.

Anslutande strängen som ska använda matas in av IoT Edge-körning i miljövariabeln `EdgeHubConnectionString`. Detta gör dem tillgängliga för alla program som vill använda den.

På samma sätt kan certifikatet som ska använda för att validera IoT Edge hubbanslutning matas in av IoT Edge-körning i en fil vars sökväg är tillgänglig i miljövariabeln `EdgeModuleCACertificateFile`.

Självstudien [utveckla och distribuera en IoT Edge-modul till en simulerad enhet] [lnk-tutorial2] visar hur du kan kontrollera att certifikatet finns i datorarkivet i programmets modul. Uppenbarligen någon annan metod som ska lita på anslutningar med hjälp av arbetet i certifikatet.

## <a name="packaging-as-an-image"></a>Paketering som en bild
IoT Edge-moduler är paketerade som Docker-avbildningar.
Du kan använda Docker verktygskedja direkt eller Visual Studio Code som visas i självstudien [utveckla och distribuera en IoT Edge-modul till en simulerad enhet] [lnk-tutorial2].

## <a name="next-steps"></a>Nästa steg

När du utvecklar en modul kan lära dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).

