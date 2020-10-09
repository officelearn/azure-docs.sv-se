---
title: Återställa virtuella Azure-datorer till en primär region med tjänsten Azure Site Recovery.
description: Beskriver hur du växlar tillbaka virtuella Azure-datorer till den primära regionen med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 432c92bcfa8a2e0df26adf1516f5bdc9ee73d267
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502383"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Återställa en virtuell Azure-dator mellan Azure-regioner

[Azure Site Recoverys](site-recovery-overview.md) tjänsten bidrar till din strategi för haveri beredskap genom att hantera och dirigera replikering, redundans och återställning efter fel för lokala datorer och virtuella datorer i Azure.

I den här självstudien beskrivs hur du växlar tillbaka en enskild virtuell Azure-dator. När du har växlat över måste du växla tillbaka till den primära regionen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> 
> * Återställa den virtuella datorn i den sekundära regionen.
> * Skydda den primära virtuella datorn igen till den sekundära regionen.
> 
> [!NOTE]
> 
> Den här självstudien hjälper dig att redundansväxla ett fåtal virtuella datorer till en mål region och tillbaka till käll regionen med minimal anpassning. Mer detaljerade instruktioner finns i [instruktions guiderna på virtuella Azure-datorer](../virtual-machines/windows/index.yml).

## <a name="before-you-start"></a>Innan du börjar

* Kontrol lera att statusen för den virtuella datorn är **redundans bekräftad**.
* Kontrol lera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.
* Se till att skydd är aktiverat.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När de virtuella datorerna har återskyddats kan du växla tillbaka till den primära regionen efter behov.

1. I valvet väljer du **replikerade objekt**och väljer sedan den virtuella dator som har återskyddats.

    ![Skärm bild som visar återställning efter fel till primär i Azure Portal.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. I **replikerade objekt**väljer du den virtuella datorn och väljer sedan **redundans**.
3. I **redundans**väljer du en återställnings punkt att redundansväxla till:
    - **Senaste (standard)**: bearbetar alla data i site Recoverys tjänsten och ger lägsta återställnings punkt mål (återställnings punkt mål).
    - **Senaste bearbetade**: återställer den virtuella datorn till den senaste återställnings punkten som har bearbetats av Site Recovery.
    - **Anpassad**: växlar över till en viss återställnings punkt. Det här alternativet är användbart för att utföra test av redundansväxling.
4. Välj **Stäng datorn innan du påbörjar redundans** om du vill att Site Recovery ska försöka stänga av virtuella datorer i Dr-regionen innan redundansväxlingen utlöses. Redundansväxlingen fortsätter även om avstängningen Miss lyckas. 
5. Följ redundansförloppet på sidan **Jobb**.
6. När redundansväxlingen är klar verifierar du den virtuella datorn genom att logga in på den. Du kan ändra återställnings punkten efter behov.
7. När du har verifierat redundansväxlingen väljer du **genomför redundansväxlingen**. Commit tar bort alla tillgängliga återställnings punkter. Alternativet ändra återställnings punkt är inte längre tillgängligt.
8. Den virtuella datorn ska visa som misslyckad över och kunde inte återställas.

    ![Skärm bild som visar VM i primär och sekundär region.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> För datorer som använder hanterade diskar och kör Site Recovery Extension-versionen 9.28. x. x och med [Samlad uppdatering 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery rensar datorer i den sekundära Disaster Recovery-regionen efter att återställning efter fel har slutförts och virtuella datorer skyddas på nytt. Det finns inget behov av att manuellt ta bort virtuella datorer och nätverkskort i den sekundära regionen. Observera att virtuella datorer med ohanterade diskar inte rensas. Om du inaktiverar replikeringen helt efter att du har återställt igen rensar Site Recovery diskarna i Disaster Recovery-regionen, utöver de virtuella datorerna och nätverkskorten.

## <a name="next-steps"></a>Nästa steg

[Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om återskydds flödet.
