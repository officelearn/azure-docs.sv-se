---
title: StorSimple-redundans, katastrofåterställning för enheter i 8000-serien | Microsoft Docs
description: Lär dig hur du växlar över din StorSimple-enhet på samma enhet.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: dd207eaad1a3e821724d51a890d0882bfffda131
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577401"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Växla över den fysiska StorSimple-enheten till samma enhet

## <a name="overview"></a>Översikt

Den här självstudien beskrivs de steg som krävs för att växla över en StorSimple 8000-serien fysisk enhet till sig själv om en katastrof inträffar. StorSimple använder funktionen för växling vid fel enhet för att migrera data från en källa fysisk enhet i datacenter till en annan fysisk enhet. Riktlinjerna i den här självstudien gäller StorSimple 8000-serien fysiska enheter som kör programvaruversioner uppdatering 3 och senare.

Om du vill veta mer om redundansväxling av enhet och hur de används för att komma tillrätta med en katastrof kan du gå till [redundans och disaster recovery för StorSimple 8000-serieenheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill redundansväxla en fysisk enhet till en annan fysisk enhet går du till [Redundansväxla till samma fysiska StorSimple-enheten](storsimple-8000-device-failover-physical-device.md). Om du vill redundansväxla en fysisk StorSimple-enhet till en StorSimple-Molninstallation går du till [växla över till en StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

- Se till att du har granskat överväganden för redundansväxling av enhet. Mer information går du till [vanliga överväganden för redundansväxling av enhet](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Steg för att växla över till samma enhet

Utför följande steg om du behöver att växla över till samma enhet.

1. Ta ögonblicksbilder av molnet alla volymer på enheten. Mer information går du till [använda StorSimple Device Manager-tjänsten för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Återställa enheten till fabriksinställningarna. Följ instruktionerna i [hur du återställer en StorSimple-enhet till fabriksinställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Gå till StorSimple Device Manager-tjänsten och välj sedan **enheter**. I den **enheter** bladet gamla enheten ska visa **Offline**.

    ![Källenhet offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurera din enhet och registrera den igen med din StorSimple Device Manager-tjänsten. Nyligen registrerade enheten ska visa **redo att konfigurera**. Namnet på enheten för den nya enheten är samma som den gamla enheten men läggas till med ett numeriskt värde som anger att enheten har återställts till fabriksinställningen och registreras igen.

    ![Nyligen registrerade enheter som är redo för installation](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Slutför installationen av enheten för den nya enheten. Mer information går du till [steg 4: Slutför minimala Enhetsinstallationen](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). På den **enheter** bladet enhetens status ändras till **Online**.

   > [!IMPORTANT]
   > **Slutföra konfigurationen av minsta först eller din Haveriberedskap kan misslyckas.**

    ![Nyligen registrerade enhet online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Välj den gamla enheten (offline status) och från kommandofältet klickar du på **Redundansväxla**. I den **Redundansväxla** bladet välj gamla enheten som källa och ange målenheten som nyligen registrerade enheten.

    ![Sammanfattning för redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Detaljerade anvisningar finns misslyckas över till en annan fysisk enhet.

7. En enhet Återställningsjobbet har skapats att du kan övervaka från den **jobb** bladet.

8. När jobbet har slutförts, få åtkomst till den nya enheten och gå till den **volymbehållare** bladet. Kontrollera att alla volymcontainrar från den gamla enheten har migrerat till den nya enheten.

   ![Volymcontainrar har migrerats](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. När redundansväxlingen är klar kan du inaktivera och ta bort gamla enheten från portalen. Välj den gamla enheten (offline), högerklicka och välj sedan **inaktivera**. När enheten är inaktiverad, uppdateras statusen för enheten.

     ![Källenhet inaktiveras](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Välj inaktiverad enhet, högerklicka och välj sedan **ta bort**. Detta tar bort enheten från listan över enheter.

    ![Källenheten har tagits bort](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundans, du kan behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* Information om hur du använder StorSimple Device Manager-tjänsten går du till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

