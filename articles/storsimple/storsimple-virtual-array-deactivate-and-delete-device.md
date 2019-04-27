---
title: Inaktivera och ta bort en Microsoft Azure StorSimple Virtual Array | Microsoft Docs
description: Beskriver hur du tar bort StorSimple-enhet från tjänsten genom att inaktivera det först och sedan ta bort den.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580636"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Inaktivera och ta bort en StorSimple Virtual Array

## <a name="overview"></a>Översikt

När du inaktiverar en StorSimple Virtual Array kan bryta anslutningen mellan enheten och motsvarande StorSimple Device Manager-tjänsten. I den här självstudien beskrivs hur du:

* Inaktivera en enhet 
* Ta bort en inaktiverad enhet

Informationen i den här artikeln gäller bara för StorSimple Virtual Array. För information om 8000-serien, går du till hur du [inaktivera eller ta bort en enhet](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>När du ska inaktivera?

Inaktiveringen är en PERMANENT åtgärd och kan inte ångras. Du kan inte registrera en inaktiverad enhet med StorSimple Device Manager-tjänsten igen. Du kan behöva inaktivera och ta bort en StorSimple Virtual Array i följande scenarier:

* **Planerad redundans** : Enheten är ansluten och du planerar att redundansväxla din enhet. Om du planerar att uppgradera till en större enhet, kan du behöva redundansväxla din enhet. När data äganderätten överförs och redundansen är klar, tas källenheten bort automatiskt.
* **Oplanerad redundans** : Enheten är offline och du behöver att växla över enheten. Det här scenariot kan uppstå vid ett haveri när det uppstår ett avbrott i datacentret och den primära enheten inte är igång. Du planerar att redundansväxla enheten till en sekundär enhet. När data äganderätten överförs och redundansen är klar, tas källenheten bort automatiskt.
* **Inaktivera** : Du vill inaktivera enheten. Detta måste du först inaktivera enheten och tar bort den. När du inaktiverar en enhet kan du inte längre komma åt alla data som lagras lokalt. Du kan bara komma åt och återställa data som lagras i molnet. Om du planerar att hålla enhetsdata efter inaktiveringen, bör du ta en molnögonblicksbild av alla dina data innan du inaktiverar en enhet. Den här ögonblicksbilden av molndata kan du återställa alla data i ett senare skede.

## <a name="deactivate-a-device"></a>Inaktivera en enhet

Utför följande steg för att inaktivera enheten.

#### <a name="to-deactivate-the-device"></a>Så här inaktiverar du enheten

1. I din tjänst, går du till **Management > enheter**. I den **enheter** bladet klickar du på och välj den enhet som du vill inaktivera.
   
    ![Välj enhet för att inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. I din **instrumentpanelen** bladet klickar du på **... Mer** och i listan, Välj **inaktivera**.
   
    ![Klicka på Inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. I den **inaktivera** bladet skriver du namnet på enheten och klicka sedan på **inaktivera**. 
   
    ![Bekräfta inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Inaktivera processen startar och tar några minuter att slutföra.
   
    ![Inaktivera pågår](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Efter inaktiveringen uppdaterar listan över enheter.
   
    ![Inaktivera fullständig](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Du kan nu ta bort den här enheten.

## <a name="delete-the-device"></a>Ta bort enheten

En enhet har inaktiveras först om du vill ta bort den. Tar bort en enhet tar du bort den från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Data som hör till enheten men förblir i molnet. Dessa data sedan tillkommer avgifter.

Utför följande steg för att ta bort enheten.

#### <a name="to-delete-the-device"></a>Att ta bort enheten

1. I StorSimple-Enhetshanteraren, gå till **Management > enheter**. I den **enheter** bladet Välj en inaktiverad enhet som du vill ta bort.
2. I den **instrumentpanelen** bladet klickar du på **... Mer** och klicka sedan på **ta bort**.
   
   ![Välj enhet för att ta bort](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. I den **ta bort** bladet anger du namnet på enheten för att bekräfta borttagningen och klickar sedan på **ta bort**. Tar bort enheten tas inte bort molndata som hör till enheten. 
   
   ![Bekräfta borttagning](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Borttagningen startar och tar några minuter att slutföra.
   
   ![Borttagning pågår](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    När enheten har tagits bort, kan du visa den uppdaterade listan med enheter.

## <a name="next-steps"></a>Nästa steg

* För information om hur du växlar över, går du till [redundans och återställning vid återställning av StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md).

* Om du vill veta mer om hur du använder StorSimple Device Manager-tjänsten kan du gå till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md). 

