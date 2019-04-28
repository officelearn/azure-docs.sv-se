---
title: Inaktivera och ta bort en enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du tar bort StorSimple-enhet från tjänsten genom att inaktivera det först och sedan ta bort den.
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
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 116ac5c4efda87b5d16336dd326d516299f6955d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481976"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Inaktivera och ta bort en StorSimple-enhet

## <a name="overview"></a>Översikt

Den här artikeln beskriver hur du inaktivera och ta bort en StorSimple-enhet som är ansluten till en StorSimple Device Manager-tjänsten. Riktlinjerna i den här artikeln gäller bara för StorSimple 8000-serieenheter inklusive StorSimple Cloud Appliances. Om du använder en StorSimple Virtual Array, går du till [inaktivera och ta bort en StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Inaktiveringen servrar anslutningen mellan enheten och motsvarande StorSimple Device Manager-tjänsten. Du kanske vill ta ur en StorSimple-enhet (till exempel om du ersätta eller uppgradera din enhet eller om du inte längre använder StorSimple). Om så är fallet, måste du inaktivera enheten innan du kan ta bort den.

När du inaktiverar en enhet är alla data som lagrats lokalt på enheten inte längre tillgänglig. Endast de data som hör till enheten som lagrats i molnet kan återställas.

> [!WARNING]
> Inaktiveringen är en PERMANENT åtgärd och kan inte ångras. En inaktiverad enhet kan inte registreras med StorSimple Device Manager-tjänsten, såvida inte det återställs till fabriksinställningarna.
>
> Fabriksåterställning processen tar bort alla data som lagras lokalt på din enhet. Därför måste du ta en molnögonblicksbild av alla dina data innan du inaktiverar en enhet. Den här ögonblicksbilden av molndata kan du återställa alla data i ett senare skede.

När du har läst den här självstudiekursen kommer du att kunna:

* Inaktivera en enhet och ta bort data.
* Inaktivera en enhet och behålla data.

> [!NOTE]
> Innan du inaktiverar en fysiska StorSimple-enheten eller molninstallationen, stoppa eller ta bort klienter och värdar som beror på den enheten.


## <a name="deactivate-and-delete-data"></a>Inaktivera och ta bort data

Om du är intresserad av att ta bort enheten helt och inte vill behålla data på enheten, utför du följande steg.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Att inaktivera enheten och ta bort data

1. Innan du inaktiverar en enhet måste du ta bort alla volymen behållare (och volymerna) som hör till enheten. Du kan ta bort volymbehållare endast när du tar bort tillhörande säkerhetskopior.

    > [!NOTE]
    > Se till att data från den borttagna volymbehållaren tas bort från enheten innan du inaktiverar en fysiska StorSimple-enheten eller molninstallationen. Du kan övervaka molnet förbrukning diagram och när du ser molnanvändning ta bort på grund av de säkerhetskopior som du har tagit bort kan du fortsätta att inaktivera enheten. Om du inaktiverar enheten innan den här sänkningen sker data ensamma i lagringskontot och tillkommer avgifter.

2. Inaktivera enheten enligt följande:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet Välj den enhet som du vill inaktivera, högerklicka och klicka sedan på **inaktivera**.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. I den **inaktivera** bladet, Skriv namnet på enheten för att bekräfta och klicka sedan på **inaktivera**. Inaktivera processen startar och tar några minuter att slutföra.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Efter inaktiveringen, kan du ta bort enheten helt. Tar bort en enhet tar du bort den från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Använd följande steg för att ta bort enheten:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet Välj den inaktiverade enheten som du vill ta bort, högerklicka och klicka sedan på **ta bort**.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. I den **ta bort** bladet, Skriv namnet på enheten för att bekräfta och klicka sedan på **ta bort**. Det tar några minuter att slutföra borttagningen.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen är slutförd, du får ett meddelande. Dessutom uppdateras enhetslistan tagits bort.

## <a name="deactivate-and-retain-data"></a>Inaktivera och behålla data

Om du är intresserad av tar bort enheten men vill behålla data, gör du följande:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Att inaktivera en enhet och behålla data
1. Inaktivera enheten. Alla volymcontainrar och ögonblicksbilder av enheten förblir.
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet Välj den enhet som du vill inaktivera, högerklicka och klicka sedan på **inaktivera**.

         ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. I den **inaktivera** bladet, Skriv namnet på enheten för att bekräfta och klicka sedan på **inaktivera**. Inaktivera processen startar och tar några minuter att slutföra.

         ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Nu kan du växla över volymbehållarna och associerade ögonblicksbilder. Mer information om procedurer går du till [redundans och disaster recovery för StorSimple-enheten](storsimple-8000-device-failover-disaster-recovery.md).
3. Efter inaktivering och växling vid fel, kan du ta bort enheten helt. Tar bort en enhet tar du bort den från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera enheten tagits bort. Utför följande steg för att ta bort enheten:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I den **enheter** bladet Välj den inaktiverade enheten som du vill ta bort, högerklicka och klicka sedan på **ta bort**.

       ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. I den **ta bort** bladet, Skriv namnet på enheten för att bekräfta och klicka sedan på **ta bort**. Det tar några minuter att slutföra borttagningen.

       ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen är slutförd, du får ett meddelande. Dessutom uppdateras enhetslistan tagits bort.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Inaktivera och ta bort en molninstallation

För en StorSimple Cloud Appliance inaktivering från portalen frigör och tar bort den virtuella datorn och de resurser som skapades när den etablerades. När molninstallationen har inaktiverats kan den inte återställas till sitt tidigare tillstånd.

![Inaktivera StorSimple-Molninstallation](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Inaktivera resulterar i följande åtgärder:

* StorSimple-Molninstallationen tas bort från tjänsten.
* Den virtuella datorn för StorSimple-Molninstallationen tas bort.
* Operativsystemdisken och datadiskarna som skapades för StorSimple-Molninstallationen finns kvar. Om du inte använder dessa entiteter, bör du ta bort dem manuellt.
* Den värdbaserade tjänsten och virtuella nätverk som skapades under etableringen bevaras. Om du inte använder dessa entiteter, bör du ta bort dem manuellt.
* Ögonblicksbilder av molnet som skapats av StorSimple-Molninstallationen finns kvar.

När molninstallationen har inaktiverats kan du ta bort den från listan över enheter. Välj inaktiverad enhet, högerklicka och klicka sedan på **ta bort**. StorSimple meddelas du när enheten har tagits bort och listan över enheter uppdateras.

## <a name="next-steps"></a>Nästa steg

* Om du vill återställa den inaktivera enheten till fabriksinställningarna, gå till [återställa enheten till fabriksinställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* För teknisk support [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Om du vill veta mer om hur du använder StorSimple Device Manager-tjänsten kan du gå till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

