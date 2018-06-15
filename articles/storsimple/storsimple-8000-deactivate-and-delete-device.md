---
title: Inaktivera och ta bort en StorSimple 8000-serieenhet | Microsoft Docs
description: Beskriver hur du tar bort StorSimple-enhet från tjänsten genom att först inaktivera det och sedan ta bort den.
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874971"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Inaktivera och ta bort en StorSimple-enhet

## <a name="overview"></a>Översikt

Den här artikeln beskriver hur du inaktivera och ta bort en StorSimple-enhet som är ansluten till en StorSimple Device Manager-tjänst. Riktlinjerna i den här artikeln gäller endast StorSimple 8000-serien enheter, inklusive StorSimple moln installationer. Om du använder en virtuell StorSimple-matris, går du till [inaktivera och ta bort en virtuell StorSimple-matris](storsimple-virtual-array-deactivate-and-delete-device.md).

Inaktivering av servrarna anslutningen mellan enheten och motsvarande StorSimple enheten Manager-tjänsten. Du kanske vill vidta en StorSimple-enhet out-of-service (till exempel om du ersätter eller uppgradera din enhet eller om du inte längre använder StorSimple). I så fall, måste du inaktivera enheten innan du kan ta bort den.

När du inaktiverar en enhet är alla data som lagras lokalt på enheten inte längre tillgänglig. Endast data som är kopplade till den enhet som lagrades i molnet kan återställas.

> [!WARNING]
> Inaktivering är en PERMANENT åtgärd och kan inte ångras. En inaktiverad enhet kan inte registreras med tjänsten StorSimple Enhetshanteraren såvida inte den återställs till fabriksinställningarna.
>
> Fabriksåterställa processen tar bort alla data som lagras lokalt på din enhet. Därför måste du ta en ögonblicksbild av alla data i molnet innan du inaktiverar en enhet. Den här ögonblicksbild i molnet kan du återställa alla data i ett senare skede.

När du har läst den här självstudiekursen kommer du att kunna:

* Inaktivera en enhet och ta bort data.
* Inaktivera en enhet och behålla data.

> [!NOTE]
> Innan du inaktiverar en fysiska StorSimple-enheten eller molnet installation, stoppa eller ta bort klienter och värdar som beror på enheten.


## <a name="deactivate-and-delete-data"></a>Inaktivera och ta bort data

Slutför följande steg om du är intresserad av att ta bort enheten fullständigt och inte vill behålla data på enheten.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Att inaktivera enheten och ta bort data

1. Innan du inaktiverar en enhet måste du ta bort alla volymen behållare (och volymer) som är kopplade till enheten. Du kan ta bort volymbehållare endast när du tar bort tillhörande säkerhetskopior.

    > [!NOTE]
    > Se till att data från behållaren borttagna volymen tas bort från enheten innan du inaktiverar en fysiska StorSimple-enheten eller molnet utrustning. Du kan övervaka molnet förbrukning diagram och när du ser molnanvändning släpp på grund av de säkerhetskopior som du har tagit bort kan du fortsätta att inaktivera enheten. Om du inaktiverar enheten innan den här nedrullningsbara sker data ensamma i lagringskontot och påförs kostnader.

2. Inaktivera enheten enligt följande:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet Välj den enhet som du vill inaktivera, högerklicka och klicka sedan på **inaktivera**.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. I den **inaktivera** bladet, typ enhetsnamnet för att bekräfta och klicka sedan på **inaktivera**. Inaktivera processen startar och tar några minuter att slutföra.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Efter inaktiveringen, kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Använd följande steg för att ta bort enheten:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet välj inaktiverade enheten som du vill ta bort, högerklicka och klicka sedan på **ta bort**.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. I den **ta bort** bladet, typ enhetsnamnet för att bekräfta och klicka sedan på **ta bort**. Borttagningen tar några minuter att slutföra.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen är klar har du ett meddelande. Listan över enheter uppdateras också borttagningen.

## <a name="deactivate-and-retain-data"></a>Inaktivera och behålla data

Om du är intresserad av att ta bort enheten, men du vill behålla data, gör du följande:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Inaktivera en enhet och behålla data
1. Inaktivera enheten. Alla volymbehållare och ögonblicksbilder av enheten finns kvar.
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet Välj den enhet som du vill inaktivera, högerklicka och klicka sedan på **inaktivera**.

         ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. I den **inaktivera** bladet, typ enhetsnamnet för att bekräfta och klicka sedan på **inaktivera**. Inaktivera processen startar och tar några minuter att slutföra.

         ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Nu kan du växla över volymbehållarna och associerade ögonblicksbilder. Mer information om procedurer går du till [redundans och disaster recovery för din StorSimple-enhet](storsimple-8000-device-failover-disaster-recovery.md).
3. Efter inaktivering och växling vid fel, kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Om du vill ta bort enheten, gör du följande:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet välj inaktiverade enheten som du vill ta bort, högerklicka och klicka sedan på **ta bort**.

       ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. I den **ta bort** bladet, typ enhetsnamnet för att bekräfta och klicka sedan på **ta bort**. Borttagningen tar några minuter att slutföra.

       ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen är klar har du ett meddelande. Listan över enheter uppdateras också borttagningen.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Inaktivera och ta bort en moln-installation

För en för StorSimple molnet, inaktivering från portalen tar bort och tar bort den virtuella datorn och de resurser som skapades när den etablerades. När molninstallationen har inaktiverats kan den inte återställas till sitt tidigare tillstånd.

![Inaktivera molnet StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Inaktivera resulterar i följande åtgärder:

* StorSimple moln enheten tas bort från tjänsten.
* Den virtuella datorn för StorSimple moln enheten tas bort.
* Operativsystemdisken och datadiskar som skapats för StorSimple moln enhet tas bort.
* Den värdbaserade tjänsten och virtuella nätverk som skapades under etableringen bevaras. Om du inte använder dessa enheter, bör du ta bort dem manuellt.
* Molnögonblicksbilder som skapats av StorSimple moln enhet bevaras.

När molnet enheten har inaktiverats kan du ta bort den från listan över enheter. Välj inaktiverad enhet, högerklicka och klicka sedan på **ta bort**. StorSimple meddelas du när enheten har tagits bort och listan över enheter uppdateringar.

## <a name="next-steps"></a>Nästa steg

* Om du vill återställa inaktiverade enheten till fabriksinställningarna, gå till [återställa enheten till fabriksinställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* För teknisk support [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Mer information om hur du använder tjänsten StorSimple Enhetshanteraren, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

