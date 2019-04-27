---
title: StorSimple-redundans, haveriberedskap till en fysisk enhet för StorSimple 8000-serien | Microsoft Docs
description: Lär dig hur du växlar över din StorSimple 8000-serien fysiska enhet till en annan fysisk enhet.
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
ms.openlocfilehash: 5fcf95a1a3033a5150945dbd841f12d50ebb023b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577265"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Växla över till en fysisk enhet för StorSimple 8000-serien

## <a name="overview"></a>Översikt

Den här självstudien beskrivs de steg som krävs för att växla över en StorSimple 8000-serien fysisk enhet till en annan fysiska StorSimple-enheten om en katastrof inträffar. StorSimple använder funktionen för växling vid fel enhet för att migrera data från en källa fysisk enhet i datacenter till en annan fysisk enhet. Riktlinjerna i den här självstudien gäller StorSimple 8000-serien fysiska enheter som kör programvaruversioner uppdatering 3 och senare.

Om du vill veta mer om redundansväxling av enhet och hur de används för att komma tillrätta med en katastrof kan du gå till [redundans och disaster recovery för StorSimple 8000-serieenheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill redundansväxla en fysisk StorSimple-enhet till en StorSimple-Molninstallation går du till [växla över till en StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Om du vill redundansväxla en fysisk enhet till sig själv, gå till [Redundansväxla till samma fysiska StorSimple-enheten](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

- Se till att du har granskat överväganden för redundansväxling av enhet. Mer information går du till [vanliga överväganden för redundansväxling av enhet](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha en StorSimple 8000-serien fysisk enhet distribueras i datacentret. Enheten måste köra Update 3 eller senare av programvara. Mer information går du till [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Steg för att växla över till en fysisk enhet

Utför följande steg för att återställa enheten till en fysisk målenhet.

1. Kontrollera att den volymbehållare som du vill växla över associeras ögonblicksbilder av molnet. Mer information går du till [använda StorSimple Device Manager-tjänsten för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till StorSimple Device Manager och klicka sedan på **enheter**. I den **enheter** bladet går du till listan över enheter som är anslutna med din tjänst.
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Välj och klicka på källenheten. Källenheten har de volymbehållare som du vill växla över. Gå till **Inställningar > Volymbehållare**.
4. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållare om du vill visa en lista över volymer i den här behållaren. Välj en volym, högerklicka och klicka på **ta Offline** du koppla från volymen. Upprepa proceduren för alla volymer i volymbehållaren.
5. Upprepa det föregående steget för alla de volymbehållare som du vill växla över till en annan enhet.
6. Gå tillbaka till den **enheter** bladet. I kommandofältet klickar du på **Redundansväxla**.
    ![Klicka på ett fel under](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. I den **Redundansväxla** bladet utför följande steg:
   
   1. Klicka på **källa**. Volymbehållare med volymer som är associerade med ögonblicksbilder av molnet visas. Endast de behållare som visas är berättigade för redundans. I listan över volymbehållare, väljer du de volymbehållare som du vill växla över. **Endast volymbehållarna med associerade molnögonblicksbilder och offlinevolymer visas.**

       ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klicka på **Target**. Välj en målenhet för de volymbehållare som valde i föregående steg, från den nedrullningsbara listan över tillgängliga enheter. Endast de enheter som har tillräcklig kapacitet för källan volymbehållare visas i listan.

        ![Välja mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Slutligen kan granska alla inställningar för växling vid fel under **sammanfattning**. När du har granskat inställningarna, väljer du kryssrutan som anger att volymerna i valda volymbehållarna är offline. Klicka på **OK**.

       ![Granska inställningarna för växling vid fel](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple skapar ett redundansjobb. Klicka på meddelandet för jobbet att övervaka redundansjobb via den **jobb** bladet.

    Om den volymbehållare som du redundansväxlat har lokala volymer, ser du enskilda återställningsjobb för varje lokal volym (inte för nivåindelade volymer) i behållaren. Dessa jobb kan ta tid för att slutföra återställningen. Det är troligt att redundansväxlingen kan slutföra tidigare. Volymerna har lokala garantier förrän återställningen jobben har slutförts.

    ![Övervakningsjobb för redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. När redundansväxlingen är klar går du till den **enheter** bladet.
   
   1. Välj den enhet som användes som målenheten för failover-processen.

       ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Gå till den **Volymbehållare** bladet. Alla volymcontainrar tillsammans med volymer från den gamla enheten bör visas.

       ![View target volymbehållare](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundans, du kan behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* Information om hur du använder StorSimple Device Manager-tjänsten går du till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

