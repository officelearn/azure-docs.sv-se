---
title: Förbered Azure för haveri beredskap för lokala datorer med Azure Site Recovery
description: Lär dig hur du förbereder Azure för haveriberedskap för lokala datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1b8bdde64ee003d93ad15df8f1d4d8b1e3a2b5f9
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814337"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Förbereda Azure-resurser för haveriberedskap för lokala datorer

Den här artikeln beskriver hur du förbereder Azure-resurser och-komponenter så att du kan konfigurera haveri beredskap för lokala virtuella VMware-datorer, virtuella Hyper-V-datorer eller Windows/Linux fysiska servrar till Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .

Den här artikeln är den första kursen i en serie som illustrerar hur du ställer in haveriberedskap för lokala virtuella datorer. 


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrol lera att Azure-kontot har behörighet för replikering.
> * Skapa ett Recovery Services-valv. Ett valv innehåller metadata och konfigurationsinformation för virtuella datorer och andra replikeringskomponenter.
> * Konfigurera ett virtuellt Azure-nätverk (VNet). När virtuella Azure-datorer skapas efter en redundansväxling är de anslutna till det här nätverket.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artikeln i avsnittet så här i Site Recovery innehålls förteckningen.

## <a name="before-you-start"></a>Innan du börjar

- Granska arkitekturen för haveri beredskap för [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md)och [fysisk server](physical-azure-architecture.md) .
- Läs vanliga frågor för [VMware](vmware-azure-common-questions.md) och [Hyper-V](hyper-v-azure-common-questions.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar. Logga sedan in på [Azure Portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du precis har skapat ditt kostnads fria Azure-konto är du administratör för din prenumeration och du har de behörigheter som du behöver. Om du inte är administratör för prenumerationen kan du tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriv till ett Azure Storage-konto.
- Skriv till en Azure-hanterad disk.

För att slutföra dessa uppgifter måste ditt konto tilldelas en inbyggd roll som virtuell datordeltagare. För att hantera Site Recovery-åtgärder i ett valv, måste ditt konto också tilldelas en inbyggd roll som Site Recovery-deltagare.


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. I Azure Portal klickar du på **+ skapa en resurs**och söker efter **återställning**i Marketplace.
2. Klicka på **säkerhets kopiering och Site Recovery**och på sidan säkerhets kopiering och Site Recovery klickar du på **skapa**. 
1. I **Recovery Services-valv** > **Namn** anger du ett eget namn som identifierar valvet. I de här självstudierna använder vi namnet **ContosoVMVault**.
2. I **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. I den här självstudien använder vi **contosoRG**.
3. I **Plats** väljer du den region där valvet ska finnas. Använder vi **Västeuropa**.
4. För att snabbt komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Skapa ett nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

   Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Lokala datorer replikeras till Azure Managed disks. När redundansväxlingen inträffar skapas virtuella Azure-datorer från dessa hanterade diskar och ansluts till det Azure-nätverk som du anger i den här proceduren.

1. I [Azure Portal](https://portal.azure.com) markerar du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
2. Behåll **Resource Manager** valt som distributions modell.
3. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. Vi använder **ContosASRnet** i den här självinlärningskursen.
4. Ange resursgruppen där nätverket kommer att skapas. Vi använder den befintliga resursgruppen **contosoRG**.
5. I **adress intervall**anger du intervallet för nätverket. Vi använder **10.1.0.0/24**och använder inte ett undernät.
6. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.
7. I **plats**väljer du samma region som Recovery Servicess valvet har skapats i. I vår självstudie är det **Västeuropa**. Nätverket måste finnas i samma region som valvet.
8. Vi lämnar standardalternativen för grundläggande DDoS-skydd utan tjänstslutpunkten i nätverket.
9. Klicka på **Skapa**.

   ![Skapa ett virtuellt nätverk](media/tutorial-prepare-azure/create-network.png)

Det tar några sekunder att skapa ditt virtuella nätverk. När det har skapats ser du det i instrumentpanelen för Azure Portal.




## <a name="next-steps"></a>Nästa steg

- [Förbered den lokala VMware-infrastrukturen](tutorial-prepare-on-premises-vmware.md)för katastrof återställning i VMware.
- För haveri beredskap för Hyper-V [förbereder du de lokala Hyper-V-servrarna](hyper-v-prepare-on-premises-tutorial.md).
- För haveri beredskap för fysiska servrar [ställer du in konfigurations servern och käll miljön](physical-azure-disaster-recovery.md)
- [Lär dig om](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure-nätverk.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) Managed disks.
