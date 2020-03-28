---
title: Växla över och rotera azure-virtuella datorer som replikeras till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
description: Lär dig hur du växlar över och roterar om virtuella Azure-datorer som replikeras till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68782597"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Växla över och rotera azure-virtuella datorer mellan regioner

Den här självstudien beskriver hur du växlar över en virtuell Azure-dator (VM) till en sekundär Azure-region med [Azure Site Recovery-tjänsten.](site-recovery-overview.md) När du har misslyckats över, du reprotect den virtuella datorn. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Redundansväxla en virtuell Azure-dator
> * Reprotect den sekundära Azure VM, så att den replikeras till den primära regionen.

> [!NOTE]
> Den här självstudien innehåller den enklaste sökvägen med standardinställningar och minsta anpassning. Mer komplexa scenarier använder du artiklarna under "Så här gör du" för virtuella Azure-datorer.


## <a name="prerequisites"></a>Krav

- Innan du börjar bör du granska [vanliga frågor](site-recovery-faq.md#failover) om redundans.
- Se till att du genomfört ett [programåterställningstest](azure-to-azure-tutorial-dr-drill.md) och kontrollerat att allt fungerar som väntat.
- Verifiera den virtuella datorns egenskaper innan testet av redundansväxling körs. Den virtuella datorn måste uppfylla [kraven för Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Utför en redundansväxling till den sekundära regionen

1. I **Replikerade objekt** väljer du den virtuella dator som ska redundansväxlas > **Redundans**

   ![Redundans](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:

   * **Senaste** (standard): Bearbetar alla data i tjänsten Webbplatsåterställning och ger det lägsta återställningspunktmålet (RPO).
   * **Senast bearbetad:** Återställer den virtuella datorn till den senaste återställningspunkten som har bearbetats av site recovery-tjänsten.
   * **Anpassad:** Växlar inte över till en viss återställningspunkt. Det här alternativet är användbart för att utföra test av redundansväxling.

3. Välj **Stäng av datorn innan du påbörjar redundans** om du vill att Site Recovery ska försöka göra en avstängning av virtuella käll-datorer innan du utlöser redundansen. Avstängning bidrar till att säkerställa ingen dataförlust. Redundansen fortsätter även om avstängningen misslyckas. Platsåterställning rensar inte källan efter redundans.

4. Följ redundansförloppet på sidan **Jobb**.

5. Efter redundansväxlingen verifierar du den virtuella datorn genom att logga in på den. Om du vill använda en annan återställningspunkt för den virtuella datorn kan du använda alternativet **Ändra återställningspunkt**.

6. När du kontrollerat den redundansväxlade virtuella datorn kan du **Bekräfta** redundansväxlingen.
   När du bekräftar tas alla återställningspunkter bort i tjänsten. Du kommer inte nu att kunna ändra återställningspunkten.

> [!NOTE]
> När du växlar över en virtuell dator som du lägger till en disk efter att du har aktiverat replikering för den virtuella datorn, visar replikeringspunkterna de diskar som är tillgängliga för återställning. Om en virtuell dator till exempel har en enda disk och du lägger till en ny, visar replikeringspunkter som skapades innan du lade till disken att replikeringspunkten består av "1 av 2 diskar".

![Växla över med en tillagd disk](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Återaktivera skyddet för den sekundära virtuella datorn

När den virtuella datorn redundansväxlats måste du återaktivera skyddet för den så att den replikeras tillbaka till den primära regionen.

1. Se till att den virtuella datorn har läget **redundansväxling bekräftad** och kontrollera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.
2. Högerklicka på den virtuella datorn som har misslyckats över i >  **Arkiv-replikerade****objekt**och välj sedan Skydda igen . **Vault**

   ![Högerklicka för att återaktivera skyddet](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Kontrollera att skyddsriktningen, sekundär till primär region, redan är markerad.
3. Granska informationen om **resursgrupp, nätverk, lagring och tillgänglighetsuppsättningar**. Alla resurser som markerats som nya skapas som en del av reprotect-åtgärden.
4. Klicka på **OK** för att utföra jobbet att återaktivera skyddet. Jobbet överför de senaste data som är tillgängliga till målplatsen. Sedan replikerar det deltan till den primära regionen. Den virtuella datorn är nu i ett skyddat läge.

## <a name="next-steps"></a>Nästa steg
- När du har återbeskyddat [kan du lära dig hur du](azure-to-azure-tutorial-failback.md) växlar tillbaka till den primära regionen när den är tillgänglig.
- [Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) om återskyddsflödet.
