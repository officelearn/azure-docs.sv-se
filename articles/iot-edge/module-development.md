---
title: Utveckla moduler för Azure IoT kant | Microsoft Docs
description: Lär dig hur du skapar anpassade moduler för Azure IoT kant
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95b1d5d4e5e11f96b6abb17f0aeba935cc65512d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules---preview"></a>Förstå de krav och verktyg för att utveckla IoT kant moduler – förhandsgranskning

Den här artikeln förklarar vilka funktioner är tillgängliga när du skriver program som körs som IoT kant och hur du drar nytta av dem.

## <a name="iot-edge-runtime-environment"></a>IoT-Edge-körningsmiljön
IoT Edge-runtime tillhandahåller infrastrukturen som att integrera funktionerna i flera IoT Edge-moduler och distribuera dem till IoT Edge-enheterna.  På en hög nivå kan alla program paketeras som en IoT-Edge-modul. Om du vill dra full nytta av IoT kant kommunikation och hanteringsfunktioner, men ansluta ett program som körs i en modul till lokala IoT kant hubben, integrerade i IoT kant-körningsmiljön.

## <a name="using-the-iot-edge-hub"></a>Med hjälp av kant för IoT-hubb
Gräns för IoT-hubb innehåller två huvudsakliga funktionerna: proxy till IoT-hubb och lokala kommunikation.

### <a name="iot-hub-primitives"></a>IoT-hubb primitiver
IoT-hubb ser en modul-instans på samma sätt till en enhet i meningen att den:

* den har en modul dubbla som är separat och isolerade från den [enheten dubbla] [ lnk-devicetwin] och modulen twins av denna enhet;
* Skicka [meddelanden från enhet till moln][lnk-iothub-messaging];
* Det kan ta emot [direkt metoder] [ lnk-methods] specifikt riktat sin identitet.

För närvarande en modul kan inte ta emot meddelanden moln till enhet inte heller använda funktionen överför filen.

När du skriver en modul kan du använda den [Azure IoT-enhet SDK] [ lnk-devicesdk] att ansluta till IoT Edge-hubb och använda funktionen ovan precis som när du använder IoT-hubb med ett enhetsprogram, den enda skillnaden är att, från ditt program backend-, du måste referera till modulen identitet i stället för enhetens identitet.

Se [utveckla och distribuera en IoT-Edge-modul till en simulerad enhet] [ lnk-tutorial2] ett exempel på en modul-program som skickar meddelanden från enhet till moln och använder modulen dubbla.

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet
För att aktivera avancerade bearbetning av meddelanden från enhet till moln, kant för IoT-hubb ger deklarativ routning av meddelanden mellan moduler och mellan moduler och IoT-hubb.
Detta gör att avlyssna-moduler och bearbeta meddelanden som skickas av andra moduler och sprider dem till komplexa pipeline.
Artikeln [modulsammansättningen] [ lnk-module-comp] förklarar hur du skriver moduler i komplexa pipelines som använder vägar.

En IoT-Edge-modul annorlunda än en normal IoT-hubb enhetsprogram ta emot meddelanden från enhet till moln som har via proxy i sin lokala IoT Edge hub för att bearbeta dem.

Gräns för IoT-hubb sprider meddelanden till din modul baserat på deklarativ vägar som beskrivs i den [modulsammansättningen] [ lnk-module-comp] artikel. När du utvecklar en IoT-Edge-modul, du kan ta emot dessa meddelanden genom att ange meddelandehanterare för, som visas i kursen [utveckla och distribuera en IoT-Edge-modul till en simulerad enhet][lnk-tutorial2].

För att förenkla skapandet av vägar, IoT kant lägger till begreppet modulen *inkommande* och *utdata* slutpunkter. En modul kan ta emot alla meddelanden från enhet till moln dirigeras till den utan att ange några indata och kan skicka meddelanden från enhet till moln utan att ange några utdata.
Med hjälp av explicita indata och utdata, men gör routningsregler enklare att förstå. Se [modulsammansättningen] [ lnk-module-comp] för mer information om regler för Routning och indata- och slutpunkter för moduler.

Slutligen är enhet till moln meddelanden som hanteras i Edge-hubben stämplad med följande egenskaper för system:

| Egenskap | Beskrivning |
| -------- | ----------- |
| $connectionDeviceId | Enhets-ID för den klient som skickade meddelandet |
| $connectionModuleId | Modul-ID på den modul som skickade meddelandet |
| $inputName | Indata som tog emot detta meddelande. Kan vara tom. |
| $outputName | Utdata som används för att skicka meddelandet. Kan vara tom. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluter till IoT Edge-hubb från en modul
Ansluter till lokala kant för IoT-hubben från en modul består av två steg: använder anslutningssträngen angivna genom IoT kant runtime när modulen startar och kontrollera att tillämpningsprogrammet accepterar certifikatet som presenterades kant för IoT-hubb på enheten.

Anslutande strängen som ska användas är matas in av IoT kant körningen i miljövariabeln `EdgeHubConnectionString`. Detta gör den tillgänglig för alla program som du vill använda den.

På samma sätt kan certifikatet du använder för att verifiera IoT Edge hub anslutningen införs av IoT kant-körning i en fil vars sökväg är tillgängliga i miljövariabeln `EdgeModuleCACertificateFile`.

Kursen [utveckla och distribuera en IoT-Edge-modul till en simulerad enhet] [ lnk-tutorial2] visar hur du kan kontrollera att certifikatet finns i datorarkivet i din modul-program. Uppenbarligen någon annan metod ska lita på anslutningar med som certifikat fungerar.

## <a name="packaging-as-an-image"></a>Paket som en bild
IoT-Edge moduler paketeras som Docker bilder.
Du kan använda Docker toolchain direkt eller Visual Studio-koden som visas i kursen [utveckla och distribuera en IoT-Edge-modul till en simulerad enhet][lnk-tutorial2].

## <a name="next-steps"></a>Nästa steg

När du utvecklar en modul kan lära dig hur du [distribuera och övervaka IoT kant moduler i skala][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
