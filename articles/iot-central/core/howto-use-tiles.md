---
title: Använda paneler i instrument panelen för Azure IoT Central-program | Microsoft Docs
description: Som ett verktyg kan du läsa om hur du använder paneler på instrument paneler för program, enhets uppsättnings instrument paneler och enhets instrument paneler.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952633"
---
# <a name="how-to-use-tiles"></a>Så här använder du paneler
Du kan använda paneler för att anpassa program instrument paneler, enhets instrument paneler och enhets uppsättnings instrument paneler. Du kan lägga till flera paneler i en instrument panel och anpassa dessa paneler för att visa information som är relevant för ditt program. Du kan också ändra storlek på paneler och anpassa layouten på vilken instrument panel som helst. Skärm bilden nedan visar program instrument panelen med olika paneler.

![Instrumentpanel för program](media/howto-use-tiles/image1a.png)


I följande tabell sammanfattas användningen av paneler i Azure IoT Central:

 
| Hörnruta | Instrumentpanel | Beskrivning
| ----------- | ------- | ------- |
| Länk | Instrument paneler för program-och enhets uppsättning |Länk paneler är klicknings bara paneler som visar rubrik och beskrivnings text. Använd en länk panel för att göra det möjligt för en användare att navigera till en URL som är relaterad till ditt program. |
| Bild | Instrument paneler för program-och enhets uppsättning |Bild paneler visar en anpassad bild och går att klicka på. Använd en bild panel för att lägga till grafik på en instrument panel och om du vill kan en användare navigera till en URL som är relevant för ditt program.|
| Etikett | Instrument paneler för program |Etikett paneler visar anpassad text på en instrument panel. Du kan välja storlek på texten. Använd en etikett panel för att lägga till relevant information på instrument panelen, till exempel beskrivningar, kontakt uppgifter eller hjälp|
| Karta | Instrument paneler för program-och enhets uppsättning |Kart paneler visar plats och status för en enhet på en karta. Du kan till exempel Visa var en enhet är och om fläkten är avstängd.|
| Linje diagram | Instrument paneler för program och enheter |Linje diagram paneler visar ett diagram över mängd mått för en enhet under en tids period. Du kan till exempel visa ett linje diagram som visar genomsnitts temperaturen och trycket på en enhet under den senaste timmen|
| Stapeldiagram | Instrument paneler för program och enheter |I stapeldiagram visas ett diagram över sammanställda mått för en enhet under en tids period. Du kan till exempel visa ett stapeldiagram som visar genomsnitts temperaturen och trycket på en enhet under den senaste timmen |
| Händelse historik | Instrument paneler för program och enheter |Panelen händelse historik visar händelserna för en enhet under en viss tids period. Du kan till exempel använda den för att visa alla temperatur ändringar som gjorts för en enhet under den senaste timmen. |
| Tillstånds historik | Instrument paneler för program och enheter |I panelen tillstånds historik visas mått värden för en tids period. Du kan till exempel använda den för att visa temperatur värden för en enhet under den senaste timmen.|
| KPI | Instrument paneler för program och enheter | KPI-panelen visar en sammanställd telemetri eller händelse mätning för en tids period. Du kan till exempel använda den för att visa den maximala temperatur som nåtts för en enhet under den senaste timmen.|
| Senast kända värde | Instrument paneler för program och enheter |Panelen senast känt värde visar det senaste värdet för en telemetri-eller tillstånds mätning. Du kan till exempel använda den här panelen för att visa de senaste mätningarna av temperatur, tryck och fuktighet för en enhet.|
| Rutnät för enhets uppsättning | Instrument paneler för program-och enhets uppsättning | I rutnätet för enhets uppsättning visas information om enhets uppsättningen. Använd en panel för enhets uppsättnings rutnät för att visa information som namn, plats och modell för alla enheter i enhets uppsättningen.|


Mer information om hur du konfigurerar instrument panelen i ditt Azure IoT Central-program finns i [lägga till paneler på instrument panelen](./howto-add-tiles-to-your-dashboard.md).
