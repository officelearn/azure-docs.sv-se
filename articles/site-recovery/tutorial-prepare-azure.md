---
title: Förbereda Azure för haveriberedskap för lokala datorer med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du förbereder Azure för haveriberedskap för lokala datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/03/2019
ms.author: mayg
ms.custom: MVC
ms.openlocfilehash: 5168fc28952631f00c2415d6bc171a130dc85dfd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838569"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Förbereda Azure-resurser för haveriberedskap för lokala datorer

 [Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

Den här artikeln är den första kursen i en serie som illustrerar hur du ställer in haveriberedskap för lokala virtuella datorer. Det är relevant oavsett om du skyddar lokala virtuella VMware-datorer, virtuella Hyper-V-datorer eller fysiska servrar.

> [!NOTE]
> Självstudiekurser är utformade för att visa den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i **instruktionsavsnittet** för motsvarande scenario.

Den här artikeln visar hur du förbereder Azure-komponenter när du vill replikera lokala virtuella datorer (Hyper-V eller VMware) eller fysiska Windows-/Linux-servrar till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera att ditt Azure-konto har replikeringsbehörighet.
> * Skapa ett Recovery Services-valv. Ett valv innehåller metadata och konfigurationsinformation för virtuella datorer och andra replikeringskomponenter.
> * Skapa ett Azure-nätverk. När de virtuella Azure-datorerna skapats efter redundansen, är de anslutna till det här Azure-nätverket.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du nyligen skapade ditt kostnadsfria Azure-konto är du administratör för prenumerationen. Om du inte är administratör för prenumerationen kan du tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till lagringskontot.
- Skriva till hanterade diskar.

För att slutföra dessa uppgifter måste ditt konto tilldelas en inbyggd roll som virtuell datordeltagare. För att hantera Site Recovery-åtgärder i ett valv, måste ditt konto också tilldelas en inbyggd roll som Site Recovery-deltagare.


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. I Azure-portalen klickar du på **+Skapa en resurs** och söker i Marketplace efter **Recovery services**.
2. Klicka på **Backup och Site Recovery (OMS)**. På sidan Backup och Site Recovery klickar du på **Skapa**. 
1. I **Recovery Services-valv** > **Namn** anger du ett eget namn som identifierar valvet. I de här självstudierna använder vi namnet **ContosoVMVault**.
2. I **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. I den här självstudien använder vi **contosoRG**.
3. I **Plats** väljer du den region där valvet ska finnas. Använder vi **Västeuropa**.
4. För att snabbt komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Skapa ett nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

   Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När virtuella Azure-datorer skapas från hanterade diskar efter en redundansväxling, anslutna de till nätverket.

1. I [Azure Portal](https://portal.azure.com) markerar du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
2. Låt **Resource Manager** vara valt som distributionsmodell.
3. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. Vi använder **ContosASRnet** i den här självinlärningskursen.
4. Ange resursgruppen där nätverket kommer att skapas. Vi använder den befintliga resursgruppen **contosoRG**.
5. I **Adressintervall** anger du nätverksadressintervallet **10.0.0.0/24**. I det här nätverket använder vi inte ett undernät.
6. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.
7. För **Plats** väljer du **Europa, västra**. Nätverket måste finnas i samma region som Recovery Services-valvet.
8. Vi lämnar standardalternativen för grundläggande DDoS-skydd utan tjänstslutpunkten i nätverket.
9. Klicka på **Skapa**.

   ![Skapa ett virtuellt nätverk](media/tutorial-prepare-azure/create-network.png)

   Det tar några sekunder att skapa ditt virtuella nätverk. När det har skapats ser du det i instrumentpanelen för Azure Portal.

## <a name="useful-links"></a>Användbara länkar

- [Lär dig om](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure-nätverk.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) hanterade diskar.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbereda den lokala VMware-infrastrukturen på haveriberedskap till Azure](tutorial-prepare-on-premises-vmware.md)
