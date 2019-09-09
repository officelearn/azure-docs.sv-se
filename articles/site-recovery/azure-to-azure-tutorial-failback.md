---
title: Återställa virtuella Azure-datorer som replikeras till en sekundär Azure-region för haveri beredskap med tjänsten Azure Site Recovery.
description: Lär dig hur du växlar tillbaka virtuella Azure-datorer med tjänsten Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c8be547790452774992b9226ca8010532263aaff
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814516"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Återställa en virtuell Azure-dator mellan Azure-regioner

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

I den här självstudien beskrivs hur du växlar tillbaka en enskild virtuell Azure-dator. När du har växlat över måste du växla tillbaka till den primära regionen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> 
> * Återställa den virtuella datorn i den sekundära regionen.
> * Skydda den primära virtuella datorn igen till den sekundära regionen.
> 
> [!NOTE]
> 
> Den här självstudien hjälper dig att redundansväxla ett fåtal virtuella datorer till en mål region och tillbaka till käll regionen med minimal anpassning. Mer detaljerade instruktioner finns i [instruktions guiderna på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Innan du börjar

* Kontrol lera att statusen för den virtuella datorn är **redundans bekräftad**.
* Kontrol lera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.
* Se till att skydd är aktiverat.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När de virtuella datorerna har återskyddats kan du växla tillbaka till den primära regionen efter behov.

1. I valvet väljer du **replikerade objekt**och väljer sedan den virtuella dator som har återskyddats.

    ![Återställning efter fel till primär](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. I **replikerade objekt**väljer du den virtuella datorn och väljer sedan **redundans**.
3. I **redundans**väljer du en återställnings punkt att redundansväxla till:
    - **Senaste (standard)** : Bearbetar alla data i Site Recoverys tjänsten och ger lägsta återställnings punkt mål (återställnings punkt mål).
    - **Senaste bearbetade**: Återställer den virtuella datorn till den senaste återställnings punkten som har bearbetats av Site Recovery.
    - **Anpassat**: Växlar över till en viss återställnings punkt. Det här alternativet är användbart för att utföra test av redundansväxling.
4. Välj **Stäng datorn innan du påbörjar redundans** om du vill att Site Recovery ska försöka stänga av virtuella datorer i Dr-regionen innan redundansväxlingen utlöses. Redundansväxlingen fortsätter även om avstängningen Miss lyckas. 
5. Följ redundansförloppet på sidan **Jobb**.
6. När redundansväxlingen är klar verifierar du den virtuella datorn genom att logga in på den. Du kan ändra återställnings punkten efter behov.
7. När du har verifierat redundansväxlingen väljer du **genomför redundansväxlingen**. Commit tar bort alla tillgängliga återställnings punkter. Alternativet ändra återställnings punkt är inte längre tillgängligt.
8. Den virtuella datorn ska visa som misslyckad över och kunde inte återställas.

    ![VM i primär och sekundär region](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

## <a name="next-steps"></a>Nästa steg

[Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om återskydds flödet.
