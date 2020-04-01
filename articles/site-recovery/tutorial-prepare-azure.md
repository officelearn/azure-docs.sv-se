---
title: Förbereda Azure för lokal haveriberedskap med Azure Site Recovery
description: Lär dig hur du förbereder Azure för haveriberedskap för lokala datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ba5ecc29edbcd69324500e87add846e4395ce0a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067557"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Förbereda Azure för lokal haveriberedskap till Azure

I den här artikeln beskrivs hur du förbereder Azure-resurser och -komponenter så att du kan konfigurera haveriberedskap för lokala virtuella datorer, virtuella hyper-virtuella datorer eller windows/linux fysiska servrar till Azure med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)

Den här artikeln är den första kursen i en serie som illustrerar hur du ställer in haveriberedskap för lokala virtuella datorer. 


I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Kontrollera att Azure-kontot har replikeringsbehörigheter.
> * Skapa ett Recovery Services-valv. Ett valv innehåller metadata och konfigurationsinformation för virtuella datorer och andra replikeringskomponenter.
> * Konfigurera ett virtuellt Azure-nätverk (VNet). När virtuella Azure-datorer skapas efter redundans ansluts de till det här nätverket.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i artikeln i avsnittet Så här gör du i innehållsförteckningen för webbplats.

## <a name="before-you-start"></a>Innan du börjar

- Granska arkitekturen för [VMware,](vmware-azure-architecture.md) [Hyper-V](hyper-v-azure-architecture.md)och fysisk serverkatastrofåterställning. [physical server](physical-azure-architecture.md)
- Läs vanliga frågor för [VMware](vmware-azure-common-questions.md) och [Hyper-V](hyper-v-azure-common-questions.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar. Logga sedan in på [Azure-portalen](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du just har skapat ditt kostnadsfria Azure-konto är du administratör för din prenumeration och du har de behörigheter du behöver. Om du inte är administratör för prenumerationen kan du tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriv till ett Azure-lagringskonto.
- Skriv till en Azure-hanterad disk.

För att slutföra dessa uppgifter måste ditt konto tilldelas en inbyggd roll som virtuell datordeltagare. Dessutom, för att hantera site recovery-åtgärder i ett valv, bör ditt konto tilldelas den inbyggda rollen site recovery contributor.


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. På Portal-menyn i Azure väljer du **Skapa en resurs**och söker på Marketplace efter **återställning**.
2. Välj **Säkerhetskopiering och Webbplatsåterställning** från sökresultaten och klicka på **Skapa**på sidan Säkerhetskopiering och webbplatsåterställning . 
3. På sidan **Skapa återställningstjänster** väljer du **prenumerationen**. Vi använder **Contoso Subscription.**
4. I **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. I den här självstudien använder vi **contosoRG**.
5. Ange ett eget namn för att identifiera valvet i **Arkiv-namnet.** I de här självstudierna använder vi namnet **ContosoVMVault**.
6. I **Region**väljer du den region där valvet ska placeras. Använder vi **Europa, västra**.
7. Välj **Granska + skapa**.

   ![Skapa ett nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

   Det nya valvet visas nu i **Instrumentpanel** > **alla resurser**och på sidan för de viktigaste **återställningstjänsternas valv.**

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Lokala datorer replikeras till Azure-hanterade diskar. När redundans inträffar skapas virtuella Azure-datorer från dessa hanterade diskar och ansluts till Azure-nätverket som du anger i den här proceduren.

1. I [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
2. Håll **Resurshanteraren** markerat som distributionsmodell.
3. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. Vi använder **ContosASRnet** i den här självinlärningskursen.
4. I **Adressutrymme**anger du det virtuella nätverkets adressintervall i CDR-notation. Vi använder **10.1.0.0/24**.
5. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.
6. Ange den **resursgrupp** där nätverket ska skapas. Vi använder den befintliga resursgruppen **contosoRG**.
7. Välj samma region som det där valvet för återställningstjänster skapades i **Plats.** I vår handledning är det **Västeuropa**. Nätverket måste finnas i samma region som valvet.
8. Ange nätverkets område i **adressintervallet.** Vi använder **10.1.0.0/24**och använder inte ett undernät.
9. Vi lämnar standardalternativen för grundläggande DDoS-skydd, utan tjänstslutpunkt eller brandvägg i nätverket.
9. Välj **Skapa**.

   ![Skapa ett virtuellt nätverk](media/tutorial-prepare-azure/create-network.png)

Det tar några sekunder att skapa ditt virtuella nätverk. När den har skapats visas den på Instrumentpanelen för Azure-portalen.




## <a name="next-steps"></a>Nästa steg

- För VMware-haveriberedskap [förbereder du den lokala VMware-infrastrukturen](tutorial-prepare-on-premises-vmware.md).
- Förbereda [de lokala Hyper-V-servrarna](hyper-v-prepare-on-premises-tutorial.md)för Hyper-V-haveriberedskap .
- För fysisk återställning av [serverkatastrofer konfigurerar du konfigurationsservern och källmiljön](physical-azure-disaster-recovery.md)
- [Lär dig om](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure-nätverk.
- [Läs mer om](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) hanterade diskar.
