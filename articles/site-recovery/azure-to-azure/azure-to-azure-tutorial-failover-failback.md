---
title: "Växla över och misslyckas tillbaka virtuella Azure-datorer replikeras till en sekundär region som Azure med Azure Site Recovery (förhandsgranskning)"
description: "Lär dig hur du växla över och återställas tillbaka replikering för virtuella Azure-datorer till en sekundär region som Azure med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5f37a7b3534102a06f6d5dc6fb91d3abee7c0522
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Växla över och återställas tillbaka Azure virtuella datorer mellan Azure-regioner (förhandsgranskning)

Den [Azure Site Recovery](../site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här självstudiekursen beskrivs hur du växlar över en enskild virtuell Azure-dator till en sekundär region i Azure. När du har redundansväxlats växlar du tillbaka till den primära regionen när den är tillgänglig. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Växla över Azure VM
> * Skapa nytt sekundära Azure VM, så att replikeras till den primära regionen
> * Växla tillbaka den sekundära virtuella datorn
> * Skyddar den primära virtuella datorn tillbaka till den sekundära regionen

## <a name="prerequisites"></a>Krav

- Kontrollera att du har slutfört en [disaster recovery ökad](azure-to-azure-tutorial-dr-drill.md) att kontrollera att allt fungerar som förväntat.
- Kontrollera egenskaper för Virtuella datorer innan du kör redundanstestet. Den virtuella datorn måste vara kompatibel med [krav för Azure](../site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="run-a-failover-to-the-secondary-region"></a>Kör en redundansväxling till den sekundära regionen

1. I **replikerade objekt**, Välj den virtuella datorn som du vill växla över > **växling vid fel**

   ![Redundans](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. I **redundans**, Välj en **återställningspunkt** att växla över till. Du kan använda något av följande alternativ:

   * **Senaste** (standard): det här alternativet bearbetar alla data i Site Recovery-tjänsten och ger lägst Recovery punkt mål (RPO).
   * **Senaste bearbetas**: det här alternativet återställer den virtuella datorn till den senaste återställningspunkten som har behandlats av Site Recovery-tjänsten.
   * **Anpassad**: Använd det här alternativet för att växla över till en viss återställningspunkt. Det här alternativet är användbart för att utföra ett test av redundansen.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan växling vid fel. Redundans fortsätter även om avstängning misslyckas.

4. Följa förloppet för växling vid fel på den **jobb** sidan.

5. Verifiera den virtuella datorn efter växling vid fel, genom att logga in till den. Om du vill gå en annan återställningspunkt för den virtuella datorn så att du kan använda **ändra återställningspunkt** alternativet.

6. När du är nöjd med den redundansväxlade virtuella datorn kan du **genomför** växling vid fel.
   Genomför tar bort alla återställningspunkter som är tillgängliga med tjänsten. Den **ändra återställningspunkt** alternativet är inte längre tillgänglig.

## <a name="reprotect-the-secondary-vm"></a>Skyddar den sekundära virtuella datorn

Du behöver skydda den igen så att replikeras tillbaka till den primära regionen efter redundans för den virtuella datorn.

1. Kontrollera att den virtuella datorn i den **redundans allokerat** tillstånd och kontrollera att den primära regionen är tillgänglig och att du ska kunna skapa och få åtkomst till nya resurser i den.
2. I **valvet** > **replikerade objekt**, högerklicka på den virtuella datorn som har växlas över och välj sedan **skydda igen**.

   ![Högerklicka om du vill skydda igen](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Observera att riktningen för skydd, sekundära till primära region redan är markerad.
3. Granska de **resursgrupp, nätverk, lagring och tillgänglighet anger** information. Alla resurser som är markerade (nya) skapas som en del av åtgärden skydda igen.
4. Klicka på **OK** att utlösa ett jobb som skyddar. Det här jobbet lägger målplatsen med den senaste informationen. Sedan replikerar den går till den primära regionen. Den virtuella datorn är nu i ett skyddat läge.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När virtuella datorer är återaktivera skyddet, kan du växla tillbaka till den primära regionen som du behöver. Gör detta genom att följa den [redundans](#run-a-failover) instruktioner.
