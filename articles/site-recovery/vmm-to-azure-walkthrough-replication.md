---
title: "Konfigurera en replikeringsprincip för Hyper-V-dator (med VMM) replikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du ställer in en replikeringsprincip för Hyper-V-dator (med VMM) replikering till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 592e1c3f647e5b1f1d9aa776657e8f89b60349e1
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>Steg 10: Ställ in en replikeringsprincip för Virtuella Hyper-V-replikering (med VMM) till Azure


När du har installerat [nätverksmappning](vmm-to-azure-walkthrough-network-mapping.md), Använd den här artikeln för att konfigurera en replikeringsprincip T\to replikera lokala Hyper-V virtuella datorer som hanteras i System Center Virtual Machine Manager (VMM)-moln till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="create-a-policy"></a>Skapa en princip

1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.

    ![Nätverk](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. I **Princip för att skapa och koppla** anger du ett principnamn.
3. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).

    > [!NOTE]
    >  En frekvens på 30 sekunder stöds inte när du replikerar till premiumlagring. Begränsningen bestäms av antalet ögonblicksbilder per blob (100) som stöds av premium-lagring. [Läs mer](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden i antal timmar för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en period.
5. I **Appkompatibel ögonblicksbildsfrekvens** anger du hur ofta (1–12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Hyper-V använder två typer av ögonblicksbilder: en standardögonblicksbild som tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn och en programkonsekvent ögonblicksbild som tar en ögonblicksbild vid en viss tidpunkt av programdata på den virtuella datorn. Programkonsekventa ögonblicksbilder använda VSS (Volume Shadow Copy Service) för att säkerställa att programmen är i ett konsekvent tillstånd när ögonblicksbilden tas. Observera att om du aktiverar programkonsekventa ögonblicksbilder så påverkar detta prestanda för program som körs på virtuella källdatorer. Kontrollera att värdet som du anger är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
6. I **Starttid för inledande replikering** anger du när den inledande replikeringen ska börja. Replikeringen sker via Internetbandbredden så du kanske vill schemalägga den utanför kontorstid.
7. I **Kryptera data lagrade på Azure** anger du om du vill kryptera vilande data i Azure-lagring. Klicka sedan på **OK**.

    ![Replikeringsprincip](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. När du skapar en ny princip associeras den automatiskt med VMM-molnet. Klicka på **OK**. Du kan associera ytterligare VMM-moln (och de virtuella datorerna i dem) med den här replikeringsprincipen i **Replikering** > principnamn > **Associera VMM-moln**.

    ![Replikeringsprincip](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>Nästa steg

Gå till [steg 11: Aktivera replikering](vmm-to-azure-walkthrough-enable-replication.md)
