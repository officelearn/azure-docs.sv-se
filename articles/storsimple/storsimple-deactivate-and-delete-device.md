---
title: Inaktivera och ta bort en StorSimple-enhet | Microsoft Docs
description: "Beskriver hur du tar bort StorSimple-enhet från tjänsten genom att först inaktivera det och sedan ta bort den."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c000a642aa088ac80cc7077453b87e9a47f96900
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-8000-series-device-via-storsimple-manager-service"></a>Inaktivera och ta bort en enhet med StorSimple 8000-serien via StorSimple Manager-tjänsten
## <a name="overview"></a>Översikt
Du kanske vill vidta en StorSimple-enhet out-of-service (till exempel om du ersätter eller uppgradera din enhet eller om du inte längre använder StorSimple). Om så är fallet kommer du behöva inaktivera enheten innan du kan ta bort den. Inaktivera servrarna anslutningen mellan enheten och motsvarande StorSimple Manager-tjänsten. Den här självstudiekursen beskrivs hur du tar bort en StorSimple-enhet från tjänsten genom att först avaktivera den och sedan ta bort den. 

När du inaktiverar en enhet kan kommer alla data som lagras lokalt på enheten inte längre tillgänglig. Endast data som är kopplade till den enhet som lagrades i molnet kan återställas.  

> [!WARNING]
> Inaktivering är en PERMANENT åtgärd och kan inte ångras. En inaktiverad enhet kan inte registreras med StorSimple Manager-tjänsten om den första återställs till fabriksinställningar. 
> 
> Fabriksåterställa processen tar bort alla data som lagras lokalt på din enhet. Det är därför viktigt att ta en ögonblicksbild av alla data i molnet innan du inaktiverar en enhet. Detta kan du återställa alla data i ett senare skede.
> 
> 

Den här självstudiekursen beskrivs hur du:

* Inaktivera en enhet och ta bort data
* Inaktivera en enhet och behålla data

Här förklaras också hur inaktivera och ta bort fungerar på en virtuell StorSimple-enhet.

> [!NOTE]
> Se till att stoppa eller ta bort klienter och värdar som är beroende av enheten innan du inaktiverar en StorSimple fysisk eller virtuell enhet.
> 
> 

## <a name="deactivate-and-delete-data"></a>Inaktivera och ta bort data
Slutför följande steg om du är intresserad av att ta bort enheten fullständigt och inte vill behålla data på enheten.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Att inaktivera enheten och ta bort data
1. Innan du inaktiverar en enhet, måste du ta bort alla volymen behållare (och volymer) som är kopplade till enheten. Du kan ta bort volymbehållare endast när du tar bort tillhörande säkerhetskopior.
2. Inaktivera enheten enligt följande:
   
   1. På StorSimple Manager-tjänsten **enheter** markerar du den enhet som du vill inaktivera och längst ned på sidan klickar du på **inaktivera**.
   2. Ett bekräftelsemeddelande visas. Klicka på **Ja** att fortsätta. Inaktivera processen startar och ta några minuter att slutföra.
3. Efter inaktiveringen, kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Använd följande steg för att ta bort enheten:
   
   1. På StorSimple Manager-tjänsten **enheter** väljer du en inaktiverad enhet som du vill ta bort.
   2. Klicka på längst ned på sidan **ta bort**.
   3. Du uppmanas att bekräfta. Klicka på **Ja** att fortsätta.
      
      Det kan ta några minuter för enheten som ska tas bort.

## <a name="deactivate-and-retain-data"></a>Inaktivera och behålla data
Om du är intresserad av att ta bort enheten, men du vill behålla data, gör du följande.

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Inaktivera en enhet och behålla data
1. Inaktivera enheten. Alla volymbehållare och ögonblicksbilder på enheten som finns kvar.
   
   1. På StorSimple Manager-tjänsten **enheter** markerar du den enhet som du vill inaktivera och längst ned på sidan klickar du på **inaktivera**.
   2. Ett bekräftelsemeddelande visas. Klicka på **Ja** att fortsätta. Inaktivera processen startar och ta några minuter att slutföra.
2. Nu kan du växla över volymbehållarna och associerade ögonblicksbilder. Mer information om procedurer går du till [redundans och disaster recovery för din StorSimple-enhet](storsimple-device-failover-disaster-recovery.md).
3. Efter inaktivering och växling vid fel, kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Utför följande steg för att ta bort enheten:
   
   1. På StorSimple Manager-tjänsten **enheter** väljer du en inaktiverad enhet som du vill ta bort.
   2. Klicka på längst ned på sidan **ta bort**.
   3. Du uppmanas att bekräfta. Klicka på **Ja** att fortsätta.
      
      Det kan ta några minuter för enheten som ska tas bort.

## <a name="deactivate-and-delete-a-virtual-device"></a>Inaktivera och ta bort en virtuell enhet
För en virtuell StorSimple-enhet inaktiveringen tar du bort den virtuella datorn. Du kan sedan ta bort den virtuella datorn och de resurser som skapades när den etablerades. När den virtuella enheten inaktiveras kan den inte återställas till sitt tidigare tillstånd. 

Inaktivera resulterar i följande åtgärder:

* Den virtuella StorSimple-enheten tas bort.
* OSDisk och Datadiskar som skapats för den virtuella StorSimple-enheten tas bort.
* Värdbaserad tjänst och virtuella nätverk som skapades under etableringen bevaras. Om du inte använder dessa enheter, bör du ta bort dem manuellt.
* Molnögonblicksbilder som skapats av den virtuella enheten StorSimple finns kvar.

## <a name="next-steps"></a>Nästa steg
* Om du vill återställa inaktiverade enheten till fabriksinställningarna, gå till [återställa enheten till fabriksinställningarna](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* För teknisk support [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).
* Mer information om hur du använder StorSimple Manager-tjänsten går du till [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md). 

