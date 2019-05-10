---
title: Förbereda Azure-resurser för haveriberedskap för lokala datorer
description: Lär dig hur du förbereder Azure för haveriberedskap för lokala Hyper-V-datorer med hjälp av Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 29189a5919a01fcb897758fb64ca9e84b9381fb6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410905"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Förbereda Azure-resurser för haveriberedskap för lokala datorer

 [Azure Site Recovery](site-recovery-overview.md) hjälper företag affärskontinuitet och haveriberedskap recovery (BCDR) genom att hålla appar som körs under planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

Den här självstudien är först i en serie som beskriver hur du konfigurerar haveriberedskap för lokala Hyper-V-datorer.

> [!NOTE]
> Vi utforma självstudier för att visa den enklaste distribution sökvägen för ett scenario. De här självstudierna använder standardalternativen när det är möjligt och visas inte alla möjliga inställningar och sökvägar. Mer information finns i avsnittet ”How To” för varje motsvarande scenario.

Den här självstudien visar hur du förbereder Azure-komponenter när du vill replikera lokala virtuella datorer (Hyper-V) till Azure. Du lär dig följande:

> [!div class="checklist"]
> * Kontrollera att ditt Azure-konto har replikeringsbehörighet.
> * Skapa ett Azure storage-konto som lagrar avbildningar av replikerade datorer.
> * Skapa ett Recovery Services-valv som lagrar information om metadata och konfiguration för virtuella datorer och andra komponenter för replikering.
> * Skapa ett Azure-nätverk. När virtuella Azure-datorerna skapats efter redundansen, är de anslutna till det här nätverket.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="sign-in"></a>Logga in

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du just har skapat ett kostnadsfritt Azure-konto är du administratör för den aktuella prenumerationen. Om du inte är administratör, tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till det valda lagringskontot.

För att slutföra dessa uppgifter måste tilldelas ditt konto den inbyggda rollen virtuell Datordeltagare. För att hantera Site Recovery-åtgärder i ett valv, tilldelas ditt konto den inbyggda rollen som Site Recovery-bidragsgivare.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Avbildningar av replikerade datorer lagras i Azure-lagringen. Virtuella Azure-datorer skapas från minnet vid redundansväxling från lokalt till Azure. Lagringskontot måste finnas i samma region som Recovery Services-valvet.

1. I den [Azure-portalen](https://portal.azure.com) menyn och välj **skapa en resurs** > **Storage** > **lagringskonto – blob, fil, tabell, kö** .
2. I **Skapa lagringskonto** anger du ett namn för kontot.  Det namn du väljer måste vara unikt i Azure, vara från 3 till 24 tecken långt och endast använda gemena bokstäver och siffror. Den här självstudien använder **contosovmsacct1910171607**.
3. För **Distributionsmodell** väljer du **Resource Manager**.
4. I **typ av konto**väljer **Storage (general-purpose v1)**. Välj inte blobblagring.
5. I **Replikering** väljer du standardinställningen **Read-access geo-redundant storage** för lagringsredundans. Lämna den säker överföring krävs som inaktiverade.
6. I **Prestanda** väljer du **Standard**. I **åtkomstnivå**, väljer du standardalternativet **frekvent**.
7. I **prenumeration**, Välj den prenumeration där du vill skapa det nya lagringskontot.
8. Ange ett nytt namn på resursgruppen i **Resursgrupp**. En Azure-resursgrupp är en logisk behållare där Azure resurser distribueras och hanteras. Den här självstudien använder **ContosoRG**.
9. I **plats**, Välj den geografiska platsen för ditt lagringskonto. Den här självstudien använder **Västeuropa**.
10. Skapa lagringskontot genom att välja **Skapa**.

   ![skapar ett lagringskonto](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. I Azure-portalen väljer du **+ skapa en resurs**, och sedan söka på Azure Marketplace för Recovery Services.
2. Välj **Backup och Site Recovery (OMS)**. Därefter öppnar den **Backup och Site Recovery** väljer **skapa**.
1. I **Recovery services-valv > namn**, ange ett eget namn som identifierar valvet. I den här självstudien använder du **ContosoVMVault**.
2. I **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. Den här självstudien använder **contosoRG**.
3. I **plats**, väljer du den region där valvet ska vara belägen. Den här självstudien använder **Västeuropa**.
4. För att snabbt komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

![Skapa ett nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När de virtuella Azure-datorerna har skapats från minnet efter redundansen, är de anslutna till det här nätverket.

1. I [Azure Portal](https://portal.azure.com) markerar du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**. Lämna Resource Manager är valt som distributionsmodell.
2. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. Den här självstudien använder **ContosoASRnet**.
3. Ange resursgrupp där du vill skapa nätverket. Den här självstudien använder du den befintliga resursgruppen **contosoRG**.
4. I **adressintervall**, ange **10.0.0.0/24** som intervall för det virtuella nätverket. Det finns inget undernät för det här nätverket.
5. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.
6. I **plats**, Välj **Västeuropa**. Nätverket måste finnas i samma region som Recovery Services-valvet.
7. Standardalternativen för grundläggande DDoS-skydd med ingen tjänstslutpunkt i nätverket.
8. Välj **Skapa**.

![Skapa ett virtuellt nätverk](media/tutorial-prepare-azure/create-network.png)

Det tar några sekunder att skapa ditt virtuella nätverk. När den har skapats visas den i instrumentpanelen för Azure portal.

## <a name="useful-links"></a>Användbara länkar

Läs mer om:
- [Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbereda Hyper-V-infrastruktur på plats för haveriberedskap till Azure](hyper-v-prepare-on-premises-tutorial.md)
