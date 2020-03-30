---
title: Konfigurera replikering för virtuella Azure-datorer i Azure Site Recovery
description: Lär dig hur du konfigurerar replikering till en annan region för virtuella Azure-datorer med hjälp av Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973697"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikera virtuella Azure-datorer till en annan Azure-region


I den här artikeln beskrivs hur du aktiverar replikering av virtuella Azure-datorer, från en Azure-region till en annan.

## <a name="before-you-start"></a>Innan du börjar

Den här artikeln förutsätter att du har förberett för distribution av platsåterställning, enligt beskrivningen i [azure till Azure-haveriberedskapsdialkursen](azure-to-azure-tutorial-enable-replication.md).

Förutsättningar bör vara på plats och du bör ha skapat ett Recovery Services-valv.


## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering. Den här proceduren förutsätter att den primära Azure-regionen är Östasien och den sekundära regionen är Sydostasien.

1. Klicka på **+Replikera**i valvet .
2. Observera följande fält:
   - **Källa**: Utgångspunkten för de virtuella datorerna, som i det här fallet är **Azure**.
   - **Källplats**: Azure-regionen där du vill skydda dina virtuella datorer. För den här bilden är källplatsen "Östasien"
   - **Distributionsmodell**: Azure-distributionsmodell för källdatorerna.
   - **Källprenumeration**: Prenumerationen som dina virtuella käll-datorer tillhör. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
   - **Resursgrupp**: Den resursgrupp som käll virtuella datorer tillhör. Alla virtuella datorer under den valda resursgruppen visas för skydd i nästa steg.

     ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Klicka på och välj varje virtuell dator som du vill replikera i virtuella datorer > **Välj virtuella datorer.** Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.
    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. I **Inställningar**kan du konfigurera inställningar för målplats:

   - **Målplats:** Den plats där källdata för virtuella datorer replikeras. Beroende på var du väljer maskiner ger site recovery dig en lista över lämpliga målområden. Vi rekommenderar att du behåller målplatsen på samma sätt som platsen för Återställningstjänster.
   - **Målprenumeration**: Målprenumerationen som används för haveriberedskap. Som standard är målprenumerationen samma som källprenumerationen.
   - **Målgrupp:** Den resursgrupp som alla replikerade virtuella datorer tillhör.
       - Som standard skapar Site Recovery en ny resursgrupp i målregionen med ett suffix "asr" i namnet.
       - Om resursgruppen som skapats av Site Recovery redan finns återanvänds den.
       - Du kan anpassa resursgruppsinställningarna.
       - Platsen för målresursgruppen kan vara vilken Azure-region som helst, utom den region där käll-virtuella datorer finns.
   - **Mål virtuellt nätverk**: Som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen med ett suffix "asr" i namnet. Detta mappas till källnätverket och används för framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
   - **Mållagringskonton (käll-VM använder inte hanterade diskar):** Som standard skapar Site Recovery ett nytt mållagringskonto som härmar lagringskonfigurationen för källdatorn. Om lagringskonto redan finns återanvänds det.
   - **Replikhanterade diskar (källa VM använder hanterade diskar):** Site Recovery skapar nya replikhanterade diskar i målområdet för att spegla källdatorns hanterade diskar med samma lagringstyp (standard eller premium) som källdatorns hanterade disk.
   - **Cachelagringskonton**: Site Recovery behöver extra lagringskonto som kallas cachelagring i källregionen. Alla ändringar som sker på käll-virtuella datorer spåras och skickas till cachelagringskontot innan de replikeras till målplatsen. Det här lagringskontot ska vara Standard.
   - **Måltillgänglighetsuppsättningar**: Som standard skapar Site Recovery en ny tillgänglighetsuppsättning i målregionen med suffixet "asr" i namnet, för virtuella datorer som ingår i en tillgänglighet som anges i källregionen. Om den tillgänglighetsuppsättning som skapas av Site Recovery redan finns, återanvänds den.
   - **Tillgänglighetszoner för mål**: som standard tilldelar Site Recovery samma zonnummer som källregionen i målregionen om målregionen har stöd för tillgänglighetszoner.

     Om målregionen inte har stöd för tillgänglighetszoner konfigureras de virtuella måldatorerna som enskilda instanser som standard. Om det behövs kan du konfigurera sådana virtuella datorer att bli en del av tillgänglighetsuppsättningarna i målregionen genom att klicka på ”Anpassa”.

     >[!NOTE]
     >Du kan inte ändra tillgänglighetstypen enskild instans, tillgänglighetsuppsättningen eller tillgänglighetszonen efter att du har aktiverat replikering. Du måste inaktivera och aktivera replikering för att ändra tillgänglighetstypen.
     >

   - **Replikeringsprincip**: Den definierar inställningarna för återställningspunktkvarhållningshistorik och app konsekvent ögonblicksbildfrekvens. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningar på "24 timmar" för återställningspunktkvarhållning och "4 timmar" för app konsekvent ögonblicksbildfrekvens.

     ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Aktivera replikering för tillagda diskar

Om du lägger till diskar i en Virtuell Azure-dator för vilken replikering är aktiverad, inträffar följande:
-   Replikeringshälsa för den virtuella datorn visar en varning och en anteckning informerar om att en eller flera diskar är tillgängliga för skydd.
-   Om du aktiverar skydd för de tillagda diskarna försvinner varningen efter den första replikeringen av disken.
-   Om du väljer att inte aktivera replikering för disken kan du välja att stänga av varningen.


    ![Ny disk tillagd](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Så här aktiverar du replikering för en tillagd disk:

1.  Klicka på den virtuella dator som du lade till disken i valvet > **replikerade objekt.**
2.  Klicka på **Diskar**och välj sedan den datadisk som du vill aktivera replikering för (dessa diskar har **inte skyddad** status).
3.  Klicka på **Aktivera replikering** **i Diskinformation**.

    ![Aktivera replikering för tillagd disk](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

När aktivera replikeringsjobbet har körts och den första replikeringen är klar tas hälsovarningen för replikering för diskproblemet bort.



## <a name="customize-target-resources"></a>Anpassa målresurser

Du kan ändra standardmålinställningarna som används av Site Recovery.

1. Klicka på **Anpassa:** Bredvid Målprenumeration om du vill ändra standardmålprenumerationen. Välj prenumerationen i listan över alla prenumerationer som är tillgängliga i samma Azure Active Directory -klientorganisation (AAD).

2. Klicka på **Anpassa:** om du vill ändra standardinställningarna:
    - I **gruppen Målresurs**väljer du resursgruppen i listan över alla resursgrupper på målplatsen för prenumerationen.
    - I **virtuellt målnätverk**väljer du nätverket från en lista över alla virtuella nätverk på målplatsen.
    - I **Tillgänglighetsuppsättning**kan du lägga till inställningar för tillgänglighetsuppsättning till den virtuella datorn, om de ingår i en tillgänglighetsuppsättning i källregionen.
    - Välj det konto som du vill använda i **Mållagringskonton.**

        ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Klicka på **Anpassa:** om du vill ändra replikeringsinställningarna.
4. I **konsekvensen för flera virtuella datorer**väljer du de virtuella datorer som du vill replikera tillsammans.
    - Alla maskiner i en replikeringsgrupp har delade kraschkonsekventa och appkonsekventa återställningspunkter när de redundansväxlas.
    - Aktivera konsekvens för flera virtuella datorer kan påverka arbetsbelastningens prestanda (eftersom den är processorintensiv). Det bör endast aktiveras om datorer kör samma arbetsbelastning och du behöver konsekvens mellan flera datorer.
    - Om ett program till exempel har 2 virtuella SQL Server-datorer och två webbservrar bör du bara lägga till virtuella SQL Server-datorer i en replikeringsgrupp.
    - Du kan välja att ha högst 16 virtuella datorer i en replikeringsgrupp.
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004.
    - Se till att det inte finns någon brandväggsinstallation som blockerar den interna kommunikationen mellan de virtuella datorerna via port 20004.
    - Om du vill att virtuella Linux-datorer ska ingå i en replikeringsgrupp kontrollerar du att den utgående trafiken på port 20004 öppnas manuellt enligt vägledning för den specifika Linux-versionen.
![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Klicka på **Skapa målresurs** > **Aktivera replikering**.
6. När de virtuella datorerna har aktiverats för replikering kan du kontrollera statusen för VM-hälsotillståndet under **Replikerade objekt**

>[!NOTE]
>Under den första replikeringen kan statusen ta lite tid att uppdatera, utan förlopp. Klicka på knappen **Uppdatera** för att få den senaste statusen.
>

## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om att köra en testundanställning.
