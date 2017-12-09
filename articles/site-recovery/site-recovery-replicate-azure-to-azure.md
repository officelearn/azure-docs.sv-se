---
title: Duplicera program (Azure Azure) | Microsoft Docs
description: "Den här artikeln beskriver hur du ställer in replikering av virtuella datorer som körs i en Azure-region till en annan region i Azure."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: asgang
ms.openlocfilehash: dc7dff33aa2c3e844c6a91024fcfc98148416f7e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replikera virtuella Azure-datorer till en annan Azure-region



>[!NOTE]
>
> Site Recovery replikering för virtuella Azure-datorer är för närvarande under förhandsgranskning.

Den här artikeln beskriver hur du ställer in replikering av virtuella datorer som körs i en Azure-region till en annan Azure-region.

## <a name="prerequisites"></a>Krav

* Artikeln förutsätter att du redan vet om Site Recovery och Recovery Services-valvet. Du måste ha en 'återställningstjänstvalvet' som skapats i förväg.

    >[!NOTE]
    >
    > Det rekommenderas att du skapar den 'återställningstjänstvalvet' på den plats där du vill att dina virtuella datorer för replikering. Till exempel om din målplats 'centrala USA, skapa valv i 'Centrala USA'.

* Om du använder Nätverkssäkerhetsgrupp grupper (NSG) regler eller brandväggen proxy för att styra åtkomsten till utgående Internetanslutning på Azure Virtual Machines, se till att du godkända obligatorisk URL: er eller IP-adresser. Referera till [nätverk riktlinjerna](./site-recovery-azure-to-azure-networking-guidance.md) för mer information.

* Om du har en ExpressRoute eller en VPN-anslutning mellan lokala och källplats i Azure följer [platsöverväganden för Azure till lokala ExpressRoute / VPN-konfiguration](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration) dokumentet.

* Ditt Azure-konto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en virtuell Azure-dator.

* Din Azure-prenumeration måste vara aktiverat för att skapa virtuella datorer i den målplats som du vill använda som DR region. Du kan kontakta support om du vill aktivera kvoten som krävs.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Aktivera replikering från Azure Site Recovery-valvet
För den här bilden kommer vi replikera virtuella datorer som körs i östra Asien Azure installationsplatsen på Syd Östasien. Stegen är följande:

 Klicka på **+ replikera** i valvet för att aktivera replikering för virtuella datorer.

1. **Källa:** den refererar till punkten ursprung för datorer, i det här fallet **Azure**.

2. **Datakällplats:** är det Azure-region där du vill skydda dina virtuella datorer. För den här bilden är källplatsen östra Asien

3. **Distributionsmodell:** refererar den till Azure distributionsmodell källdatorer. Du kan välja antingen klassiska eller Resource Manager och datorer som tillhör en viss modell visas för skydd i nästa steg.

      >[!NOTE]
      >
      > Du kan endast replikera en klassisk virtuell dator och återställa den som en klassisk virtuell dator. Du kan inte återställa den som en virtuell dator i Resource Manager.

4. **Resursgrupp:** det är den resursgrupp som din virtuella källdatorer tillhör. Alla virtuella datorer under den valda resursgruppen visas för skydd i nästa steg.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

I **virtuella datorer > Välj virtuella datorer**och på varje dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på OK.
    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


Under inställningar kan du konfigurera egenskaper för mål

1. **Målplatsen:** detta är den plats där källdata virtuella datorn kommer att replikeras. Beroende på din plats för valda datorer, tillhandahåller Site Recovery listan över målregioner lämplig.

    > [!TIP]
    > Det rekommenderas att hålla målplats samma från och med din recovery services-valvet.

2. **Målresursgruppen:** är det resursgruppen där alla de replikerade virtuella datorerna ska tillhöra. Standard skapar Azure Site Recovery en ny resursgrupp i området mål med namn med suffixet ”asr”. Resursgruppen som skapats av Azure Site Recovery redan finns den av återanvändas. Du kan också välja att anpassa den som visas i följande avsnitt.    
3. **Mål för virtuella nätverk:** som standard, Azure Site Recovery skapar ett nytt virtuellt nätverk i området mål med namn med suffixet ”asr”. Detta kommer att mappas till nätverket källa och kommer att användas för alla framtida skydd.

    > [!NOTE]
    > [Nätverk klientkontrollen](site-recovery-network-mapping-azure-to-azure.md) vill veta mer om nätverksmappning.

4. **Rikta Storage-konton:** som standard skapar Azure Site Recovery nya mål-lagringskontot frihandsbilden lagringskonfigurationen källa VM. Om lagringskonto som skapats av Azure Site Recovery redan finns, den av återanvändas.

5. **Cachelagra Storage-konton:** Azure Site Recovery behöver extra lagring som kallas konto för cachelagring i området för källa. Alla ändringar som sker på virtuella källdatorer spåras och skickas till cachen storage-konto innan du replikerar de till målplatsen.

6. **Tillgänglighetsuppsättningen:** Azure Site Recovery kommer som standard skapar en ny tillgänglighetsuppsättning i området mål med namn med suffixet ”asr”. Tillgänglighetsuppsättningen redan skapat av Azure Site Recovery finns den av återanvändas.

7.  **Replikeringsprincip:** definierar inställningar för återställning punkt kvarhållning historik och app programkonsekvent ögonblicksbild frekvens. Standard skapas Azure Site Recovery en ny replikeringsprincip med standardinställningarna för ”24 timmars för kvarhållningstid för återställningspunkten och” 60 minuters för app programkonsekvent ögonblicksbild frekvens.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Anpassa target-resurser

Om du vill ändra standardinställningarna som används av Azure Site Recovery kan du ändra inställningarna utifrån dina behov.

1. **Anpassa:** Klicka om du vill ändra standardinställningarna som används av Azure Site Recovery.

2. **Målresursgruppen:** du kan välja resursgruppen från listan över alla resursgrupper finns i målplatsen i prenumerationen.

3. **Mål för virtuella nätverk:** du hittar listan över det virtuella nätverket på målplatsen.

4. **Tillgänglighetsuppsättningen:** anger tillgänglighetsinställningarna kan endast läggas till de virtuella datorerna som är en del av tillgänglighet i källan region.

5. **Mål Storage-konton:**

![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/customize.PNG) klickar du på **skapa målresurs** och aktivera replikering


När virtuella datorer är skyddade du kan kontrollera status för virtuella datorer hälsa under **replikerade objekt**

>[!NOTE]
>Under tiden för inledande replikering kan det finnas en möjlighet status tar tid att uppdatera och du kan se inte förloppet under en viss tid. Du kan klicka på uppdateringsknappen överst på bladet för att hämta senaste status.
>

![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>Nästa steg
- [Lär dig mer](site-recovery-test-failover-to-azure.md) om att köra ett redundanstest.
- [Lär dig mer](site-recovery-failover.md) om olika typer av redundansväxlingar och hur du kör dem.
- Lär dig mer om [med återställningsplaner](site-recovery-create-recovery-plans.md) att minska Återställningstidsmål.
- Lär dig mer om [skydda virtuella datorer i Azure](site-recovery-how-to-reprotect.md) efter växling vid fel.
