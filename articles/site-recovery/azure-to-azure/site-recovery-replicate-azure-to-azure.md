---
title: "Replikera virtuella Azure-datorer till en sekundär region med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du replikerar Azure virtuella datorer som körs i en Azure-region till en annan region med hjälp av Azure Site Recovery-tjänsten."
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
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replikera virtuella Azure-datorer till en annan Azure-region


Den här artikeln beskriver hur du replikerar virtuella Azure-datorer (VM) i en Azure-region, till en sekundär Azure region med hjälp av Azure Site Recovery-tjänsten.

>[!NOTE]
>
> Azure VM-replikeringen med Site Recovery är för närvarande under förhandsgranskning.

## <a name="prerequisites"></a>Krav

* Du bör ha ett Recovery Services-valv på plats. Vi rekommenderar att du skapar valvet i mål-region som du vill att dina virtuella datorer för replikering.
* Om du använder regler för Nätverkssäkerhetsgrupp grupper (NSG) eller en brandväggen proxy styra åtkomsten till utgående Internetanslutning på Azure Virtual Machines, kontrollerar du att du URL: erna eller IP-adresser. [Läs mer](./site-recovery-azure-to-azure-networking-guidance.md).
* Om du har en ExpressRoute eller en VPN-anslutning mellan lokala och källplats i Azure, [Lär dig hur du ställer in](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Din Azure användarkontot måste [specifika behörigheter](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines), för att aktivera replikering av en Azure VM.
Din Azure-prenumeration måste vara aktiverat för att skapa virtuella datorer i den målplats som du vill använda som en disaster recovery-region. Kontakta supporten om du vill aktivera kvoten som krävs.

## <a name="enable-replication"></a>Aktivera replikering

I den här proceduren används Östasien som källplats och Sydostasien som mål.

1. Klicka på **+ replikera** i valvet för att aktivera replikering för virtuella datorer.
2. Kontrollera att **källa:** är inställd på **Azure**.
3. Ange **datakällplats** till Östasien.
4. I **distributionsmodellen**väljer **klassiska** eller **Resource Manager**.
5. I **resursgruppen**, Välj den grupp som din virtuella källdatorer tillhör. Alla virtuella datorer under den valda resursgruppen finns.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. I **virtuella datorer > Välj virtuella datorer**och klicka på Välj varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. Under **inställningar** > **målplats**, ange att där källan VM-data replikeras. Site Recovery tillhandahåller en lista över lämpliga målregioner, beroende på region på de valda virtuella datorerna.
8. Site Recovery anger standard Målinställningar. Dessa kan ändras efter behov.

    - **Målresursgruppen**. Som standard skapar en ny resursgrupp i målregionen med suffixet ”asr” i Site Recovery. Om det redan finns skapas resursgruppen återanvänds.
    - **Virtuella målnätverket**. Som standard skapar ett nytt virtuellt nätverk i målregionen med suffixet ”asr” i Site Recovery. Det här nätverket är mappad till nätverket källa. [Lär dig mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
    - **Rikta lagringskonton**. Som standard skapar en ny mål-lagringskontot som matchar konfigurationen av källa VM Site Recovery. Om det finns redan ett konto som skapades, återanvänds.
    - **Cachelagra lagringskonton**. Azure Site Recovery skapar ett extra cache lagringskonto käll-region. Alla ändringar på virtuella källdatorer spåras och skickas till cachen storage-konto innan replikeringen till målplatsen.
    - **Tillgänglighetsuppsättningen**. Som standard skapar en ny tillgänglighetsuppsättning i målregionen med ett ”asr” suffix Site Recovery. Om det redan finns skapas uppsättningen återanvänds.
    - **Replikeringsprincip**. Site Recovery definierar inställningar för återställningshistorik punkt kvarhållning och frekvensen för programkonsekventa ögonblicksbilder. Som standard skapar en ny replikeringsprincip med standardinställningar 24 timmar för kvarhållningstid för återställningspunkten och 60 minuter för frekvensen för programkonsekventa ögonblicksbilder i Site Recovery.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. Klicka på **Aktivera replikering** att börja skydda virtuella datorer.

## <a name="customize-target-resources"></a>Anpassa target-resurser

1. Ändra standardinställningarna mål:

    - **Målresursgruppen**. Välj en resursgrupp i listan över alla resursgrupper i målplatsen i prenumerationen.
    - **Virtuella målnätverket**. Välj i listan över alla virtuella nätverk på målplatsen.
    - **Tillgänglighetsuppsättningen** anger tillgänglighetsinställningarna kan endast läggas till virtuella datorer finns i en mängd i området för källa.
    - **Rikta lagringskonton**: lägga till ett konto som är tillgänglig.

        ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Klicka på **skapa målresurs** > **Aktivera replikering**. Under den första replikeringen kan VM-statusen ta lite tid att uppdatera. Klicka på **uppdatera** att hämta senaste status.

    ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. När virtuella datorer är skyddade, kontrollera VM hälsa i **replikerade objekt**.



## <a name="next-steps"></a>Nästa steg
[Läs](../azure-to-azure-tutorial-dr-drill.md) hur du kör ett redundanstest.

