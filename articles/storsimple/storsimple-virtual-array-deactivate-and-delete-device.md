---
title: Inaktivera och ta bort en Virtuell Microsoft Azure-storsimple-matris | Microsoft-dokument
description: Beskriver hur du tar bort StorSimple-enheten från tjänsten genom att först inaktivera den och sedan ta bort den.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: bb1a56d204a46f89213f20e317494120f0ea565e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580636"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Inaktivera och ta bort en virtuell StorSimple-matris

## <a name="overview"></a>Översikt

När du inaktiverar en StorSimple Virtual Array bryter du anslutningen mellan enheten och motsvarande StorSimple-enhetshanterarens tjänst. I den här självstudien beskrivs hur du:

* Inaktivera en enhet 
* Ta bort en inaktiverad enhet

Informationen i den här artikeln gäller endast StorSimple Virtual Arrays. Information om 8000-serien finns i hur du [inaktiverar eller tar bort en enhet](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>När ska du inaktivera?

Inaktivering är en PERMANENT-åtgärd och kan inte ångras. Du kan inte registrera en inaktiverad enhet med Tjänsten StorSimple Device Manager igen. Du kan behöva inaktivera och ta bort en StorSimple Virtual Array i följande scenarier:

* **Planerad redundans:** Enheten är online och du planerar att växla över enheten. Om du planerar att uppgradera till en större enhet kan du behöva växla över enheten. När dataägarskapet har överförts och redundansen är klar tas källenheten automatiskt bort.
* **Oplanerad redundans:** Enheten är offline och du måste växla över enheten. Det här scenariot kan inträffa vid en katastrof när det finns ett avbrott i datacentret och din primära enhet är nere. Du planerar att växla över enheten till en sekundär enhet. När dataägarskapet har överförts och redundansen är klar tas källenheten automatiskt bort.
* **Inaktivera** : Du vill inaktivera enheten. Detta kräver att du först inaktiverar enheten och sedan tar bort den. När du inaktiverar en enhet kan du inte längre komma åt data som lagras lokalt. Du kan bara komma åt och återställa data som lagras i molnet. Om du planerar att behålla enhetsdata efter avaktivering, bör du ta en ögonblicksbild av alla dina data innan du inaktiverar en enhet. Med den här ögonblicksbilden av molnet kan du återställa alla data i ett senare skede.

## <a name="deactivate-a-device"></a>Inaktivera en enhet

Gör följande om du vill inaktivera enheten.

#### <a name="to-deactivate-the-device"></a>Så här inaktiverar du enheten

1. Gå till Hantering **> enheter i**din tjänst . Klicka på och välj den enhet som du vill inaktivera i bladet **Enheter.**
   
    ![Välj enhet som ska inaktiveras](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. I **instrumentpanelsbladet enhet** klickar du på **... Mer** och välj **Inaktivera**i listan .
   
    ![Klicka på inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Skriv enhetsnamnet i bladet **Inaktivera** och klicka sedan på **Inaktivera**. 
   
    ![Bekräfta inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Avaktiveringsprocessen startar och tar några minuter att slutföra.
   
    ![Inaktivera pågående](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Efter inaktivering uppdateras listan över enheter.
   
    ![Inaktivera fullständig](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Du kan nu ta bort den här enheten.

## <a name="delete-the-device"></a>Ta bort enheten

En enhet måste först inaktiveras för att den ska kunna tas bort. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan då inte längre hantera den borttagna enheten. Data som är associerade med enheten finns dock kvar i molnet. Dessa data samlar sedan in avgifter.

Om du vill ta bort enheten utför du följande steg.

#### <a name="to-delete-the-device"></a>Så här tar du bort enheten

1. Gå till Hantering > enheter **i**StorSimple-enhetshanteraren . I bladet **Enheter** väljer du en inaktiverad enhet som du vill ta bort.
2. I **instrumentpanelsbladet enhet** klickar du på **... Mer** och klicka sedan på **Ta bort**.
   
   ![Välj enhet som ska tas bort](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Skriv namnet på enheten i bladet **Ta bort** för att bekräfta borttagningen och klicka sedan på **Ta bort**. Om du tar bort enheten tas inte molndata som är associerade med enheten bort. 
   
   ![Bekräfta borttagning](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Borttagningen startar och tar några minuter att slutföra.
   
   ![Ta bort pågående](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    När enheten har tagits bort kan du visa den uppdaterade listan över enheter.

## <a name="next-steps"></a>Nästa steg

* Information om hur du växlar över går du till [Redundans och haveriberedskap för den virtuella storsimple-matrisen](storsimple-virtual-array-failover-dr.md).

* Om du vill veta mer om hur du använder Tjänsten StorSimple Device Manager går du till [Använd Tjänsten StorSimple Device Manager för att administrera din Virtuella StorSimple-matris](storsimple-virtual-array-manager-service-administration.md). 

