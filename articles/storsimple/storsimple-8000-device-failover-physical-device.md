---
title: Redundans, haveriberedskap till en annan StorSimple 8000-enhet
description: Lär dig hur du växlar över den fysiska storsimple-serien i StorSimple 8000-serien till en annan fysisk enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 9e4e890ab5491e46ffe5ea0e1c168d168f9cc729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468617"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Växla över till en fysisk storsimple-enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

Den här självstudien beskriver de steg som krävs för att växla över en fysisk storsimple 8000-serie till en annan fysisk StorSimple-enhet om det inträffar en katastrof. StorSimple använder enhetens redundansfunktion för att migrera data från en källfysisk enhet i datacentret till en annan fysisk enhet. Vägledningen i den här självstudien gäller för fysiska enheter i StorSimple 8000-serien som kör programversioner uppdatering 3 och senare.

Om du vill veta mer om enhetsväxling och hur den används för att återställa från en katastrof går du till [Redundans och haveriberedskap för StorSimple 8000-serieenheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill växla över en fysisk StorSimple-enhet till en StorSimple Cloud Appliance går du till [Växla över till en StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Om du vill växla över en fysisk enhet till sig själv går du till [Växla över till samma Fysiska StorSimple-enhet](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Krav

- Se till att du har granskat övervägandena för enhetsväxling. Mer information finns i [Vanliga överväganden för enhetsväxling](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha en fysisk enhet i StorSimple 8000-serien som distribueras i datacentret. Enheten måste köra uppdatering 3 eller senare. Mer information finns i [Distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Steg för att växla över till en fysisk enhet

Utför följande steg för att återställa enheten till en fysisk målenhet.

1. Kontrollera att volymbehållaren som du vill växla över har associerade ögonblicksbilder i molnet. Mer information finns i [Använd Tjänsten StorSimple Device Manager för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till StorSimple-enhetshanteraren och klicka sedan på **Enheter**. Gå till listan över enheter som är anslutna till tjänsten i bladet **Enheter.**
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Markera och klicka på källenheten. Källenheten har de volymbehållare som du vill växla över. Gå till **Inställningar > Volymbehållare**.
4. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållaren om du vill visa listan över volymer i den här behållaren. Välj en volym, högerklicka och klicka på **Koppla från** för att koppla från volymen. Upprepa den här processen för alla volymer i volymbehållaren.
5. Upprepa föregående steg för alla volymbehållare som du vill växla över till en annan enhet.
6. Gå tillbaka till **bladet Enheter.** Klicka på Växla **över**i kommandofältet .
    ![Klicka på växla över](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Gör följande i bladet **Växla över:**
   
   1. Klicka på **Källa**. Volymbehållarna med volymer som är associerade med ögonblicksbilder i molnet visas. Endast de behållare som visas kan redundans. I listan över volymbehållare väljer du de volymbehållare som du vill växla över. **Endast volymbehållare med associerade ögonblicksbilder av molnet och offlinevolymer visas.**

       ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klicka på **Mål**. För de volymbehållare som valts i föregående steg väljer du en målenhet i listrutan över tillgängliga enheter. Endast de enheter som har tillräcklig kapacitet för att hantera källvolymbehållare visas i listan.

        ![Välja mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Slutligen granska alla redundansinställningar under **Sammanfattning**. När du har granskat inställningarna markerar du kryssrutan som anger att volymerna i valda volymbehållare är offline. Klicka på **OK**.

       ![Granska redundansinställningar](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple skapar ett redundansjobb. Klicka på jobbmeddelandet om du vill övervaka redundansjobbet via bladet **Jobb.**

    Om volymbehållaren som du misslyckades över har lokala volymer visas enskilda återställningsjobb för varje lokal volym (inte för nivåindelade volymer) i behållaren. Dessa återställa jobb kan ta ganska lång tid att slutföra. Det är troligt att redundansjobbet kan slutföras tidigare. Dessa volymer kommer att ha lokala garantier först när återställningsjobben har slutförts.

    ![Övervaka redundansjobb](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. När redundansen är klar går du till bladet **Enheter.**
   
   1. Välj den enhet som användes som målenhet för redundansprocessen.

       ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Gå till bladet **Volymbehållare.** Alla volymbehållare, tillsammans med volymerna från den gamla enheten, bör listas.

       ![Visa behållare för målvolym](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundans kan du behöva [inaktivera eller ta bort StorSimple-enheten](storsimple-8000-deactivate-and-delete-device.md).
* Information om hur du använder Tjänsten StorSimple Device Manager finns i [Använda Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

