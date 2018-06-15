---
title: Inaktivera och ta bort en virtuell matris för Microsoft Azure StorSimple | Microsoft Docs
description: Beskriver hur du tar bort StorSimple-enhet från tjänsten genom att först inaktivera det och sedan ta bort den.
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
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875832"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Inaktivera och ta bort en virtuell StorSimple-matris

## <a name="overview"></a>Översikt

När du inaktiverar en virtuell StorSimple-matris bryta anslutningen mellan enheten och motsvarande StorSimple enheten Manager-tjänsten. Den här självstudiekursen beskrivs hur du:

* Inaktivera en enhet 
* Ta bort en inaktiverad enhet

Informationen i den här artikeln gäller bara för virtuella StorSimple-matriser. För information om 8000-serien, gå till [inaktivera eller ta bort en enhet](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>När du ska inaktivera?

Inaktivering är en PERMANENT åtgärd och kan inte ångras. Du kan inte registrera en inaktiverad enhet med StorSimple Device Manager-tjänsten igen. Du kan behöva inaktivera och ta bort en virtuell StorSimple-matris i följande scenarier:

* **Planerad redundans** : enheten är online och du planerar att växla över din enhet. Om du planerar att uppgradera till en större enhet, kan du behöva växla över din enhet. När data äganderätten överförs och växling vid fel är klar tas på källenheten bort automatiskt.
* **Oplanerad växling** : enheten är offline och du måste växla över enheten. Det här scenariot kan uppstå under en katastrofåterställning när det sker ett avbrott i datacentret och den primära enheten är inte tillgänglig. Du planerar att växla över enheten till en sekundär enhet. När data äganderätten överförs och växling vid fel är klar tas på källenheten bort automatiskt.
* **Inaktivera** : du vill inaktivera enheten. Detta måste du först inaktivera enheten och tar bort den. När du inaktiverar en enhet kan du inte längre komma åt alla data som lagras lokalt. Du kan bara komma åt och återställa data som lagras i molnet. Om du planerar att behålla enhetsdata efter inaktiveringen bör du ta en ögonblicksbild av alla data i molnet innan du inaktiverar en enhet. Den här ögonblicksbild i molnet kan du återställa alla data i ett senare skede.

## <a name="deactivate-a-device"></a>Inaktivera en enhet

Utför följande steg om du vill inaktivera enheten.

#### <a name="to-deactivate-the-device"></a>Inaktivera enheten

1. Gå till i din tjänst **Management > enheter**. I den **enheter** bladet, klickar du på och välj den enhet som du vill inaktivera.
   
    ![Välj enhet att inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. I din **enheten instrumentpanelen** bladet, klickar du på **... Flera** och i listan, Välj **inaktivera**.
   
    ![Klicka på Inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. I den **inaktivera** bladet skriver du namnet på en enhet och klicka sedan på **inaktivera**. 
   
    ![Bekräfta inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Inaktivera processen startar och tar några minuter att slutföra.
   
    ![Inaktivera pågår](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Efter inaktiveringen uppdaterar listan över enheter.
   
    ![Inaktivera klar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Nu kan du ta bort den här enheten.

## <a name="delete-the-device"></a>Ta bort enheten

En enhet måste inaktiveras först om du vill ta bort den. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Data som är kopplade till enheten men finns kvar i molnet. Dessa data sedan påförs kostnader.

Utför följande steg för att ta bort enheten.

#### <a name="to-delete-the-device"></a>Ta bort enheten

1. I din StorSimple Enhetshanteraren, gå till **Management > enheter**. I den **enheter** bladet Välj en inaktiverad enhet som du vill ta bort.
2. I den **enheten instrumentpanelen** bladet, klickar du på **... Flera** och klicka sedan på **ta bort**.
   
   ![Välj enhet att ta bort](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. I den **ta bort** bladet skriver du namnet på din enhet för att bekräfta borttagningen och klicka sedan på **ta bort**. Ta bort enheten tar inte bort molndata som är kopplade till enheten. 
   
   ![Bekräfta borttagning](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Borttagningen startar och tar några minuter att slutföra.
   
   ![Ta bort pågår](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    När enheten har tagits bort, kan du visa den uppdaterade listan med enheter.

## <a name="next-steps"></a>Nästa steg

* För information om hur du växlar över, gå till [redundans och återställning vid återställning av din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md).

* Mer information om hur du använder tjänsten StorSimple Enhetshanteraren, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din virtuella StorSimple-matris](storsimple-virtual-array-manager-service-administration.md). 

