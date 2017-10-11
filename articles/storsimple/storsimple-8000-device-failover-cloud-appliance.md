---
title: "StorSimple-redundans, katastrofåterställning till ett moln StorSimple-enhet | Microsoft Docs"
description: "Lär dig mer om att växla över StorSimple 8000-serien fysiska enheten till en enhet i molnet."
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Växla över till molnet för StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver de steg som krävs för att växla över en StorSimple 8000-serien fysisk enhet till ett moln StorSimple-enhet om en katastrof. StorSimple använder funktionen med redundans i enheten för att migrera data från en fysisk enhet som källa i datacenter till ett moln-installation som körs i Azure. Riktlinjerna i den här kursen gäller StorSimple 8000-serien fysiska enheter och molnet installationer med programvaruversioner uppdatering 3 och senare.

Mer information om enheten växling vid fel och hur den används för att återställa från en katastrof, gå till [redundans och disaster recovery för StorSimple 8000-serien enheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill växla över en fysiska StorSimple-enheten till en annan fysisk enhet, gå till [växla över till en fysisk enhet i StorSimple](storsimple-8000-device-failover-physical-device.md). Om du vill växla en enhet till sig själv, gå till [växla över till samma fysiska StorSimple-enheten](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Krav

- Se till att du har granskat överväganden för växling vid fel för enheten. Mer information finns på [vanliga överväganden för enheten redundans](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha en StorSimple-molnet enhet skapas och konfigureras innan du kör den här proceduren. Om körs uppdaterar 3 programvaruversionen eller senare, Överväg att använda en 8020 moln anordning för DR. Modellen 8020 har 64 TB och Premium-lagring. Mer information finns på [distribuera och hantera en StorSimple-enhet för molnet](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Steg för att växla över till en moln-installation

Utför följande steg för att återställa enheten till ett mål StorSimple-enhet för molnet.

1.  Kontrollera att volymbehållaren som du vill växla över associeras molnögonblicksbilder. Mer information finns på [Använd Enhetshanteraren för StorSimple-tjänsten för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet, gå till listan över enheter som är anslutna med din tjänst.
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Välj och klicka på din källenhet. På källenheten har volymbehållarna som du vill växla över. Gå till **Inställningar > Volymbehållare**.

    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållare om du vill visa en lista över volymer i den här behållaren. Välj en volym, högerklicka och klicka på **ta Offline** att kopplas ifrån volymen.

    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Upprepa proceduren för alla volymer i volymbehållaren.

     ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Upprepa det föregående steget för alla volymbehållare som du vill växla över till en annan enhet.

7. Gå tillbaka till den **enheter** bladet. Klicka på kommandofältet **växla över**.

    ![Klicka på misslyckas över](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. I den **växla över** bladet utför följande steg:
   
    1. Klicka på **källa**. Välj volymbehållare att växla över. **Volymbehållare med associerade molnögonblicksbilder och offline volymer visas.**
        ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klicka på **mål**. Välj ett mål moln installation från den nedrullningsbara listan över tillgängliga enheter. **Endast de enheter som har tillräckligt med kapacitet för källa volymbehållare visas i listan.**

        ![Välj mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Granska inställningarna för växling vid fel under **sammanfattning** och markera kryssrutan som anger att volymerna i valda volymbehållare är offline. 

        ![Granska inställningarna för växling vid fel](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Det skapas ett jobb för växling vid fel. Klicka på meddelandet för jobbet om du vill övervaka beställningsjobbet.

    ![Övervakningsjobb för växling vid fel](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. När redundansväxlingen är klar går du tillbaka till den **enheter** bladet.

    1. Välj den enhet som används som mål för redundans.

       ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klicka på **Volymbehållare**. Alla volymbehållare tillsammans med volymer från den gamla enheten ska visas.

       Om volymbehållaren som du redundansväxlade har lokalt Fäst volymer, har volymerna redundansväxlats som nivåindelade volymer. Lokalt fästa volymer stöds inte på en StorSimple-enhet för molnet.

       ![View target volymbehållare](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en växling vid fel, kan du behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).

* För information om hur du använder tjänsten StorSimple Enhetshanteraren, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

