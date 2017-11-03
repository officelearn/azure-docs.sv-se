---
title: "Azure IoT-hubb så | Microsoft Docs"
description: "Som en utvecklare hur använder jag de olika funktionerna i IoT-hubb?"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: b451ea2d42f0ff2f64746bcb296fb9128472fea8
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="how-to-use-azure-iot-hub"></a>Hur du använder Azure IoT-hubb

Har du olika alternativ för att lära dig hur du utvecklar för IoT-hubb-tjänsten:

* Läsa begreppsrelaterade artiklar som beskriver funktionerna i IoT-hubb i detalj.
* Följ någon av följande självstudiekurser som beskriver de olika funktionerna i IoT-hubb.

## <a name="developer-guide"></a>Utvecklarguide

Som en utvecklare kan du läsa detaljerad konceptuell information om IoT-hubb i den [Utvecklarhandbok][lnk-devguide]. Den här guiden innehåller:

* Detaljerade beskrivningar av alla IoT-hubb-funktioner som hjälper dig att lära dig hur du använder dem.
* Anvisningar om hur du väljer när det finns flera alternativ.

## <a name="tutorials"></a>Självstudier

Om du föredrar att lära dig om specifika IoT-hubb-funktioner genom att utföra praktiska övningarna finns flera självstudiekurser för att välja bland. Många av dessa självstudiekurser är tillgängliga i flera programmeringsspråk. Dessa självstudiekurser är:

- [IoT-hubb enhet till moln-meddelanden med hjälp av vägar][lnk-routes-tutorial]. Den här kursen visar hur du använder regler för routning av IoT-hubb för att skicka meddelanden från enhet till moln i ett enkelt och konfigurationsbaserade sätt.

- [Skicka meddelanden moln till enhet med IoT-hubb][lnk-c2d-tutorial]. Den här kursen visar hur du skickar meddelanden moln till enhet via IoT-hubb och ta emot meddelanden moln till enhet på en enhet.

- [Ladda upp filer från enheter till molnet med IoT-hubben][lnk-upload-tutorial]. Den här kursen visar hur du använder filen överför funktionerna i IoT-hubb.

- [Kom igång med enheten twins][lnk-twin-tutorial]. Den här självstudiekursen presenteras enheten twins, rapporterade egenskaper, önskade egenskaper och taggar. Du kan använda enheten twins för att synkronisera värdena med dina enheter.

- [Använda direkt metoder][lnk-methods-tutorial]. Den här kursen visar hur du använder direkta metoder. Du lägger till en hanterare för en direkt metod i den simulerade enheten och anropa metoden direkt från IoT-hubb.

- [Kom igång med enhetshantering][lnk-dm-tutorial]. Den här kursen visar hur du använder viktiga enhetshanteringsfunktioner, till exempel twins och direkt metoder. Du kan använda dessa funktioner för att starta om den simulerade enheten via fjärranslutning.

- [Använd önskade egenskaper för att konfigurera enheter][lnk-properties-tutorial]. Den här kursen visar du hur för att använda enheten dubblas önskad och rapporterade egenskaper, till via fjärranslutning konfigurera din enhet.

- [Använder enhet jobb för att initiera en firmware-uppdatering för enheten][lnk-jobs-tutorial]. Den här kursen visar hur du använder viktiga enhetshanteringsfunktioner, till exempel twins och direkt metoder. Du lär dig hur du använder dessa funktioner för fjärruppdatering enhetens inbyggda programvara.

- [Schema-och broadcast][lnk-schedule-tutorial]. Den här kursen visar hur du använder egenskaper och metoder som direkt för att interagera med flera enheter vid ett schemalagt klockslag.

## <a name="next-steps"></a>Nästa steg

Mer information om tjänsten IoT-hubb finns i [Utvecklarhandbok][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md