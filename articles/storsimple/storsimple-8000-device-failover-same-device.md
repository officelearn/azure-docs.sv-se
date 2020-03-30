---
title: Redundans- och haveriberedskap till samma StorSimple 8000-enhet
description: Läs om hur du växlar över Din StorSimple-enhet till samma enhet.
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
ms.openlocfilehash: c8fe2d7ec7649f47f6cb9c8ae2c83f19c15691b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471813"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Växla över din fysiska StorSimple-enhet till samma enhet

## <a name="overview"></a>Översikt

Den här självstudien beskriver de steg som krävs för att växla över en fysisk storsimple 8000-serie till sig själv om det finns en katastrof. StorSimple använder enhetens redundansfunktion för att migrera data från en källfysisk enhet i datacentret till en annan fysisk enhet. Vägledningen i den här självstudien gäller för fysiska enheter i StorSimple 8000-serien som kör programversioner uppdatering 3 och senare.

Om du vill veta mer om enhetsväxling och hur den används för att återställa från en katastrof går du till [Redundans och haveriberedskap för StorSimple 8000-serieenheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill växla över en fysisk enhet till en annan fysisk enhet går du till [Växla över till samma Fysiska StorSimple-enhet](storsimple-8000-device-failover-physical-device.md). Om du vill växla över en fysisk StorSimple-enhet till en StorSimple Cloud Appliance går du till [Växla över till en StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Krav

- Se till att du har granskat övervägandena för enhetsväxling. Mer information finns i [Vanliga överväganden för enhetsväxling](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Steg för att växla över till samma enhet

Utför följande steg om du behöver växla över till samma enhet.

1. Ta ögonblicksbilder av molnet av alla volymer i enheten. Mer information finns i [Använd Tjänsten StorSimple Device Manager för att skapa säkerhetskopior](storsimple-8000-manage-backup-policies-u2.md).
2. Återställ enheten till fabriksinställningar. Följ detaljerade instruktioner i [hur du återställer en StorSimple-enhet till fabriksinställningar .](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)
3. Gå till Tjänsten StorSimple Device Manager och välj sedan **Enheter**. I bladet **Enheter** ska den gamla enheten visas som **offline**.

    ![Källenheten offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurera enheten och registrera den igen med tjänsten StorSimple Device Manager. Den nyregistrerade enheten ska visas som **klar att konfigurera**. Enhetsnamnet för den nya enheten är samma som den gamla enheten men läggs till med en siffra för att indikera att enheten återställdes till fabriksstandard och registrerades igen.

    ![Nyregistrerad enhet redo att konfigureras](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Slutför enhetskonfigurationen för den nya enheten. Mer information finns i [steg 4: Slutför minsta enhetskonfiguration](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). På bladet **Enheter** ändras enhetens status till **Online**.

   > [!IMPORTANT]
   > **Slutför den minsta konfigurationen först, annars misslyckas din DR.**

    ![Nyregistrerad enhet online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Välj den gamla enheten (status offline) och klicka på **Växla över**i kommandofältet . I bladet **Växla över misslyckas** väljer du gammal enhet som källa och anger målenheten som den nyregistrerade enheten.

    ![Sammanfattning av redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Detaljerade instruktioner finns i Växla över till en annan fysisk enhet.

7. Ett enhetsåterställningsjobb skapas **Jobs** som du kan övervaka från jobbbladet.

8. När jobbet har slutförts öppnar du den nya enheten och navigerar till **bladet Volymbehållare.** Kontrollera att alla volymbehållare från den gamla enheten har migrerat till den nya enheten.

   ![Volymbehållare migrerade](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. När redundansen är klar kan du inaktivera och ta bort den gamla enheten från portalen. Markera den gamla enheten (offline), högerklicka och välj sedan **Inaktivera**. När enheten har inaktiverats uppdateras enhetens status.

     ![Källenheten inaktiverad](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Markera den inaktiverade enheten, högerklicka och välj sedan **Ta bort**. Då tas enheten bort från listan över enheter.

    ![Källenheten har tagits bort](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundans kan du behöva [inaktivera eller ta bort StorSimple-enheten](storsimple-8000-deactivate-and-delete-device.md).
* Information om hur du använder Tjänsten StorSimple Device Manager finns i [Använda Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

