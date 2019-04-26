---
title: StorSimple Virtual Array etableringstjänstens sammanfattningsblad | Microsoft Docs
description: Beskriver sammanfattningsbladet för tjänsten för StorSimple Device Manager och som förklarar hur du använder den för att övervaka hälsotillståndet för StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337935"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använd sammanfattningsbladet för tjänsten för StorSimple Device Manager är ansluten till StorSimple Virtual Array
## <a name="overview"></a>Översikt
Sammanfattningsbladet för tjänsten för StorSimple Device Manager innehåller en sammanfattning av de StorSimple Virtual Array (även kallat StorSimple lokala virtuella enheter eller virtuella enheter) som är anslutna till din tjänst, om du markerar dem som behöver ett system administratörens uppmärksamhet. Den här självstudien introducerar sammanfattningsbladet för tjänsten, förklarar innehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från det här bladet.

![Instrumentpanel](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Kommandon för hantering och essentials
På sammanfattningsbladet för StorSimple visas alternativ för att hantera din StorSimple Device Manager-tjänst samt virtuella matriser som registrerats för den här tjänsten. Du kan se kommandon för hantering överst på bladet och till vänster.

Använd dessa alternativ för att utföra olika åtgärder som lägger till resurser eller volymer eller övervaka de olika jobb som körs på virtuella matriser.

Området essentials samlar in några av de viktiga egenskaperna som resursgruppens namn, plats och prenumeration som StorSimple Device Manager skapades.

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av StorSimple Device Manager-tjänst
* Den **aviseringar** panel ger en ögonblicksbild av alla aktiva aviseringar över alla virtuella enheter, grupperat efter allvarlighetsgrad. Om du klickar på panelen öppnas den **aviseringar** bladet där du kan klicka på en enskild varning att visa ytterligare information om den här aviseringen, inklusive alla rekommenderade åtgärder. Du kan även radera aviseringen om problemet har lösts.
* Den **kapacitet** panel visar visar den primära lagringen som är upprättad och återstående på alla virtuella enheter i förhållande till det totala lagringsutrymmet som är tillgängliga på alla virtuella enheter. **Etablerade** refererar till det lagringsutrymme som är förberedd och är allokerad för användning, **återstående** refererar till den återstående kapacitet som kan etableras på alla virtuella enheter. Den **återstående Nivåindelad** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är den kapacitet kvar på diskar som är anslutna till virtuella matriser.
* I den **användning** diagrammet, du kan se mått som är relevanta för din virtuella enheter. Du kan visa den primära lagringen som används på alla virtuella enheter, samt molnlagring som används av virtuella enheter under de senaste 7 dagarna standard tidsperiod. Använd den **redigera** alternativet i det övre högra hörnet i diagrammet för att välja en annan tidsskala.
* Den **enheter** panelen innehåller en sammanfattning av hur många virtuella matriser i StorSimple-Enhetshanteraren grupperade efter enhetens status. Klicka på den här panelen för att öppna den **enheter** listan bladet och klicka sedan på en enskild enhet att visa detaljerad information om sammanfattningen för enheter som är specifik för enheten. Du kan också utföra specifika åtgärder för enhet från en given enhet sammanfattningsbladet. Mer information om sammanfattningsbladet för enheten går du till [sammanfattningsbladet för Device](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Visa aktivitetsloggar
Om du vill visa de olika åtgärderna som utförs inom din StorSimple Device Manager klickar du på den **aktivitetsloggar** länk på vänster sida av din sammanfattningsbladet för StorSimple-tjänsten. Då kommer du till den **aktivitetsloggar** bladet, där du kan se en sammanfattning av de senaste genomförda transaktioner.

![Aktivitetsloggar](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [använda det lokala webbgränssnittet för att administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

