---
title: Redundansväxla och återaktivera skyddet av virtuella Azure-datorer som replikeras till en sekundär Azure-region för haveri beredskap med tjänsten Azure Site Recovery.
description: Lär dig hur du växlar över och skyddar virtuella Azure-datorer som replikeras till en sekundär Azure-region för haveri beredskap med tjänsten Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8d38aa513b0829c2626fcd4a92c40faabff1f83e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502400"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Redundansväxla och återaktivera skyddet av virtuella Azure-datorer mellan regioner

I den här självstudien beskrivs hur du växlar över en virtuell Azure-dator (VM) till en sekundär Azure-region med tjänsten [Azure Site Recovery](site-recovery-overview.md) . När du har växlat över skyddar du den virtuella datorn. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Redundansväxla en virtuell Azure-dator
> * Återaktivera skyddet för den sekundära virtuella Azure-datorn så att den replikeras till den primära regionen.

> [!NOTE]
> Den här självstudien innehåller den enklaste sökvägen med standardinställningar och minimal anpassning. För mer komplexa scenarier använder du artiklarna under "How to" för virtuella Azure-datorer.


## <a name="prerequisites"></a>Förutsättningar

- Innan du börjar bör du läsa igenom [vanliga frågor](site-recovery-faq.md#failover) om redundans.
- Se till att du genomfört ett [programåterställningstest](azure-to-azure-tutorial-dr-drill.md) och kontrollerat att allt fungerar som väntat.
- Verifiera den virtuella datorns egenskaper innan testet av redundansväxling körs. Den virtuella datorn måste uppfylla [kraven för Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Utför en redundansväxling till den sekundära regionen

1. I **Replikerade objekt** väljer du den virtuella dator som ska redundansväxlas > **Redundans**

   ![Skärm bild som visar alternativ för redundans för en virtuell dator.](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:

   * **Senaste** (standard): bearbetar alla data i site Recoverys tjänsten och ger lägsta återställnings punkt mål (återställnings punkt mål).
   * **Senaste bearbetade**: återställer den virtuella datorn till den senaste återställnings punkten som har bearbetats av site Recoverys tjänsten.
   * **Anpassad**: växlar över till en viss återställnings punkt. Det här alternativet är användbart för att utföra test av redundansväxling.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill att Site Recovery försöker stänga av virtuella käll datorer innan du utlöser redundansväxlingen. Avstängning bidrar till att förhindra data förlust. Redundansväxlingen fortsätter även om avstängningen misslyckas. Site Recovery rensar inte källan efter redundansväxlingen.

4. Följ redundansförloppet på sidan **Jobb**.

5. Efter redundansväxlingen verifierar du den virtuella datorn genom att logga in på den. Om du vill använda en annan återställningspunkt för den virtuella datorn kan du använda alternativet **Ändra återställningspunkt**.

6. När du kontrollerat den redundansväxlade virtuella datorn kan du **Bekräfta** redundansväxlingen.
   När du bekräftar tas alla återställningspunkter bort i tjänsten. Du kan nu inte ändra återställnings punkten.

> [!NOTE]
> När du växlar över en virtuell dator som du lägger till en disk efter att du har aktiverat replikering för den virtuella datorn, visar replikerings platserna de diskar som är tillgängliga för återställning. Om en virtuell dator till exempel har en enskild disk och du lägger till en ny, kommer de replikerings punkter som skapades innan du lade till disken att visa att replikerings platsen består av "1 av 2 diskar".

![Skärm bild som visar redundans med en extra disk.](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Återaktivera skyddet för den sekundära virtuella datorn

När den virtuella datorn redundansväxlats måste du återaktivera skyddet för den så att den replikeras tillbaka till den primära regionen.

1. Se till att den virtuella datorn har läget **redundansväxling bekräftad** och kontrollera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.
2. I **valv**  >  **replikerade objekt**högerklickar du på den virtuella datorn som har redundansväxlats och väljer sedan **skydda igen**.

   ![Skärm bild av alternativet för omskydd för en virtuell dator.](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Kontrol lera att skydds riktningen, den sekundära till den primära regionen, redan har valts.
3. Granska informationen om **resursgrupp, nätverk, lagring och tillgänglighetsuppsättningar**. Alla resurser som marker ATS som nya skapas som en del av återskydds åtgärden.
4. Klicka på **OK** för att utföra jobbet att återaktivera skyddet. Jobbet överför de senaste data som är tillgängliga till målplatsen. Sedan replikerar det deltan till den primära regionen. Den virtuella datorn är nu i ett skyddat läge.

## <a name="next-steps"></a>Nästa steg
- Efter återaktivering av skyddet, [lär du dig hur du](azure-to-azure-tutorial-failback.md) växlar tillbaka till den primära regionen när den är tillgänglig.
- [Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om återskydds flödet.
