---
title: "Redundansväxling och återställning vid fel för virtuella Azure-datorer som replikeras till en sekundär Azure-region med Azure Site Recovery (förhandsversion)"
description: "Lär dig hur du utför redundansväxling och återställning vid fel för virtuella Azure-datorer som replikeras till en sekundär Azure-region med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b45d1287444d200727550a81ce72a19a417fe510
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Redundansväxling och återställning vid fel för virtuella Azure-datorer mellan Azure-regioner (förhandsversion)

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och Azure virtuella datorer.

Den här självstudien beskriver hur en virtuell Azure-dator redundansväxlas till en sekundär Azure-region. När du har redundansväxlat kan du återställa till den primära regionen när den är tillgänglig. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Redundansväxla en virtuell Azure-dator
> * Återaktivera skyddet för den sekundära virtuella Azure-datorn så att den replikeras till den primära regionen
> * Återställa den sekundära virtuella datorn
> * Återaktivera skyddet för den primära virtuella datorn så den replikeras till den sekundära regionen

## <a name="prerequisites"></a>Nödvändiga komponenter

- Se till att du genomfört ett [programåterställningstest](azure-to-azure-tutorial-dr-drill.md) och kontrollerat att allt fungerar som väntat.
- Verifiera den virtuella datorns egenskaper innan testet av redundansväxling körs. Den virtuella datorn måste uppfylla [kraven för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="run-a-failover-to-the-secondary-region"></a>Utför en redundansväxling till den sekundära regionen

1. I **Replikerade objekt** väljer du den virtuella dator som ska redundansväxlas > **Redundans**

   ![Redundans](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:

   * **Senaste** (standard): Detta alternativ bearbetar alla data i Site Recovery-tjänsten och ger lägsta mål för återställningspunkt (RPO).
   * **Senaste bearbetade**: Alternativet återställer den virtuella datorn till den senaste återställningspunkt som bearbetats av Site Recovery-tjänsten.
   * **Anpassad**: Använd detta alternativ för att redundansväxla till en specifik återställningspunkt. Det här alternativet är användbart för att utföra test av redundansväxling.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan du utlöser redundansväxling. Redundansväxlingen fortsätter även om avstängningen misslyckas.

4. Följ redundansförloppet på sidan **Jobb**.

5. Efter redundansväxlingen verifierar du den virtuella datorn genom att logga in på den. Om du vill använda en annan återställningspunkt för den virtuella datorn kan du använda alternativet **Ändra återställningspunkt**.

6. När du kontrollerat den redundansväxlade virtuella datorn kan du **Bekräfta** redundansväxlingen.
   När du bekräftar tas alla återställningspunkter bort i tjänsten. Alternativet **Ändra återställningspunkt** är inte längre tillgängligt.

## <a name="reprotect-the-secondary-vm"></a>Återaktivera skyddet för den sekundära virtuella datorn

När den virtuella datorn redundansväxlats måste du återaktivera skyddet för den så att den replikeras tillbaka till den primära regionen.

1. Se till att den virtuella datorn har läget **redundansväxling bekräftad** och kontrollera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.
2. I **Vault** > **Replikerade objekt** högerklickar du på den redundansväxlade virtuella datorn och väljer sedan **Återaktivera skydd**.

   ![Högerklicka för att återaktivera skyddet](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Lägg märke till att skyddets riktning, från sekundär till primär region, redan är vald.
3. Granska informationen om **resursgrupp, nätverk, lagring och tillgänglighetsuppsättningar**. Alla markerade resurser (ny) skapas som en del av återaktiveringen av skyddet.
4. Klicka på **OK** för att utföra jobbet att återaktivera skyddet. Jobbet överför de senaste data som är tillgängliga till målplatsen. Sedan replikerar det deltan till den primära regionen. Den virtuella datorn är nu i ett skyddat läge.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När de virtuella datorerna åter skyddats kan du växla tillbaka till den primära regionen efter behov. Följ instruktionerna för [redundansväxling](#run-a-failover).
