---
title: Förbereda Azure för Hyper-V-haveriberedskap med Azure Site Recovery
description: Lär dig hur du förbereder Azure för haveriberedskap av lokala virtuella hyper-virtuella datorer med hjälp av Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084177"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Förbereda Azure-resurser för Hyper-V-haveriberedskap

 [Azure Site Recovery](site-recovery-overview.md) hjälper affärskontinuitet och haveriberedskap (BCDR) genom att hålla affärsappar igång under planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

Den här självstudien är den första i en serie som beskriver hur du ställer in haveriberedskap för lokala virtuella hyper-virtuella datorer.

> [!NOTE]
> Vi utformar självstudier för att visa den enklaste distributionsvägen för ett scenario. Dessa självstudier använder standardalternativ när det är möjligt och visar inte alla möjliga inställningar och sökvägar. Mer information finns i avsnittet "Så här gör du" för varje motsvarande scenario.

Den här självstudien visar hur du förbereder Azure-komponenter när du vill replikera lokala virtuella datorer (Hyper-V) till Azure. Du lär dig följande:

> [!div class="checklist"]
> * Kontrollera att ditt Azure-konto har replikeringsbehörighet.
> * Skapa ett Azure-lagringskonto som lagrar avbildningar av replikerade datorer.
> * Skapa ett Recovery Services-valv som lagrar metadata och konfigurationsinformation för virtuella datorer och andra replikeringskomponenter.
> * Skapa ett Azure-nätverk. När virtuella Azure-datorer skapas efter redundans ansluts de till det här nätverket.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="sign-in"></a>Logga in

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du just har skapat ett kostnadsfritt Azure-konto är du administratör för den prenumerationen. Om du inte är administratör arbetar du med administratören för att tilldela de behörigheter du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till det valda lagringskontot.

För att slutföra dessa uppgifter bör ditt konto tilldelas den inbyggda rollen för den virtuella datorns deltagare. Om du vill hantera site recovery-åtgärder i ett valv bör ditt konto tilldelas den inbyggda rollen site recovery contributor.

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Avbildningar av replikerade datorer lagras i Azure-lagringen. Virtuella Azure-datorer skapas från minnet vid redundansväxling från lokalt till Azure. Lagringskontot måste finnas i samma region som Recovery Services-valvet.

1. På [Portal-menyn i Azure](https://portal.azure.com) väljer du **Skapa ett** >  > **resurslagringslagringskonto - blob, fil, tabell, kö**.**Storage**
2. I **Skapa lagringskonto** anger du ett namn för kontot.  Namnet du väljer måste vara unikt i Azure, vara mellan 3 och 24 tecken långt och endast använda gemener och siffror. För den här självstudien, använd **contosovmsacct1910171607**.
3. För **Distributionsmodell** väljer du **Resource Manager**.
4. I **Kontosort**väljer du **Lagring (allmänt v1)**. Välj inte blobblagring.
5. I **Replikering** väljer du standardinställningen **Read-access geo-redundant storage** för lagringsredundans. Lämna inställningen säker överföring som krävs som inaktiverad.
6. I **Prestanda** väljer du **Standard**. Välj sedan standardalternativet **Hot**på **Åtkomstnivå**.
7. I **Prenumeration**väljer du den prenumeration där du vill skapa det nya lagringskontot.
8. Ange ett nytt namn på resursgruppen i **Resursgrupp**. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. För den här självstudien använder du **ContosoRG**.
9. Välj den geografiska platsen för ditt lagringskonto i **Plats.** För den här guiden använder du **Västeuropa**.
10. Skapa lagringskontot genom att välja **Skapa**.

   ![Skapa ett lagringskonto](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. I Azure-portalen väljer du **+Skapa en resurs**och söker sedan på Azure Marketplace for Recovery Services.
2. Välj **Säkerhetskopiering och platsåterställning (OMS)**. På sidan **Säkerhetskopiering och platsåterställning** väljer du **Skapa**.
1. Ange ett eget namn för att identifiera valvet > i valvet för **återställningstjänster.** I den här självstudien använder du **ContosoVMVault**.
2. I **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. För den här självstudien använder **du contosoRG**.
3. I **Plats**väljer du det område där valvet ska placeras. För den här guiden använder du **Västeuropa**.
4. Om du snabbt vill komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

![Skapa ett nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

Det nya valvet visas på **instrumentpanelen** > **Alla resurser**och på sidan för de viktigaste valven för **Återställningstjänster.**

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När de virtuella Azure-datorerna har skapats från minnet efter redundansen, är de anslutna till det här nätverket.

1. I [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**. Låt Resource Manager vara valt som distributionsmodell.
2. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. För den här självstudien använder du **ContosoASRnet**.
3. Ange den resursgrupp där nätverket ska skapas. För den här självstudien använder du den befintliga resursgruppen **contosoRG**.
4. I **Adressintervall anger**du **10.0.0.0/24** som nätverksintervall. Det finns inget undernät för det här nätverket.
5. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.
6. På **Plats**väljer du **Västeuropa**. Nätverket måste finnas i samma region som Recovery Services-valvet.
7. Lämna standardalternativen för grundläggande DDoS-skydd, utan tjänstslutpunkt i nätverket.
8. Välj **Skapa**.

![Skapa ett virtuellt nätverk](media/tutorial-prepare-azure/create-network.png)

Det tar några sekunder att skapa ditt virtuella nätverk. När den har skapats visas den på Instrumentpanelen för Azure-portalen.

## <a name="useful-links"></a>Användbara länkar

Läs om:
- [Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbereda den lokala Hyper-V-infrastrukturen för haveriberedskap till Azure](hyper-v-prepare-on-premises-tutorial.md)
