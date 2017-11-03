---
title: "StorSimple-redundans, återställning till en fysisk enhet för StorSimple 8000-serien | Microsoft Docs"
description: "Lär dig mer om att växla över din StorSimple 8000-serien fysisk enhet till en annan fysisk enhet."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Växla över till en fysisk enhet för StorSimple 8000-serien

## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver de steg som krävs för att växla över en StorSimple 8000-serien fysisk enhet till en annan fysiska StorSimple-enheten om en katastrof. StorSimple använder funktionen med redundans i enheten för att migrera data från en fysisk enhet som källa i datacentret till en annan fysisk enhet. Riktlinjerna i den här kursen gäller StorSimple 8000-serien fysiska enheter som kör programvaruversioner uppdatering 3 och senare.

Mer information om enheten växling vid fel och hur den används för att återställa från en katastrof, gå till [redundans och disaster recovery för StorSimple 8000-serien enheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill växla över en fysiska StorSimple-enheten till ett moln StorSimple-enhet, gå till [växla över till en StorSimple-enhet för molnet](storsimple-8000-device-failover-cloud-appliance.md). Om du vill växla över en fysisk enhet till sig själv, gå till [växla över till samma fysiska StorSimple-enheten](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Krav

- Se till att du har granskat överväganden för växling vid fel för enheten. Mer information finns på [vanliga överväganden för enheten redundans](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha en StorSimple 8000-serien fysisk enhet som distribuerats i datacentret. Enheten måste köra Update 3 eller senare programvaruversion. Mer information finns på [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Steg för att växla över till en fysisk enhet

Utför följande steg för att återställa enheten till en fysisk enhet som mål.

1. Kontrollera att volymbehållaren som du vill växla över associeras molnögonblicksbilder. Mer information finns på [Använd Enhetshanteraren för StorSimple-tjänsten för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till din StorSimple-Enhetshanteraren och klicka sedan på **enheter**. I den **enheter** bladet, gå till listan över enheter som är anslutna med din tjänst.
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Välj och klicka på din källenhet. På källenheten har volymbehållarna som du vill växla över. Gå till **Inställningar > Volymbehållare**.
4. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållare om du vill visa en lista över volymer i den här behållaren. Välj en volym, högerklicka och klicka på **ta Offline** att kopplas ifrån volymen. Upprepa proceduren för alla volymer i volymbehållaren.
5. Upprepa det föregående steget för alla volymbehållare som du vill växla över till en annan enhet.
6. Gå tillbaka till den **enheter** bladet. Klicka på kommandofältet **växla över**.
    ![Klicka på misslyckas över](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. I den **växla över** bladet utför följande steg:
   
   1. Klicka på **källa**. Volymbehållare med volymer som är kopplade till molnögonblicksbilder visas. De behållare som visas är berättigade för redundans. Välj volymbehållarna som du vill växla över i listan över volymbehållare. **Volymbehållare med associerade molnögonblicksbilder och offline volymer visas.**

       ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klicka på **mål**. Välj en målenhet för volymbehållarna valde i föregående steg, från listan över tillgängliga enheter. Endast de enheter som har tillräckligt med kapacitet för källa volymbehållare visas i listan.

        ![Välj mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Slutligen granska alla inställningar för växling vid fel under **sammanfattning**. När du har granskat inställningarna, markerar du kryssrutan som anger att volymerna i valda volymbehållare är offline. Klicka på **OK**.

       ![Granska inställningarna för växling vid fel](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple skapar en växling vid fel. Klicka på meddelandet för jobbet att övervaka beställningsjobbet via den **jobb** bladet.

    Om volymbehållaren som du redundansväxlade har lokala volymer kan se du enskilda återställningsjobb för varje lokal volym (inte för nivåindelade volymer) i behållaren. Dessa jobb kan ta tid för att slutföra återställningen. Det är troligt att beställningsjobbet kan slutföra tidigare. Volymerna har lokala garantier endast när återställningsjobb har slutförts.

    ![Övervakningsjobb för växling vid fel](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. När redundansväxlingen är klar går du till den **enheter** bladet.
   
   1. Välj den enhet som användes som målenhet för failover-processen.

       ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Gå till den **Volymbehållare** bladet. Alla volymbehållare tillsammans med volymer från den gamla enheten ska visas.

       ![View target volymbehållare](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en växling vid fel, kan du behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* För information om hur du använder tjänsten StorSimple Enhetshanteraren, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

