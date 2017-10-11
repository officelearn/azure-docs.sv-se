---
title: StorSimple virtuell matris service sammanfattning bladet | Microsoft Docs
description: "Beskriver bladet service sammanfattning för StorSimple Enhetshanteraren och förklarar hur du använder den för att övervaka hälsotillståndet för din virtuella StorSimple-matris."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använd service sammanfattning bladet för StorSimple Enhetshanteraren anslutna till virtuella StorSimple-matris
## <a name="overview"></a>Översikt
Bladet service sammanfattning för StorSimple Device Manager innehåller en sammanfattning av StorSimple virtuell matriserna (även kallat StorSimple lokala virtuella enheter eller virtuella enheter) som är anslutna till din tjänst, markera dem som behöver du ett system administratörens uppmärksamhet. Den här kursen introducerar bladet service sammanfattning, förklarar innehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från det här bladet.

![Instrumentpanel](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Hantering av kommandon och essentials
I bladet StorSimple sammanfattning visas alternativ för att hantera din StorSimple Enhetshanteraren tjänsten samt virtuella matriser som registrerats för den här tjänsten. Du kan se kommandona management överst på bladet och till vänster.

Använd dessa alternativ för att utföra olika åtgärder som kan du lägga till resurser eller volymer eller övervaka olika jobb som körs på de virtuella matriserna.

Området essentials samlar in några viktiga egenskaper som resursgruppens namn, plats och prenumeration som din StorSimple-Enhetshanteraren skapades.

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av StorSimple Enhetshanteraren tjänst
* Den **aviseringar** panelen ger en ögonblicksbild av alla aktiva aviseringar över alla virtuella enheter, grupperat efter allvarlighetsgrad. Om du klickar på ikonen öppnas den **aviseringar** bladet där du kan klicka på en enskild varning för att visa ytterligare information om den här aviseringen, inklusive de rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.
* Den **kapacitet** panelen visar visar den primära lagring som är allokerade och återstående över alla virtuella enheter i förhållande till det totala lagringsutrymmet som är tillgängliga i alla virtuella enheter. **Etablerad** avser mängden lagringsutrymme som är förberedd och tilldelat för användning, **återstående** refererar till den återstående kapacitet som kan etableras på alla virtuella enheter. Den **återstående nivåer** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är kapacitet kvar på diskar som är kopplade till virtuella matriser.
* I den **användning** diagram, du kan se mätvärdena som är relevanta för din virtuella enheter. Du kan visa den primära lagringsutrymme som används på alla virtuella enheter, samt molnlagring som används av virtuella enheter under de senaste 7 dagarna standard tidsperiod. Använd den **redigera** alternativ i det övre högra hörnet i diagrammet att välja en annan tidsskala.
* Den **enheter** panelen innehåller en sammanfattning av antalet virtuella matriser i din StorSimple Enhetshanteraren grupperade efter status för enheter. Klicka på den här rutan för att öppna den **enheter** bladet och klicka sedan på en enskild enhet att visa detaljerad information om sammanfattningen för enheter som är specifik för enheten. Du kan också utföra särskilda åtgärder för enhet från en viss enhet sammanfattning-bladet. Mer information om enheten sammanfattning bladet går du till [enheten sammanfattning bladet](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Kontrollera händelseloggarna för aktiviteten
Om du vill visa olika åtgärder som utförs inom din StorSimple-Enhetshanteraren, klickar du på den **aktivitetsloggar** länk på vänster sida av din StorSimple-tjänsten sammanfattning-bladet. Då kommer du att den **aktivitetsloggar** bladet, där du kan se en översikt över de senaste genomförda transaktioner.

![Aktivitetsloggar](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [använda lokala webbgränssnittet för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

