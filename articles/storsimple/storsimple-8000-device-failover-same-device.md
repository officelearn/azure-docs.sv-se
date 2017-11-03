---
title: "StorSimple-redundans, katastrofåterställning för enheter i 8000-serien | Microsoft Docs"
description: "Lär dig mer om att växla över din StorSimple-enhet på samma enhet."
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Växla över den fysiska StorSimple-enheten till samma enhet

## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver de steg som krävs för att växla över en StorSimple 8000-serien fysisk enhet till sig själv om en katastrof. StorSimple använder funktionen med redundans i enheten för att migrera data från en fysisk enhet som källa i datacentret till en annan fysisk enhet. Riktlinjerna i den här kursen gäller StorSimple 8000-serien fysiska enheter som kör programvaruversioner uppdatering 3 och senare.

Mer information om enheten växling vid fel och hur den används för att återställa från en katastrof, gå till [redundans och disaster recovery för StorSimple 8000-serien enheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill växla över en fysisk enhet till en annan fysisk enhet, gå till [växla över till samma fysiska StorSimple-enheten](storsimple-8000-device-failover-physical-device.md). Om du vill växla över en fysiska StorSimple-enheten till ett moln StorSimple-enhet, gå till [växla över till en StorSimple-enhet för molnet](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Krav

- Se till att du har granskat överväganden för växling vid fel för enheten. Mer information finns på [vanliga överväganden för enheten redundans](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Steg för att växla över till samma enhet

Utför följande steg om du behöver att växla över till samma enhet.

1. Skapa molnögonblicksbilder av alla volymer på din enhet. Mer information finns på [Använd Enhetshanteraren för StorSimple-tjänsten för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Återställa enheten till fabriksinställningarna. Följ de detaljerade anvisningarna i [hur du återställer en StorSimple-enhet till fabriksinställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Gå till Enhetshanteraren för StorSimple-tjänsten och välj sedan **enheter**. I den **enheter** bladet gamla enheten ska visa **Offline**.

    ![Källenheten offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurera din enhet och registrera den igen med din StorSimple Device Manager-tjänst. Nyligen registrerade enheten ska visa **redo att konfigurera**. Enhetsnamnet för den nya enheten är samma som den gamla enheten men läggas till med ett numeriskt värde som anger att enheten var återställs till fabriksinställningen och registreras igen.

    ![Nyligen registrerade enheter som är redo att konfigurera](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Slutför installationen av enheten för den nya enheten. Mer information finns på [steg 4: Slutför minimala Enhetsinstallationen](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). På den **enheter** bladet enhetens status ändras till **Online**.

   > [!IMPORTANT]
   > **Slutför den lägsta konfigurationen först eller din DR kan misslyckas.**

    ![Nyligen registrerade enhet online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Välj den gamla enheten (offline status) och från kommandofältet på **växla över**. I den **växla över** bladet välj gamla enhet som källa och ange målenheten som nyligen registrerad enhet.

    ![Sammanfattning för växling vid fel](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Detaljerade anvisningar finns i [växla över till en annan fysisk enhet](#fail-over-to-another-physical-device).

7. En enhet Återställningsjobbet har skapats att du kan övervaka från den **jobb** bladet.

8. När jobbet har slutförts, åtkomst till den nya enheten och navigera till den **volymbehållare** bladet. Kontrollera att alla volymbehållarna från den gamla enheten har migrerats till den nya enheten.

   ![Volymbehållare migreras](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. När redundansväxlingen är klar kan du inaktivera och ta bort den gamla enheten från portalen. Välj den gamla enheten (offline), högerklicka och välj sedan **inaktivera**. När enheten har inaktiverats kan uppdateras status för enheten.

     ![Källenheten inaktiveras](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Välj inaktiverad enhet, högerklicka och välj sedan **ta bort**. Detta tar bort enheten från listan över enheter.

    ![Källenheten har tagits bort](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Nästa steg

* När du har utfört en växling vid fel, kan du behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* För information om hur du använder tjänsten StorSimple Enhetshanteraren, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

