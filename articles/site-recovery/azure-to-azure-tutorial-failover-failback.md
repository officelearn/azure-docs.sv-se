---
title: Redundansväxling och felåterställning av virtuella Azure IaaS-datorer replikerade till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
description: Lär dig om redundansväxling och felåterställning av virtuella Azure-datorer replikerade till en sekundär Azure-region för haveriberedskap med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 46dae28fd6c9eaa3d5e03f5f06c5e92449653679
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737730"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>Redundansväxling och felåterställning av virtuella Azure-datorer mellan Azure-regioner

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien beskriver hur en virtuell Azure-dator redundansväxlas till en sekundär Azure-region. När du har redundansväxlat kan du återställa till den primära regionen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Redundansväxla en virtuell Azure-dator
> * Återaktivera skyddet för den sekundära virtuella Azure-datorn så att den replikeras till den primära regionen
> * Återställa den sekundära virtuella datorn
> * Återaktivera skyddet för den primära virtuella datorn så den replikeras till den sekundära regionen

> [!NOTE]
> Den här självstudiekursen är avsedd att guida användaren genom stegen att växla över till en målregion med minsta möjliga anpassning. Mer information om de olika aspekterna som förknippas med redundans, till exempel nätverksöverväganden eller felsökning, finns i dokumenten under instruktionerna för virtuella Azure-datorer.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Se till att du genomfört ett [programåterställningstest](azure-to-azure-tutorial-dr-drill.md) och kontrollerat att allt fungerar som väntat.
- Verifiera den virtuella datorns egenskaper innan testet av redundansväxling körs. Den virtuella datorn måste uppfylla [kraven för Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Utför en redundansväxling till den sekundära regionen

1. I **Replikerade objekt** väljer du den virtuella dator som ska redundansväxlas > **Redundans**

   ![Redundans](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:

   * **Senaste** (standard): Detta alternativ bearbetar alla data i Site Recovery-tjänsten och ger lägsta mål för återställningspunkt (RPO).
   * **Senaste bearbetade**: Alternativet återställer den virtuella datorn till den senaste återställningspunkt som bearbetats av Site Recovery-tjänsten.
   * **Anpassad**: Använd detta alternativ för att redundansväxla till en specifik återställningspunkt. Det här alternativet är användbart för att utföra test av redundansväxling.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan du utlöser redundansväxling. Redundansväxlingen fortsätter även om avstängningen misslyckas. Observera att Site Recovery inte rensar källan efter en redundansväxling.

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

> [!NOTE]
> Se [avsnittet ”Så här gör du”](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) för mer information om arbetsflödets återaktivering av skydd och vad som händer vid återaktiveringen av skyddet.


## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När de virtuella datorerna åter skyddats kan du växla tillbaka till den primära regionen när du behöver det. Gör detta genom att konfigurera redundans från den sekundära till den primära regionen, enligt beskrivningen i den här artikeln.

![Högerklicka för att återaktivera skyddet](./media/azure-to-azure-tutorial-failover-failback/failback.png)

Som du ser i föregående skärmbild, ”ContosoWin2016”, har den virtuella datorn redundansväxlats från USA, centrala till USA, östra och tillbaka från USA, östra till USA, centrala.

Redundansen stänger av den virtuella datorn i den sekundära regionen, det vill säga haveriberedskapsregionen, samt skapar och startar den virtuella datorn i den primära regionen. **Observera** att virtuella DR-datorer finns kvar i avstängningen i ett frigjort tillstånd enligt ovan. Det här beteendet är avsiktligt eftersom Azure Site Recovery sparar information för den virtuella datorn. Den kan användas vid redundans för den primära till den sekundära regionen senare. Du debiteras inte för frigjorda virtuella datorer, så detta bör behållas som det är.
