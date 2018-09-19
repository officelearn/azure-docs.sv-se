---
title: Konfigurera replikering för virtuella Azure-datorer i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar replikering för virtuella Azure-datorer från en Azure-region till en annan med Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: asgang
ms.openlocfilehash: 7002e8a63ca0223a38ba099b17955a86034fa057
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295469"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replikera virtuella Azure-datorer till en annan Azure-region



Den här artikeln beskriver hur du aktiverar replikering av virtuella Azure-datorer från en Azure-region till en annan.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ställt in Site Recovery för det här scenariot som beskrivs i den [Azure till Azure-självstudie](azure-to-azure-tutorial-enable-replication.md). Se till att du har förberett kraven och skapat Recovery Services-valvet.



## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering. Den här proceduren förutsätter att den primära Azure-regionen är Östasien och den sekundära regionen Sydostasien.

1. I valvet, klickar du på **+ replikera**.
2. Observera följande fält:
    - **Källan**: det datum då ursprung för de virtuella datorerna i det här fallet **Azure**.
    - **Källplats**: Azure-regionen där du vill skydda dina virtuella datorer. För den här bilden ser är källplatsen 'Östasien'
    - **Distributionsmodell**: Azure-distributionsmodellen källdatorer.
    - **Käll-prenumeration**: den prenumeration som din virtuella källdatorer tillhör. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
    - **Resursgrupp**: den resursgrupp som din virtuella källdatorer tillhör. Alla virtuella datorer under den valda resursgruppen listas för skydd i nästa steg.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. I **virtuella datorer > Välj virtuella datorer**, klicka på och välj varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.
    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. I **inställningar**, du kan också konfigurera Målinställningar för platsen:

    - **Målplats**: den plats där dina källdata för virtuell dator kommer att replikeras. Beroende på din plats för valda datorer, får Site Recovery du listan med lämplig målregioner. Vi rekommenderar att du behåller målplatsen samma som platsen för Recovery Services-valvet.
    - **Målprenumeration**: Målprenumerationen som används för haveriberedskap. Som standard är målprenumerationen samma som källprenumerationen.
    - **Målresursgrupp**: resursgruppen där alla de replikerade virtuella datorerna tillhör. Som standard skapar Azure Site Recovery en ny resursgrupp i målregionen med namn som har suffixet ”asr”. Om resursgruppen redan skapat av Azure Site Recovery finns, återanvänds. Du kan också välja att anpassa den som visas i avsnittet nedan. Platsen för målresursgruppen kan vara alla Azure-regioner utom den region som är värd för virtuella källdatorer.
    - **Målnätverket har virtuella**: som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen med namn som har suffixet ”asr”. Detta är mappad till nätverket källa och används för alla framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
    - **Mållagringskonton (om källan Virtuella måldatorn inte använder hanterade diskar)**: som standard skapar Site Recovery ett nytt mållagringskonto frihandsbilden lagringskonfigurationen för käll-VM. Om lagringskontot finns redan, återanvänds.
    - **Hanterade replikeringsdiskar (om den Virtuella måldatorn använder hanterade diskar)**: Site Recovery skapar nya hanterade replikeringsdiskar i målregionen som speglar den Virtuella källdatorns hanterade diskar med samma lagringstyp (Standard eller premium) som den Virtuella källdatorn hanterade disk.
    - **Cachelagringskonton**: Site Recovery behöver extra lagringskonto med namnet cachelagring i källregionen. Alla ändringar som sker på virtuella källdatorn är spåras och skickas till cachelagringskontot innan du replikerar de till målplatsen.
    - **Tillgänglighetsuppsättning**: som standard skapar Azure Site Recovery en ny tillgänglighetsuppsättning i målregionen med namn som har suffixet ”asr”. Tillgänglighetsuppsättning som skapats av Azure Site Recovery redan finns, återanvänds.
    - **Replikeringsprincip**: den definierar inställningarna för recovery point kvarhållning och frekvensen för programkonsekventa ögonblicksbilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningarna för ”24 timmars för kvarhållning av återställningspunkt och” 60 minuters för frekvens för appkonsekvent ögonblicksbild.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Anpassa målresurser

Du kan ändra standardinställningarna för målet som används av Site Recovery.

1. Klicka på **anpassa:** bredvid mål-prenumeration att ändra målprenumerationen standard. Välj prenumerationen i listan över alla prenumerationer som är tillgängliga i samma Azure Active Directory (AAD)-klient.

2. Klicka på **anpassa:** att ändra standardinställningarna:
    - I **målresursgrupp**, Välj resursgruppen i listan över alla resursgrupper i målplatsen för prenumerationen.
    - I **virtuellt Målnätverk**, väljer nätverket från en lista över det virtuella nätverket på målplatsen.
    - I **tillgänglighetsuppsättning**, du kan lägga till inställningar för tillgänglighetsuppsättningen till den virtuella datorn, om de är en del av en tillgänglighetsuppsättning i källregionen.
    - I **Mållagring konton**, väljer du det konto som du vill använda.

        ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Klicka på **skapa målresursen** > **Aktivera replikering**.
3. När de virtuella datorerna har aktiverats för replikering, du kan kontrollera status för VM-hälsa under **replikerade objekt**

>[!NOTE]
>Under den inledande replikeringen kan status ta lite tid att uppdatera utan pågår. Klicka på den **uppdatera** knapp för att hämta senaste status.
>

# <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om hur du kör ett redundanstest.
