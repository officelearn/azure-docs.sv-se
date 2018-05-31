---
title: Skapa resurser som ska användas med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du förbereder Azure för replikering av lokala datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 95d6673acaf3cbac2098ac7ae30114696f477045
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212797"
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Förbereda Azure-resurser för replikering av lokala datorer

 [Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

Den här självstudien visar hur du förbereder Azure-komponenter när du vill replikera lokala virtuella datorer (Hyper-V eller VMware) eller fysiska Windows-/Linux-servrar till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera att ditt Azure-konto har replikeringsbehörighet.
> * Skapa ett Azure-lagringskonto. Replikerade data lagras i det.
> * Skapa ett Recovery Services-valv.
> * Konfigurera ett Azure-nätverk. När de virtuella Azure-datorerna skapats efter redundansen, är de anslutna till det här Azure-nätverket.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Kontrollera kontobehörigheten

Om du nyligen skapade ditt kostnadsfria Azure-konto är du administratör för prenumerationen. Om du inte är administratör för prenumerationen kan du tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till det valda lagringskontot.

För att slutföra dessa uppgifter måste ditt konto tilldelas en inbyggd roll som virtuell datordeltagare. För att hantera Site Recovery-åtgärder i ett valv, måste ditt konto också tilldelas en inbyggd roll som Site Recovery-deltagare.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Avbildningar av replikerade datorer lagras i Azure-lagringen. Virtuella Azure-datorer skapas från minnet vid redundansväxling från lokalt till Azure.

1. På menyn [Azure Portal](https://portal.azure.com) väljer du **Nytt** > **Lagring** > **Lagringskonto**.
2. I **Skapa lagringskonto** anger du ett namn för kontot. I de här självstudierna använder vi namnet **contosovmsacct1910171607**. Namnet måste vara unikt i Azure och vara mellan 3 och 24 tecken långt. Det får endast bestå av siffror och gemener.
3. För **Distributionsmodell** väljer du **Resource Manager**.
4. I **Typ av konto** väljer du **Generell användning**. I **Prestanda** väljer du **Standard**. Välj inte blobblagring.
5. I **Replikering** väljer du standardinställningen **Read-access geo-redundant storage** för lagringsredundans.
6. I **Prenumeration** väljer du den prenumeration som du vill skapa det nya lagringskontot i.
7. Ange ett nytt namn på resursgruppen i **Resursgrupp**. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I dessa självstudier ska du använda namnet **ContosoRG**.
8. Välj den geografiska platsen för ditt lagringskonto för **Plats**. Lagringskontot måste finnas i samma region som Recovery Services-valvet. I dessa självstudier använder du regionen **Europa, västra**.

   ![skapar ett lagringskonto](media/tutorial-prepare-azure/create-storageacct.png)

9. Skapa lagringskontot genom att välja **Skapa**.

## <a name="create-a-vault"></a>Skapa ett valv

1. I Azure-portalen markerar du **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
2. I **Namn** anger du ett eget namn som identifierar valvet. I de här självstudierna använder vi namnet **ContosoVMVault**.
3. I **Resursgrupp** väljer du den befintliga resursgruppen med namnet **contosoRG**.
4. I **Plats** anger du Azure-regionen **Europa, västra** som används i de här självstudierna.
5. För att snabbt komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Skapa ett nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

   Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När de virtuella Azure-datorerna har skapats från minnet efter redundansen, är de anslutna till det här nätverket.

1. I [Azure Portal](https://portal.azure.com) markerar du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
2. Låt **Resource Manager** vara valt som distributionsmodell. Resource Manager är den rekommenderade distributionsmodellen. Gör sedan följande:

   a. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. Använd namnet **ContosoASRnet**.

   b. I **Resursgrupp** använder du den befintliga resursgruppen **contosoRG**.

   c. I **Adressintervall** anger du nätverksadressintervallet **10.0.0.0/24**.

   d. I den här självstudien behöver du inget undernät.

   e. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.

   f. För **Plats** väljer du **Europa, västra**. Nätverket måste finnas i samma region som Recovery Services-valvet.

3. Välj **Skapa**.

   ![Skapa ett virtuellt nätverk](media/tutorial-prepare-azure/create-network.png)

   Det tar några sekunder att skapa ditt virtuella nätverk. När det har skapats ser du det i instrumentpanelen för Azure Portal.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbereda den lokala VMware-infrastrukturen på haveriberedskap till Azure](tutorial-prepare-on-premises-vmware.md)
