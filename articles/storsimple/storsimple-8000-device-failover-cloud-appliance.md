---
title: Redundans och haveriberedskap till en StorSimple Cloud Appliance
description: Lär dig hur du växlar över din fysiska storsimple 8000-serie till en molninstallation.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 347b899608d4322a7873b9f80f38ca1c767194d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468753"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Växla över till din StorSimple Cloud Appliance

## <a name="overview"></a>Översikt

Den här självstudien beskriver de steg som krävs för att växla över en fysisk storsimple 8000-serie till en StorSimple Cloud Appliance om det inträffar en katastrof. StorSimple använder enhetens redundansfunktion för att migrera data från en källfysisk enhet i datacentret till en molninstallation som körs i Azure. Vägledningen i den här självstudien gäller för fysiska enheter i StorSimple 8000-serien och molninstallationer som kör programversioner uppdatering 3 och senare.

Om du vill veta mer om enhetsväxling och hur den används för att återställa från en katastrof går du till [Redundans och haveriberedskap för StorSimple 8000-serieenheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill växla över en fysisk StorSimple-enhet till en annan fysisk enhet går du till [Växla över till en fysisk StorSimple-enhet](storsimple-8000-device-failover-physical-device.md). Om du vill växla över en enhet till sig själv går du till [Växla över till samma Fysiska StorSimple-enhet](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Krav

- Se till att du har granskat övervägandena för enhetsväxling. Mer information finns i [Vanliga överväganden för enhetsväxling](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha en StorSimple Cloud Appliance skapad och konfigurerad innan du kör den här proceduren. Om du kör Uppdatering 3-programversion eller senare kan du överväga att använda en 8020-molninstallation för DR. 8020-modellen har 64 TB och använder Premium Storage. Mer information finns i [Distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Åtgärder för att växla över till en molninstallation

Utför följande steg för att återställa enheten till en målbaserad StorSimple Cloud Appliance.

1.  Kontrollera att volymbehållaren som du vill växla över har associerade ögonblicksbilder i molnet. Mer information finns i [Använd Tjänsten StorSimple Device Manager för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Gå till listan över enheter som är anslutna till tjänsten i bladet **Enheter.**
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Markera och klicka på källenheten. Källenheten har de volymbehållare som du vill växla över. Gå till **Inställningar > Volymbehållare**.

    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållaren om du vill visa listan över volymer i den här behållaren. Välj en volym, högerklicka och klicka på **Koppla från** för att koppla från volymen.

    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Upprepa den här processen för alla volymer i volymbehållaren.

     ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Upprepa föregående steg för alla volymbehållare som du vill växla över till en annan enhet.

7. Gå tillbaka till **bladet Enheter.** Klicka på Växla **över**i kommandofältet .

    ![Klicka på växla över](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Gör följande i bladet **Växla över:**
   
    1. Klicka på **Källa**. Välj de volymbehållare som ska växlas över. **Endast volymbehållare med associerade ögonblicksbilder av molnet och offlinevolymer visas.**
        ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klicka på **Mål**. Välj en målmolnsinstallation i listrutan över tillgängliga enheter. **Endast de enheter som har tillräcklig kapacitet för att hantera källvolymbehållare visas i listan.**

        ![Välja mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Granska redundansinställningarna under **Sammanfattning** och markera kryssrutan som anger att volymerna i valda volymbehållare är offline. 

        ![Granska redundansinställningar](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Ett redundansjobb skapas. Om du vill övervaka redundansjobbet klickar du på jobbaviseringen.

    ![Övervaka redundansjobb](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. När redundansen är klar går du tillbaka till bladet **Enheter.**

    1. Välj den enhet som användes som mål för redundansen.

       ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klicka på **Volymbehållare**. Alla volymbehållare, tillsammans med volymerna från den gamla enheten, bör listas.

       Om volymbehållaren som du misslyckades över har lokalt fästa volymer, misslyckas dessa volymer som nivåindelade volymer. Lokalt fästa volymer stöds inte på en StorSimple Cloud Appliance.

       ![Visa behållare för målvolym](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundans kan du behöva [inaktivera eller ta bort StorSimple-enheten](storsimple-8000-deactivate-and-delete-device.md).

* Information om hur du använder Tjänsten StorSimple Device Manager finns i [Använda Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

