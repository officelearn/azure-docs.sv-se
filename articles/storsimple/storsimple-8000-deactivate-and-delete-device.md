---
title: Inaktivera och ta bort en StorSimple 8000-serieenhet | Microsoft-dokument
description: Beskriver hur du tar bort StorSimple-enheten från tjänsten genom att först inaktivera den och sedan ta bort den.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61481976"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Inaktivera och ta bort en StorSimple-enhet

## <a name="overview"></a>Översikt

I den här artikeln beskrivs hur du inaktiverar och tar bort en StorSimple-enhet som är ansluten till en StorSimple Enhetshanterarens tjänst. Vägledningen i den här artikeln gäller endast StorSimple 8000-seriens enheter, inklusive StorSimple Cloud Appliances. Om du använder en StorSimple Virtual Array går du till [Inaktivera och ta bort en StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Inaktiveringen avbryter anslutningen mellan enheten och motsvarande StorSimple Enhetshanteraren-tjänst. Du kanske vill ta bort en StorSimple-enhet (till exempel om du byter ut eller uppgraderar enheten eller om du inte längre använder StorSimple). Om så är fallet måste du inaktivera enheten innan du kan ta bort den.

När du inaktiverar en enhet är alla data som lagrats lokalt på enheten inte längre tillgängliga. Endast data som är associerade med enheten som lagrades i molnet kan återställas.

> [!WARNING]
> Inaktivering är en PERMANENT-åtgärd och kan inte ångras. En inaktiverad enhet kan inte registreras med Tjänsten StorSimple Device Manager om den inte återställs till fabriksinställningarna.
>
> Fabriksåterställningsprocessen tar bort alla data som lagrats lokalt på enheten. Därför måste du ta en ögonblicksbild av alla dina data innan du inaktiverar en enhet. Med den här ögonblicksbilden av molnet kan du återställa alla data i ett senare skede.

Efter att ha läst den här guiden kommer du att kunna:

* Inaktivera en enhet och ta bort data.
* Inaktivera en enhet och behålla data.

> [!NOTE]
> Innan du inaktiverar en StorSimple-fysisk enhet eller molninstallation stoppar eller tar du bort klienter och värdar som är beroende av den enheten.


## <a name="deactivate-and-delete-data"></a>Inaktivera och ta bort data

Om du är intresserad av att ta bort enheten helt och inte vill behålla data på enheten, slutför du följande steg.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Så här inaktiverar du enheten och tar bort data

1. Innan du inaktiverar en enhet måste du ta bort alla volymbehållare (och volymer) som är associerade med enheten. Du kan bara ta bort volymbehållare när du har tagit bort de associerade säkerhetskopiorna.

    > [!NOTE]
    > Innan du inaktiverar en StorSimple-fysisk enhet eller molninstallation ska du se till att data från den borttagna volymbehållaren faktiskt tas bort från enheten. Du kan övervaka molnförbrukningsdiagrammen och när du ser att molnanvändningen släpps på grund av de säkerhetskopior du har tagit bort, kan du fortsätta att inaktivera enheten. Om du inaktiverar enheten innan den här släppningen inträffar är data strandsatta i lagringskontot och samlar på avgifter.

2. Inaktivera enheten enligt följande:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I bladet **Enheter** väljer du den enhet som du vill inaktivera, högerklickar och klickar sedan på **Inaktivera**.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Skriv enhetsnamnet som ska bekräftas i bladet **Inaktivera** och klicka sedan på **Inaktivera**. Avaktiveringsprocessen startar och tar några minuter att slutföra.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. När du har inaktiverat den kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan då inte längre hantera den borttagna enheten. Så här tar du bort enheten:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I bladet **Enheter** väljer du den inaktiverade enhet som du vill ta bort, högerklickar och klickar sedan på **Ta bort**.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Skriv enhetsnamnet som du vill bekräfta i bladet **Ta bort** och klicka sedan på **Ta bort**. Borttagningen tar några minuter att slutföra.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen har slutförts meddelas du. Enhetslistan uppdateras också för att återspegla borttagningen.

## <a name="deactivate-and-retain-data"></a>Inaktivera och behålla data

Om du är intresserad av att ta bort enheten men vill behålla data gör du så här:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Så här inaktiverar du en enhet och behåller data
1. Inaktivera enheten. Alla volymbehållare och ögonblicksbilder av enheten finns kvar.
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I bladet **Enheter** väljer du den enhet som du vill inaktivera, högerklickar och klickar sedan på **Inaktivera**.

         ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Skriv enhetsnamnet som ska bekräftas i bladet **Inaktivera** och klicka sedan på **Inaktivera**. Avaktiveringsprocessen startar och tar några minuter att slutföra.

         ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Du kan nu växla över volymbehållarna och de associerade ögonblicksbilderna. Om du vill ha procedurer går du till [Redundans och haveriberedskap för Din StorSimple-enhet](storsimple-8000-device-failover-disaster-recovery.md).
3. När du har inaktiverat och redundans kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan då inte längre hantera den borttagna enheten. Så här tar du bort enheten:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. I bladet **Enheter** väljer du den inaktiverade enhet som du vill ta bort, högerklickar och klickar sedan på **Ta bort**.

       ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Skriv enhetsnamnet som du vill bekräfta i bladet **Ta bort** och klicka sedan på **Ta bort**. Borttagningen tar några minuter att slutföra.

       ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen har slutförts meddelas du. Enhetslistan uppdateras också för att återspegla borttagningen.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Inaktivera och ta bort en molninstallation

För en StorSimple Cloud Appliance, avaktivering från portalen frigör och tar bort den virtuella datorn och de resurser som skapades när den etablerades. När molninstallationen har inaktiverats kan den inte återställas till sitt tidigare tillstånd.

![Inaktivera StorSimple Cloud Appliance](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Inaktivering resulterar i följande åtgärder:

* StorSimple Cloud Appliance tas bort från tjänsten.
* Den virtuella datorn för StorSimple Cloud Appliance tas bort.
* Os-disken och datadiskarna som skapats för StorSimple Cloud Appliance behålls. Om du inte använder dessa entiteter bör du ta bort dem manuellt.
* Den värdbaserade tjänsten och det virtuella nätverket som skapades under etableringen behålls. Om du inte använder dessa entiteter bör du ta bort dem manuellt.
* Ögonblicksbilder av molnet som skapats av StorSimple Cloud Appliance behålls.

När molninstallationen har inaktiverats kan du ta bort den från listan över enheter. Markera den inaktiverade enheten, högerklicka och klicka sedan på **Ta bort**. StorSimple meddelar dig när enheten har tagits bort och listan över enheter uppdateras.

## <a name="next-steps"></a>Nästa steg

* Om du vill återställa den inaktiverade enheten till fabriksinställningar går du till [Återställ enheten till fabriksinställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Kontakta Microsoft [Support](storsimple-8000-contact-microsoft-support.md)om du behöver teknisk hjälp .
* Om du vill veta mer om hur du använder Tjänsten StorSimple Device Manager går du till [Använd Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

