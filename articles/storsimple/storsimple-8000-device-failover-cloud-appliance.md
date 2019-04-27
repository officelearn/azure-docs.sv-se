---
title: StorSimple-redundans, haveriberedskap till en StorSimple Cloud Appliance | Microsoft Docs
description: Lär dig hur du växlar över din StorSimple 8000-serien fysiska enheten till en molninstallation.
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
ms.openlocfilehash: 45c521fd044fa258b8052a3f0de48784cf4160e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584443"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Växla över till din StorSimple-Molninstallation

## <a name="overview"></a>Översikt

Den här självstudien beskrivs de steg som krävs för att växla över en StorSimple 8000-serien fysisk enhet till en StorSimple-Molninstallationen om en katastrof inträffar. StorSimple använder funktionen för växling vid fel enhet för att migrera data från en källa fysisk enhet i datacenter till en molninstallation som körs i Azure. Riktlinjerna i den här självstudien gäller för fysiska enheter i StorSimple 8000-serien och molninstallationer som kör programvaruversioner uppdatering 3 och senare.

Om du vill veta mer om redundansväxling av enhet och hur de används för att komma tillrätta med en katastrof kan du gå till [redundans och disaster recovery för StorSimple 8000-serieenheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill redundansväxla en fysisk StorSimple-enhet till en annan fysisk enhet går du till [växla över till en fysisk enhet i StorSimple](storsimple-8000-device-failover-physical-device.md). Om du vill redundansväxla en enhet till sig själv, gå till [Redundansväxla till samma fysiska StorSimple-enheten](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Se till att du har granskat överväganden för redundansväxling av enhet. Mer information går du till [vanliga överväganden för redundansväxling av enhet](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha en StorSimple-Molninstallationen skapas och konfigureras innan du kör den här proceduren. Om körs uppdaterar 3 programvaruversion eller senare, Överväg att använda en 8020-molninstallationer för DR. Modellen 8020 har 64 TB och använder Premium Storage. Mer information går du till [distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Steg för att växla över till en molninstallation

Utför följande steg för att återställa enheten till ett mål StorSimple Cloud Appliance.

1.  Kontrollera att den volymbehållare som du vill växla över associeras ögonblicksbilder av molnet. Mer information går du till [använda StorSimple Device Manager-tjänsten för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet går du till listan över enheter som är anslutna med din tjänst.
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Välj och klicka på källenheten. Källenheten har de volymbehållare som du vill växla över. Gå till **Inställningar > Volymbehållare**.

    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållare om du vill visa en lista över volymer i den här behållaren. Välj en volym, högerklicka och klicka på **ta Offline** du koppla från volymen.

    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Upprepa proceduren för alla volymer i volymbehållaren.

     ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Upprepa det föregående steget för alla de volymbehållare som du vill växla över till en annan enhet.

7. Gå tillbaka till den **enheter** bladet. I kommandofältet klickar du på **Redundansväxla**.

    ![Klicka på ett fel under](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. I den **Redundansväxla** bladet utför följande steg:
   
    1. Klicka på **källa**. Välj de volymbehållare som växlar över. **Endast volymbehållarna med associerade molnögonblicksbilder och offlinevolymer visas.**
        ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klicka på **Target**. Välj ett mål för molninstallationen från den nedrullningsbara listan över tillgängliga enheter. **Endast de enheter som har tillräcklig kapacitet för källan volymbehållare visas i listan.**

        ![Välja mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Granska inställningarna för växling vid fel under **sammanfattning** och markera kryssrutan som anger att volymerna i valda volymbehållarna är offline. 

        ![Granska inställningarna för växling vid fel](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Ett redundansjobb har skapats. Klicka på meddelandet för jobbet för att övervaka redundansväxlingen.

    ![Övervakningsjobb för redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. När redundansväxlingen är klar går du tillbaka till den **enheter** bladet.

    1. Välj den enhet som används som mål för redundans.

       ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klicka på **Volymbehållare**. Alla volymcontainrar tillsammans med volymer från den gamla enheten bör visas.

       Om den volymbehållare som du redundansväxlat har lokalt fixerade volymer, har volymerna redundansväxlats som nivåindelade volymer. Lokalt fixerade volymer stöds inte på en StorSimple Cloud Appliance.

       ![View target volymbehållare](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundans, du kan behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).

* Information om hur du använder StorSimple Device Manager-tjänsten går du till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

