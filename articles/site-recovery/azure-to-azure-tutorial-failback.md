---
title: Inte Azure IaaS-datorer som replikeras till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
description: Lär dig hur du växlar tillbaka Azure virtuella datorer med Azure Site Recovery-tjänsten.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089717"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Växla tillbaka Azure virtuella datorer mellan Azure-regioner

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och misslyckas igen av lokala datorer och virtuella Azure-datorer (VM).

Den här självstudien beskrivs hur du inte återställa en virtuell Azure-dator. När du har redundansväxlat kan du återställa till den primära regionen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> 
> * Återställa den sekundära virtuella datorn
> * Skydda igen den primära virtuella datorn tillbaka till den sekundära regionen
> 
> [!NOTE]
> 
> Den här kursen är avsedd som vägleder användaren genom stegen för att redundansväxla till en målregion och tillbaka med minsta anpassning; Om du vill veta mer om de olika delarna som är associerade med växling vid fel, bland annat nätverk tänka avser automation eller felsökning, dokument under ”hur till” för virtuella Azure-datorer.

## <a name="prerequisites"></a>Förutsättningar

> * Se till att den virtuella datorn är i tillståndet Redundansväxling bekräftad och kontrollera att den primära regionen är tillgänglig och kan skapa och komma åt nya resurser i den.
> * Kontrollera att nytt skydd är aktiverat.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När virtuella datorer är skyddad igen kan växla du tillbaka till den primära regionen som och när du vill.

1. Gå till ditt Recovery Services-valv. Klicka på replikerade objekt och välj den virtuella dator som har fått nytt skydd.

2. Du bör se följande. Observera att den liknar bladet för testning av redundans och växling vid fel från den primära regionen.
![Växla tillbaka till primär](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Klicka på Testa redundans för att genomföra ett redundanstest tillbaka till den primära regionen. Välj återställningspunkten och virtuellt nätverk för redundanstestning och välj OK. Du kan se testet VM som skapats i den primära regionen som du kan komma åt och granska.

4. När du är nöjd med testa redundans kan du klicka på Rensa redundanstestning för att rensa upp resurserna som du skapade i källregionen för redundanstestning.

5. I replikerat objekt, väljer du den virtuella datorn som du vill redundansväxla > redundans.

6. Välj en återställningspunkt för att redundansväxla till i redundans. Du kan välja något av följande alternativ:
    1. Senaste (standard): Det här alternativet bearbetar alla data i Site Recovery-tjänsten och ger den lägsta mål för återställningspunkt (RPO)
    2. Senaste bearbetade: Det här alternativet återställer den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery-tjänsten
    3. Anpassat: Använd det här alternativet ska gå över till en specifik återställningspunkt. Det här alternativet är användbart för att utföra ett redundanstest

7. Välj Stäng av datorn innan du påbörjar redundans om du vill använda Site Recovery för att stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Observera att Site Recovery inte rensar källan efter en redundansväxling.

8. Följ redundansförloppet på sidan för jobbsökning

9. Efter redundansväxlingen verifierar du den virtuella datorn genom att logga in på den. Om du vill använda en annan återställningspunkt för den virtuella datorn kan du använda ändra återställningsalternativ punkt.

10. När du har kontrollerat den redundansväxlade virtuella datorn kan du etablerar redundansen. När du bekräftar tas alla återställningspunkter bort i tjänsten. Ändra recovery point alternativet är inte längre tillgängligt.

![Virtuell dator på primära och sekundära regioner](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Som du ser i föregående skärmbild, ”ContosoWin2016”, har den virtuella datorn redundansväxlats från USA, centrala till USA, östra och tillbaka från USA, östra till USA, centrala.

Observera att DR-VMs förblir med frigjord avstängning. Det här beteendet är avsiktligt eftersom Azure Site Recovery sparar information för den virtuella datorn. Den kan användas vid redundans för den primära till den sekundära regionen senare. Du debiteras inte för frigjord virtuella datorer, så bör hållas eftersom den är.

> [!NOTE]
> Se den [”så här” avsnittet](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) för mer information om arbetsflödet nytt skydd och vad som händer vid nytt skydd.
