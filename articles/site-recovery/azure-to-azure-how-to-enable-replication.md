---
title: Konfigurera replikering för virtuella Azure-datorer i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar replikering för virtuella Azure-datorer från en Azure-region till en annan med Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 86bd41d518006b0601a5c9d18e5429f76d5a4fc5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926616"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikera virtuella Azure-datorer till en annan Azure-region


Den här artikeln beskriver hur du aktiverar replikering av virtuella Azure-datorer från en Azure-region till en annan.

## <a name="before-you-start"></a>Innan du börjar

Den här artikeln förutsätter att du har förberett för distributionen av Site Recovery, enligt beskrivningen i den [Azure till Azure disaster recovery självstudien](azure-to-azure-tutorial-enable-replication.md).

Krav bör finnas på plats och du ska ha skapat ett Recovery Services-valv.


## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering. Den här proceduren förutsätter att den primära Azure-regionen är Östasien och den sekundära regionen Sydostasien.

1. I valvet, klickar du på **+ replikera**.
2. Observera följande fält:
   - **Källa**: Referenspunkt för de virtuella datorerna i det här fallet **Azure**.
   - **Källplats**: Azure-regionen där du vill skydda dina virtuella datorer. För den här bilden ser är källplatsen 'Östasien'
   - **Distributionsmodellen**: Azure-distributionsmodellen källdatorer.
   - **Käll-prenumeration**: Den prenumeration som din virtuella källdatorer tillhör. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
   - **Resursgrupp**: Den resursgrupp som din virtuella källdatorer tillhör. Alla virtuella datorer under den valda resursgruppen listas för skydd i nästa steg.

     ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. I **virtuella datorer > Välj virtuella datorer**, klicka på och välj varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.
    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. I **inställningar**, du kan också konfigurera Målinställningar för platsen:

   - **Målplats**: Den plats där dina källdata för virtuell dator kommer att replikeras. Beroende på din plats för valda datorer, får Site Recovery du listan med lämplig målregioner. Vi rekommenderar att du behåller målplatsen samma som platsen för Recovery Services-valvet.
   - **Målprenumeration**: Den målprenumeration som används för haveriberedskap. Som standard är målprenumerationen samma som källprenumerationen.
   - **Målresursgrupp**: Den resursgrupp där alla de replikerade virtuella datorerna tillhör.
       - Som standard skapar Site Recovery en ny resursgrupp i målregionen med suffixet ”asr” i namnet.
       - Om resursgruppen som skapades av Site Recovery redan finns, återanvänds.
       - Du kan anpassa gruppinställningar för resursen.
       - Platsen för målresursgruppen kan vara valfri Azure-region, utom den region som är värd för virtuella källdatorn.
   - **Virtuellt målnätverk**: Som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen med suffixet ”asr” i namnet. Detta är mappad till nätverket källa och används för alla framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
   - **Mållagringskonton (Virtuella källdatorn inte använder hanterade diskar)**: Som standard skapar Site Recovery ett nytt mållagringskonto frihandsbilden lagringskonfigurationen för käll-VM. Om lagringskontot finns redan, återanvänds.
   - **Replik-hanterade diskar (Virtuella måldatorn använder hanterade diskar)**: Site Recovery skapar nya-hanterade diskar i målregionen som speglar den Virtuella källdatorns hanterade diskar med samma lagringstyp (Standard eller premium) som den Virtuella källdatorn hanterade disk.
   - **Cachelagringskonton**: Site Recovery behöver extra lagringskonto med namnet cachelagring i källregionen. Alla ändringar som sker på virtuella källdatorn är spåras och skickas till cachelagringskontot innan du replikerar de till målplatsen.
   - **Tillgänglighetsuppsättningar för mål**: Som standard skapar Site Recovery en ny tillgänglighetsuppsättning i målregionen med suffixet ”asr” i namnet för virtuella datorer som ingår i en tillgänglighetsuppsättning i källregionen. Om tillgänglighetsuppsättningen redan skapat av Site Recovery finns återanvänds.
   - **Tillgänglighetszoner för mål**: Som standard tilldelar Site Recovery samma zonnummer som källregionen i målregionen om målregionen har stöd för tillgänglighetszoner.

     Om målregionen inte har stöd för tillgänglighetszoner konfigureras de virtuella måldatorerna som enskilda instanser som standard. Om det behövs kan du konfigurera sådana virtuella datorer att bli en del av tillgänglighetsuppsättningarna i målregionen genom att klicka på ”Anpassa”.

     >[!NOTE]
     >Du kan inte ändra tillgänglighetstypen enskild instans, tillgänglighetsuppsättningen eller tillgänglighetszonen efter att du har aktiverat replikering. Du måste inaktivera och aktivera replikering för att ändra tillgänglighetstypen.
     >
    
   - **Replikeringsprincip**: Den definierar inställningarna för recovery point kvarhållning och frekvensen för programkonsekventa ögonblicksbilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningarna för ”24 timmars för kvarhållning av återställningspunkt och” 60 minuters för frekvens för appkonsekvent ögonblicksbild.

     ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Aktivera replikering för tillagda diskar

Om du lägger till diskar till en Azure-dator där replikering är aktiverat inträffar följande:
-   Tillståndet för replikeringen för den virtuella datorn visas en varning och en anteckning informerar om att en eller fler diskar är tillgängliga för skydd.
-   Om du aktiverar skydd för de tillagda diskarna försvinner varningen när den inledande replikeringen på disken.
-   Om du väljer att inte aktivera replikering av disken, kan du välja för att ignorera varningen.

    
    ![Ny disk som har lagts till](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Om du vill aktivera replikering för en disk som har lagts till, gör du följande:

1.  I valvet > **replikerade objekt**, klickar du på den virtuella datorn som du har lagt till disken.
2.  Klicka på **diskar**, och välj sedan datadisken som du vill aktivera replikering (dessa diskar har en **oskyddade** status).
3.  I **diskinformation**, klickar du på **Aktivera replikering**.

    ![Aktivera replikering för tillagda disken](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

När du aktivera replikering jobbet körs, och de inledande replikeringen har slutförts, tas health Replikeringsvarning för disk-problemet bort.


  
## <a name="customize-target-resources"></a>Anpassa målresurser

Du kan ändra standardinställningarna för målet som används av Site Recovery.

1. Klicka på **anpassa:** bredvid mål-prenumeration att ändra målprenumerationen standard. Välj prenumerationen i listan över alla prenumerationer som är tillgängliga i samma Azure Active Directory (AAD)-klient.

2. Klicka på **anpassa:** att ändra standardinställningarna:
    - I **målresursgrupp**, Välj resursgruppen i listan över alla resursgrupper i målplatsen för prenumerationen.
    - I **virtuellt Målnätverk**, väljer nätverket från en lista över det virtuella nätverket på målplatsen.
    - I **tillgänglighetsuppsättning**, du kan lägga till inställningar för tillgänglighetsuppsättningen till den virtuella datorn, om de är en del av en tillgänglighetsuppsättning i källregionen.
    - I **Mållagring konton**, väljer du det konto som du vill använda.

        ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Klicka på **anpassa:** att ändra replikeringsinställningar för.
4. I **konsekvens**, Välj de virtuella datorer som du vill replikera tillsammans.
    - Alla maskiner i en replikeringsgrupp har delade kraschkonsekventa och appkonsekventa återställningspunkter när de redundansväxlas.
    - Aktivering av konsekvens för flera datorer kan påverka prestandan (eftersom det är Processorintensiva). Det bör bara aktiveras om datorer kör samma arbetsbelastning och konsekvens mellan flera datorer.
    - Till exempel om ett program har 2 virtuella datorer med SQL Server och två webbservrar, sedan du bör lägga till endast SQL Server-datorer i en replikeringsgrupp.
    - Du kan välja att ha högst 16 virtuella datorer i en replikeringsgrupp.
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004.
    - Se till att det inte finns någon brandvägg blockerar den interna kommunikationen mellan de virtuella datorerna på port 20004.
    - Kontrollera att utgående trafik på port 20004 manuellt öppnar enligt vägledning för den specifika Linux-versionen om du vill att de virtuella Linux-datorer ska ingå i en replikeringsgrupp.
![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Klicka på **skapa målresursen** > **Aktivera replikering**.
6. När de virtuella datorerna har aktiverats för replikering, du kan kontrollera status för VM-hälsa under **replikerade objekt**

>[!NOTE]
>Under den inledande replikeringen kan status ta lite tid att uppdatera utan pågår. Klicka på den **uppdatera** knapp för att hämta senaste status.
>

# <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om hur du kör ett redundanstest.
