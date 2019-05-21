---
title: Inte Azure virtuella datorer som replikeras till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
description: Lär dig hur du växlar tillbaka Azure virtuella datorer med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 339cbf18d79053dfb0704d928b8c9251c73b3a6e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951783"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Återställa en Azure virtuell dator mellan Azure-regioner

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien beskrivs hur du inte återställa en virtuell Azure-dator. När du har redundansväxlat kan växla du tillbaka till den primära regionen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> 
> * Återställa den virtuella datorn i den sekundära regionen.
> * Återaktivera skyddet för den primära virtuella datorn tillbaka till den sekundära regionen.
> 
> [!NOTE]
> 
> Den här kursen hjälper dig att växla över några virtuella datorer till en målregion och tillbaka till källregionen med minsta anpassningar. Mer detaljerad information, granska de [anvisningar hjälper på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Innan du börjar

* Se till att statusen för den virtuella datorn är **Redundansväxling bekräftad**.
* Kontrollera att den primära regionen är tillgänglig och att det går att skapa och komma åt nya resurser i den.
* Kontrollera att det återaktivering av skyddet är aktiverat.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När virtuella datorerna åter skyddats kan växla du tillbaka till den primära regionen efter behov.

1. I valvet, väljer **replikerade objekt**, och välj sedan den virtuella datorn som har återaktivera skyddet.

    ![Växla tillbaka till primär](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Välj **Redundanstest** för att utföra ett test failover tillbaka till den primära regionen.
4. Välj återställningspunkten och virtuellt nätverk för redundanstestning och välj sedan **OK**. Du kan granska testet VM som skapats i den primära regionen.
5. När redundanstestet har slutförts väljer **Rensa redundanstestning** att rensa upp resurserna som du skapade i källregionen för redundanstestning.
6. I **replikerade objekt**, Välj den virtuella datorn och välj sedan **redundans**.
7. I **redundans**, Välj en återställningspunkt för att växla över till:
    - **Senaste (standard)**: Bearbetar alla data i Site Recovery-tjänsten och ger lägsta mål för återställningspunkt (RPO).
    - **Senaste bearbetade**: Återställer den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery.
    - **Anpassat**: Flyttas över till en specifik återställningspunkt. Det här alternativet är användbart för att utföra test av redundansväxling.

8. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery försöker stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Observera att Site Recovery inte rensa källan efter en redundansväxling.
9. Följ redundansförloppet på sidan **Jobb**.
10. När redundansväxlingen är klar, verifierar du den virtuella datorn genom att logga in på den. Du kan ändra dess återställningspunkt efter behov.
11. När du har kontrollerat växling vid fel, Välj **etablerar redundansen**. Genomför tar bort alla tillgängliga återställningspunkter. Ändra recovery point alternativet är inte längre tillgängligt.
12. Den virtuella datorn ska visa som växlas över och växlas tillbaka.

    ![Virtuell dator på primära och sekundära regioner](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Katastrofåterställning virtuella datorer kvar i avstängning/deallocated. Det här är avsiktligt eftersom Site Recovery sparar VM-informationen, som kan vara användbart för redundans från primärt till den sekundära regionen senare. Du debiteras inte för de frigörs virtuella datorerna, så att de bör hållas som de är.

## <a name="next-steps"></a>Nästa steg

[Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om flödet återaktiveringen av skyddet.
