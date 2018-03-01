---
title: "Skapa resurser som ska användas med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du förbereder Azure för replikering av lokala datorer med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d1aadd6b44d64f0bdb35ea02d628bedfc366ad3c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Förbereda Azure-resurser för replikering av lokala datorer

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

Den här självstudien visar hur du förbereder Azure-komponenter när du vill replikera lokala virtuella datorer (Hyper-V eller VMware) eller fysiska Windows-/Linux-servrar till Azure. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Kontrollera att ditt konto har replikeringsbehörighet
> * Skapa ett Azure-lagringskonto.
> * Konfigurera ett Azure-nätverk. När de virtuella Azure-datorerna skapats efter redundansen, är de anslutna till det här Azure-nätverket.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="verify-account-permissions"></a>Kontrollera kontobehörigheten

Om du nyligen skapade ditt kostnadsfria Azure-konto är du administratör för prenumerationen. Om du inte är administratör för prenumerationen kan du tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha:

- Behörighet att skapa en virtuell dator i den valda resursgruppen
- Behörighet att skapa en virtuell dator i det valda virtuella nätverket
- Behörighet att skriva till det valda lagringskontot

Den inbyggda rollen ”Virtuell datordeltagare” har dessa behörigheter. Du behöver också behörighet för att hantera åtgärder i Azure Site Recovery. Rollen Site Recovery-bidragsgivare har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i ett Recovery Services-valv.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Avbildningar av replikerade datorer lagras i Azure-lagringen. Virtuella Azure-datorer skapas från minnet vid redundansväxling från lokalt till Azure.

1. På menyn [Azure Portal](https://portal.azure.com) klickar du på **Nytt** -> **Lagring** -> **Lagringskonto**.
2. I **Skapa lagringskonto** anger du ett namn för kontot. I dessa självstudier använder vi namnet **contosovmsacct1910171607**. Namnet måste vara unikt i Azure och mellan 3 och 24 tecken långt. Det får endast bestå av siffror och gemener.
3. Använd **Resource Manager**-distributionsmodellen.
4. Välj **Generell användning** > **Standard**. Välj inte blobblagring.
5. Välj standardvärdet **RA-GRS** som lagringsredundans.
6. Välj den prenumeration som du vill skapa det nya lagringskontot i.
7. Ange en ny resursgrupp. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I dessa självstudier använder vi namnet **ContosoRG**.
8. Välj den geografiska platsen för ditt lagringskonto. Lagringskontot måste finnas i samma region som Recovery Services-valvet. I dessa självstudier använder vi regionen **Europa, västra**.

   ![create-storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. Skapa lagringskontot genom att klicka på **Skapa**.

## <a name="create-a-vault"></a>Skapa ett valv

1. I Azure-portalen klickar du på **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
2. I **Namn** anger du ett eget namn som identifierar valvet. I den här självstudien använder vi **ContosoVMVault**.
3. Välj den befintliga resursgruppen med namnet **contosoRG**.
4. Ange Azure-regionen **Europa, västra** som vi använder i de här självstudierna.
5. Du kan snabbt komma åt valvet från instrumentpanelen genom att klicka på **Fäst på instrumentpanelen** > **Skapa**.

   ![Nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

   Det nya valvet visas i **Instrumentpanelen** > **Alla resurser** och på huvudsidan **Recovery Services-valv**.

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När de virtuella Azure-datorerna har skapats från minnet efter redundansen, är de anslutna till det här nätverket.

1. I [Azure Portal](https://portal.azure.com) klickar du på **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
2. Låt **Resource Manager** vara valt som distributionsmodell. Resource Manager är den rekommenderade distributionsmodellen.
   - Ange ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. Vi använder namnet **ContosoASRnet**
   - Använd den befintliga resursgruppen **contosoRG**.
   - Ange nätverksadressintervallet 10.0.0.0/24.
   - I den här självstudien behöver vi ett undernät.
   - Välj den prenumeration där du vill skapa nätverket.
   - Välj platsen **Europa, västra**. Nätverket måste finnas i samma region som Recovery Services-valvet.
3. Klicka på **Skapa**.

   ![create-network](media/tutorial-prepare-azure/create-network.png)

   Det tar några sekunder att skapa ditt virtuella nätverk. När det har skapats ser du det i instrumentpanelen för Azure Portal.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbereda den lokala VMware-infrastrukturen på haveriberedskap till Azure](tutorial-prepare-on-premises-vmware.md)
