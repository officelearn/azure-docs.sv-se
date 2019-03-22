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
ms.openlocfilehash: c8ce05e644ad556542314b17151b808586734824
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315325"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Växla tillbaka Azure virtuella datorer mellan Azure-regioner

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och misslyckas igen av lokala datorer och virtuella Azure-datorer (VM).

Den här självstudien beskrivs hur du inte återställa en virtuell Azure-dator. När du har redundansväxlat kan du återställa till den primära regionen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> 
> * Återställ virtuella virtuella Azure-datorer i den sekundära regionen.
> * Skydda igen den primära Azure-VM tillbaka till den sekundära regionen.
> 
> [!NOTE]
> 
> Den här kursen hjälper dig att växla över några virtuella datorer till en målregion och tillbaka till källregionen med minsta anpassningar. Mer detaljerad information, se på artiklar under ”hur till” för virtuella Azure-datorer.

## <a name="before-you-start"></a>Innan du börjar

> * Kontrollera att Virtuellt datorn i den **Redundansväxling bekräftad** tillstånd.
> * Kontrollera att den primära regionen är tillgänglig och kan skapa och komma åt nya resurser i den.
> * Kontrollera att nytt skydd är aktiverat.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När virtuella datorer är skyddad igen kan växla du tillbaka till den primära regionen efter behov.

1. I valvet, klickar du på **replikerade objekt** och välj den virtuella datorn åter skyddades.

    ![Växla tillbaka till primär](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Klicka på **Redundanstestning** för att utföra ett test failover tillbaka till den primära regionen.
4. Välj återställningspunkten och virtuellt nätverk för redundanstestning och klicka på **OK**. Du kan granska testet VM som skapats i den primära regionen.
5. När redundanstestet har slutförts klickar du på **Rensa redundanstestning** att rensa upp resurserna som du skapade i källregionen för redundanstestning.
6. I **replikerade objekt**, Välj den virtuella datorn och på **redundans**.
7. I **redundans**, Välj en återställningspunkt för att redundansväxla till.
    - **Senaste (standard)**: Bearbetar alla data i Site Recovery-tjänsten och ger den lägsta mål för återställningspunkt (RPO).
    - **Senaste bearbetade**: Återställer den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery.
    - **Anpassat**: Flyttas över till en specifik återställningspunkt. Det här alternativet är användbart för att utföra test av redundansväxling.

8. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery för att stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Observera att Site Recovery inte rensa källan efter en redundansväxling.
9. Följ redundansförloppet på sidan **Jobb**.
10. Efter redundansväxlingen verifierar du den virtuella datorn genom att logga in på den. Du kan ändra dess återställningspunkt efter behov.
11. När du har kontrollerat växling vid fel, klickar du på **etablerar redundansen**. Genomför tar bort alla tillgängliga återställningspunkter. Ändra recovery point alternativet är inte längre tillgängligt.
12. Den virtuella datorn ska visa som växlas över och växlas tillbaka.

    ![Virtuell dator på primära och sekundära regioner](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Disaster recovery virtuella datorer kvar i avstängningen tillstånd har frigjorts. Det här är avsiktligt eftersom Site Recovery sparar VM-informationen, som kan vara användbart för redundans från primärt till den sekundära regionen senare. Du debiteras inte för de frigörs virtuella datorerna, så att de bör hållas som de är.

## <a name="next-steps"></a>Nästa steg

[Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om flödet återaktiveringen av skyddet.
