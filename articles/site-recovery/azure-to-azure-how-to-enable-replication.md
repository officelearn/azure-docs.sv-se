---
title: Konfigurerar replikering för virtuella Azure-datorer i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar replikering för virtuella Azure-datorer från en Azure-region till en annan med Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/31/2018
ms.author: asgang
ms.openlocfilehash: 175b71488b429b3fd69d23db6cc9318d8db20568
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715980"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replikera virtuella Azure-datorer till en annan Azure-region



Den här artikeln beskriver hur du aktiverar replikering av virtuella Azure-datorer från en Azure-region till en annan.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har konfigurerat Site Recovery för det här scenariot som beskrivs i den [Azure Azure kursen](azure-to-azure-tutorial-enable-replication.md). Kontrollera att du har förberett kraven och skapat Recovery Services-valvet.



## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering. Den här proceduren förutsätter att den primära Azure-regionen är Östasien och den sekundära regionen är Sydostasien.

1. Klicka på valvet, **+ replikera**.
2. Observera följande fält:
    - **Källan**: referenspunkt på de virtuella datorerna i det här fallet **Azure**.
    - **Datakällplats**: Azure-region där du vill skydda dina virtuella datorer. För den här bilden är källplatsen östra Asien
    - **Distributionsmodell**: Azure distributionsmodell källdatorer.
    - **Resursgruppen**: resursgruppen som din virtuella källdatorer tillhör. Alla virtuella datorer under den valda resursgruppen visas för skydd i nästa steg.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. I **virtuella datorer > Välj virtuella datorer**och på varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka på **OK**.
    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. I **inställningar**, du kan också konfigurera platsinställningar för mål:

    - **Målplatsen**: den plats där källdata virtuella datorn kommer att replikeras. Beroende på din plats för valda datorer, tillhandahåller Site Recovery listan över målregioner lämplig. Vi rekommenderar att du behåller målplatsen samma som plats för Recovery Services-valvet.
    - **Målresursgruppen**: resursgruppen där alla de replikerade virtuella datorerna tillhör. Som standard skapar Azure Site Recovery en ny resursgrupp i området mål med namn med suffixet ”asr”. Resursgruppen som skapats av Azure Site Recovery redan finns återanvänds. Du kan också välja att anpassa den som visas i följande avsnitt. Målresursgruppen kan vara någon Azure-region utom den region som är värd för virtuella källdatorer.
    - **Rikta virtuellt nätverk**: som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen med namn med suffixet ”asr”. Detta är mappad till nätverket källa och används för alla framtida skydd. [Lär dig mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
    - **Rikta Storage-konton (om datakällan inte använder VM-hanterade diskar)**: som standard Site Recovery skapar ett nytt lagringskonto för frihandsbilden lagringskonfigurationen källa VM-mål. Om lagringskontot redan återanvänds.
    - **Replik hanterade diskar (om ditt Virtuella källdatorn använder hanterade diskar)**: Site Recovery skapar den nya repliken hanterade diskar i målregionen för spegling av källa VM hanterade diskar med samma lagringstyp (Standard eller premium) som den Virtuella källdatorns hanterad disk.
    - **Cachelagra lagringskonton**: Site Recovery behöver extra lagring som kallas konto för cachelagring i området för källa. Alla ändringar som sker på virtuella källdatorer spåras och skickas till cachen storage-konto innan du replikerar de till målplatsen.
    - **Tillgänglighetsuppsättningen**: som standard skapas en ny tillgänglighetsuppsättning i området mål med namn med suffixet ”asr” i Azure Site Recovery. Tillgänglighetsuppsättningen redan skapat av Azure Site Recovery finns återanvänds.
    - **Replikeringsprincip**: den definierar inställningar för återställning punkt kvarhållning historik och app programkonsekvent ögonblicksbild frekvens. Som standard skapar en ny replikeringsprincip med standardinställningarna för ”24 timmars för kvarhållningstid för återställningspunkten och” 60 minuters för app programkonsekvent ögonblicksbild frekvens i Azure Site Recovery.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Anpassa target-resurser

Du kan ändra standardinställningarna för mål som används av Site Recovery.

1. Klicka på **anpassa:** att ändra standardinställningar:
    - I **målresursgruppen**, Välj resursgruppen från listan över alla resursgrupper i målplatsen för prenumerationen.
    - I **virtuella målnätverket**, väljer nätverket från en lista över alla virtuella nätverk på målplatsen.
    - I **tillgänglighetsuppsättning**, du kan lägga till tillgänglighetsuppsättning inställningar till den virtuella datorn, om de är en del av en tillgänglighetsuppsättning i området för källa.
    - I **mål lagringskonton**, väljer du det konto som du vill använda.

        ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Klicka på **skapa målresurs** > **Aktivera replikering**.
3. När de virtuella datorerna har aktiverats för replikering, du kan kontrollera status för VM-hälsa under **replikerade objekt**

>[!NOTE]
>Under den första replikeringen kan status ta lite tid att uppdatera utan pågår. Klicka på den **uppdatera** för att få den senaste statusen.
>

# <a name="next-steps"></a>Nästa steg

[Lär dig mer](site-recovery-test-failover-to-azure.md) om att köra ett redundanstest.
