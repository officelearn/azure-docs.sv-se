---
title: Hur du använder paneler i instrumentpanelen för Azure IoT Central program | Microsoft Docs
description: Lär dig hur du använder paneler på instrumentpaneler för programmet, enheten set instrumentpaneler och enheten instrumentpaneler som ett verktyg.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503531"
---
# <a name="how-to-use-tiles"></a>Hur du använder paneler
Du kan använda paneler för att anpassa programmet instrumentpaneler, enheten instrumentpaneler, och enheten inställd instrumentpaneler. Du kan lägga till flera paneler på en instrumentpanel och anpassa dessa paneler för att visa information som är relevanta för ditt program. Du kan också ändra storlek på paneler och anpassa layouten på valfri instrumentpanel. I skärmbilden nedan visar instrumentpanelen för program med olika paneler.

![Instrumentpanel för program](media/howto-use-tiles/image1a.png)


I följande tabell sammanfattas användningen av paneler i Azure IoT Central:

 
| panelen | Instrumentpanel | Beskrivning
| ----------- | ------- | ------- |
| Länk | Program- och enhetshantering konfigurerar instrumentpaneler |Länken paneler är klickbara rutor som visar text för rubrik och beskrivning. Använda en panel på länken för att aktivera en användare att navigera till en URL som är relaterade till ditt program. |
| Image | Program- och enhetshantering konfigurerar instrumentpaneler |Avbildningspaneler visa en anpassad avbildning och kan vara klickbara. Använd en bildpanel för att lägga till grafik på en instrumentpanel och välja att aktivera en användare att navigera till en URL som är relevanta för ditt program.|
| Label (Etikett) | Programmet instrumentpaneler |Etikett paneler visas anpassad text på en instrumentpanel. Du kan välja storleken på texten. Använda en etikett panel att lägga till relevant information på instrumentpanelen sådana beskrivningar, kontaktinformation eller hjälp|
| Karta | Program- och enhetshantering konfigurerar instrumentpaneler |Paneler för kartan visar platsen och tillståndet för en enhet på en karta. Du kan till exempel visa där en enhet är och om fläkten är påslagen.|
| Linjediagram | Program- och enhetshantering instrumentpaneler |Linjediagramspaneler visa ett diagram över sammanställd mått för en enhet för en viss tidsperiod. Du kan till exempel visa ett linjediagram som visar genomsnittlig temperatur och tryck på en enhet för den senaste timmen|
| Liggande stapeldiagram | Program- och enhetshantering instrumentpaneler |Stapeldiagram paneler visa ett diagram över sammanställd mätning av faktisk användning för en enhet under en tidsperiod. Du kan till exempel visa ett stapeldiagram som visar genomsnittlig temperatur och tryck på en enhet för den senaste timmen |
| Händelsehistorik | Program- och enhetshantering instrumentpaneler |Händelsen historik panelen visar händelser för en enhet under en tidsperiod. Du kan till exempel använda den för att visa alla temperaturförändringarna uppstod för en enhet under den senaste timmen. |
| Tillståndshistorik | Program- och enhetshantering instrumentpaneler |Tillståndshistorik panelen visar mått på värden för en viss tidsperiod. Du kan till exempel använda den för att visa temperaturvärden för en enhet under den senaste timmen.|
| KPI | Program- och enhetshantering instrumentpaneler | KPI-panelen visar en sammanställd telemetri- eller händelse mått för en viss tidsperiod. Du kan till exempel använda den för att visa maximala temperaturen har nåtts för en enhet under den senaste timmen.|
| Senaste kända värdet | Program- och enhetshantering instrumentpaneler |Senaste kända värdet-panel visar det senaste värdet för en telemetri- eller tillstånd mätning. Du kan till exempel använda den här panelen för att visa de senaste mätningarna av temperatur och tryck och fuktighet för en enhet.|
| Enheten Set rutnät | Program- och enhetshantering konfigurerar instrumentpaneler | Enheten inställd grid visar information om enheten. Använd en enhetsuppsättning grid panel för att visa information, till exempel namn, plats och modell för alla enheter i enhetsuppsättningen.|


Mer information om hur du konfigurerar instrumentpanelen i Azure IoT Central programmet finns [konfigurera programmet instrumentpanelen](howto-configure-homepage.md).
