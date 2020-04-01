---
title: Red tillbaka virtuella Azure-datorer till en primär region med Azure Site Recovery-tjänsten.
description: Beskriver hur du misslyckas med att återställa virtuella Azure-datorer till den primära regionen med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091346"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Red tillbaka en virtuell Azure-dator mellan Azure-regioner

[Azure Site Recovery-tjänsten](site-recovery-overview.md) bidrar till din katastrofåterställningsstrategi genom att hantera och samordna replikering, redundans och återställning av lokala datorer och virtuella Azure-datorer (VIRTUELLA datorer).

Den här självstudien beskriver hur du kan återställa en enda Azure-virtuell dator. När du har misslyckats över måste du växla tillbaka till den primära regionen när den är tillgänglig. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> 
> * Red tillbaka den virtuella datorn i den sekundära regionen.
> * Reprotect den primära virtuella datorn tillbaka till den sekundära regionen.
> 
> [!NOTE]
> 
> Den här självstudien hjälper dig att växla över några virtuella datorer till en målregion och tillbaka till källregionen med minsta anpassningar. Mer detaljerade instruktioner finns i [instruktionsguiderna för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Innan du börjar

* Kontrollera att statusen för den virtuella datorn har **failover-bekräftad**.
* Kontrollera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.
* Kontrollera att återskydd är aktiverat.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När virtuella datorer har reprotected, kan du växla tillbaka till den primära regionen efter behov.

1. Välj **Replikerade objekt**i valvet och välj sedan den virtuella datorn som har återställts igen.

    ![Återgång till primär](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. I **Replikerade objekt**väljer du den virtuella datorn och väljer sedan **Redundans**.
3. I **Redundans**väljer du en återställningspunkt som ska växlas över till:
    - **Senaste (standard)**: Bearbetar alla data i tjänsten Site Recovery och ger det lägsta återställningspunktmålet (RPO).
    - **Senast bearbetad:** Återställer den virtuella datorn till den senaste återställningspunkten som har bearbetats av Site Recovery.
    - **Anpassad:** Växlar inte över till en viss återställningspunkt. Det här alternativet är användbart för att utföra test av redundansväxling.
4. Välj **Stäng av datorn innan du påbörjar redundans** om du vill att Site Recovery ska försöka stoppa virtuella datorer i DR-regionen innan du utlöser redundansen. Redundansen fortsätter även om avstängningen misslyckas. 
5. Följ redundansförloppet på sidan **Jobb**.
6. När redundansen har slutförts validerar du den virtuella datorn genom att logga in på den. Du kan ändra återställningspunkten efter behov.
7. När du har verifierat redundansen väljer du **Genomför redundansen**. Om du genomför tar alla tillgängliga återställningspunkter bort. Alternativet ändra återställningspunkt är inte längre tillgängligt.
8. Den virtuella datorn ska visas som misslyckades över och misslyckades tillbaka.

    ![Virtuell dator i primära och sekundära regioner](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> För datorer som kör tilläggsversionen av Site Recovery version 9.28.x.x och framåt [Samlad uppdatering 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Rensar rensning av plats återställningsmaskiner i den sekundära katastrofåterställningsregionen, när återställningen av återställningen efter har slutförts och virtuella datorer har skyddats på ett till- och med- Det finns ingen anledning att manuellt ta bort virtuella datorer och nätverkskort i den sekundära regionen. Om du inaktiverar replikering helt efter att ha misslyckats rensar Site Recovery diskarna i katastrofåterställningsregionen, förutom de virtuella datorerna och nätverkskorten.

## <a name="next-steps"></a>Nästa steg

[Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om återskyddsflödet.
