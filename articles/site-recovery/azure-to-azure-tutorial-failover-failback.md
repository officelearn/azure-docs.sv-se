---
title: Redundans och återaktivera skyddet av virtuella Azure-datorer replikeras till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
description: Lär dig hur du utför redundansväxling och återaktivera skyddet av virtuella Azure-datorer replikeras till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/29/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 95e4e3f3acc52c230405f0c0cc4a05b03b21a386
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153791"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Redundans och återaktivera skyddet av virtuella Azure-datorer mellan regioner

Den här självstudien beskrivs hur du växlar över en Azure-dator (VM) till en sekundär Azure-region med de [Azure Site Recovery](site-recovery-overview.md) service. När du har redundansväxlat kan skydda den virtuella datorn igen. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Redundansväxla en virtuell Azure-dator
> * Återaktivera skyddet för den sekundära virtuella Azure-datorn så att den replikeras till den primära regionen.

> [!NOTE]
> Den här självstudien innehåller enklaste sökvägen med standardinställningar och minsta anpassning. För mer komplicerade scenarier kan du använda artiklar under ”hur till” för virtuella Azure-datorer.


## <a name="prerequisites"></a>Nödvändiga komponenter

- Innan du börjar bör du granska [vanliga frågor och svar](site-recovery-faq.md#failover) om redundans.
- Se till att du genomfört ett [programåterställningstest](azure-to-azure-tutorial-dr-drill.md) och kontrollerat att allt fungerar som väntat.
- Verifiera den virtuella datorns egenskaper innan testet av redundansväxling körs. Den virtuella datorn måste uppfylla [kraven för Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Utför en redundansväxling till den sekundära regionen

1. I **Replikerade objekt** väljer du den virtuella dator som ska redundansväxlas > **Redundans**

   ![Redundans](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:

   * **Senaste** (standard): Bearbetar alla data i Site Recovery-tjänsten och ger den lägsta mål för återställningspunkt (RPO).
   * **Senaste bearbetade**: Återställer den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery-tjänsten.
   * **Anpassat**: Flyttas över till en specifik återställningspunkt. Det här alternativet är användbart för att utföra test av redundansväxling.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery för att stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Site Recovery inte rensa upp källan efter en redundansväxling.

4. Följ redundansförloppet på sidan **Jobb**.

5. Efter redundansväxlingen verifierar du den virtuella datorn genom att logga in på den. Om du vill använda en annan återställningspunkt för den virtuella datorn kan du använda alternativet **Ändra återställningspunkt**.

6. När du kontrollerat den redundansväxlade virtuella datorn kan du **Bekräfta** redundansväxlingen.
   När du bekräftar tas alla återställningspunkter bort i tjänsten. Du kan inte nu ändra dess återställningspunkt.

> [!NOTE]
> När du redundansväxlar en virtuell dator som du lägger till en disk när du har aktiverat replikering för den virtuella datorn visas replikering punkter de diskar som är tillgängliga för återställning. Till exempel om en virtuell dator har en enskild disk och du lägger till en ny, visas replikering punkter som har skapats innan du har lagt till disken att replikering punkten består av ”1 av 2 diskar”.

![Växla över med en disk som har lagts till](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Återaktivera skyddet för den sekundära virtuella datorn

När den virtuella datorn redundansväxlats måste du återaktivera skyddet för den så att den replikeras tillbaka till den primära regionen.

1. Se till att den virtuella datorn har läget **redundansväxling bekräftad** och kontrollera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.
2. I **Vault** > **Replikerade objekt** högerklickar du på den redundansväxlade virtuella datorn och väljer sedan **Återaktivera skydd**.

   ![Högerklicka för att återaktivera skyddet](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Kontrollera att skyddets riktning, sekundär till primär region har redan valts.
3. Granska informationen om **resursgrupp, nätverk, lagring och tillgänglighetsuppsättningar**. Alla markerade som nya resurser skapas som en del av återaktiveringen av.
4. Klicka på **OK** för att utföra jobbet att återaktivera skyddet. Jobbet överför de senaste data som är tillgängliga till målplatsen. Sedan replikerar det deltan till den primära regionen. Den virtuella datorn är nu i ett skyddat läge.

## <a name="next-steps"></a>Nästa steg
- Efter att skydda, [Lär dig hur du](azure-to-azure-tutorial-failback.md) växla tillbaka till den primära regionen när den är tillgänglig.
- [Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om flödet återaktiveringen av skyddet.
