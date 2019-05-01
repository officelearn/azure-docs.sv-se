---
title: Förbereda Azure för haveriberedskap för lokala datorer med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du förbereder Azure för haveriberedskap för lokala datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d2de871176917dcc24d910b3742bdb2700c4f25d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691758"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Förbereda Azure-resurser för haveriberedskap för lokala datorer

Den här artikeln beskriver hur du förbereder Azure-resurser och komponenter så att du kan konfigurera haveriberedskap för lokala virtuella VMware-datorer, Hyper-V-datorer eller Windows-/ Linux fysiska servrar till Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

Den här artikeln är den första kursen i en serie som illustrerar hur du ställer in haveriberedskap för lokala virtuella datorer. 


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera att Azure-konto har Replikeringsbehörighet.
> * Skapa ett Recovery Services-valv. Ett valv innehåller metadata och konfigurationsinformation för virtuella datorer och andra replikeringskomponenter.
> * Konfigurera ett Azure-nätverk (VNet). När virtuella Azure-datorerna skapats efter redundansen, är de anslutna till det här nätverket.

> [!NOTE]
> Självstudier visar den enklaste distribution sökvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artikel i avsnittet How To i Site Recovery i innehållsförteckningen.

## <a name="before-you-start"></a>Innan du börjar

- Granska arkitekturen för [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md), och [fysisk server](physical-azure-architecture.md) katastrofåterställning.
- Läs vanliga frågor för [VMware](vmware-azure-common-questions.md) och [Hyper-V](hyper-v-azure-common-questions.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar. Logga sedan in på den [Azure-portalen](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du nyligen skapade ditt kostnadsfria Azure-konto du är administratör för din prenumeration och du har de behörigheter som du behöver. Om du inte är administratör för prenumerationen kan du tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till ett Azure storage-konto.
- Skriva till en Azure hanterade diskar.

För att slutföra dessa uppgifter måste ditt konto tilldelas en inbyggd roll som virtuell datordeltagare. För att hantera Site Recovery-åtgärder i ett valv, måste ditt konto också tilldelas en inbyggd roll som Site Recovery-deltagare.


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. I Azure-portalen klickar du på **+ skapa en resurs**, och Sök på Marketplace efter **Recovery**.
2. Klicka på **Backup och Site Recovery (OMS)**. På sidan Backup och Site Recovery klickar du på **Skapa**. 
1. I **Recovery Services-valv** > **Namn** anger du ett eget namn som identifierar valvet. I de här självstudierna använder vi namnet **ContosoVMVault**.
2. I **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. I den här självstudien använder vi **contosoRG**.
3. I **Plats** väljer du den region där valvet ska finnas. Använder vi **Västeuropa**.
4. För att snabbt komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Skapa ett nytt valv](./media/tutorial-prepare-azure/new-vault-settings.png)

   Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Lokala datorer replikeras till Azure hanterade diskar. Vid redundans virtuella Azure-datorer skapas från dessa hanterade diskar, och ansluten till Azure-nätverket du anger i den här proceduren.

1. I [Azure Portal](https://portal.azure.com) markerar du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
2. Behåll **Resource Manager** valt som distributionsmodell.
3. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. Vi använder **ContosASRnet** i den här självinlärningskursen.
4. Ange resursgruppen där nätverket kommer att skapas. Vi använder den befintliga resursgruppen **contosoRG**.
5. I **adressintervall**, ange intervallet för det virtuella nätverket. Vi använder **10.1.0.0/24**, och inte använder ett undernät.
6. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.
7. I **plats**, Välj samma region som Recovery Services-valvet har skapats. I våra självstudier har **Västeuropa**. Nätverket måste finnas i samma region som valvet.
8. Vi lämnar standardalternativen för grundläggande DDoS-skydd utan tjänstslutpunkten i nätverket.
9. Klicka på **Skapa**.

   ![Skapa ett virtuellt nätverk](media/tutorial-prepare-azure/create-network.png)

Det tar några sekunder att skapa ditt virtuella nätverk. När det har skapats ser du det i instrumentpanelen för Azure Portal.




## <a name="next-steps"></a>Nästa steg

- För VMware-haveriberedskap [förbereda lokala VMware-infrastruktur](tutorial-prepare-on-premises-vmware.md).
- För Hyper-V disaster recovery [och Förbered lokalerna Hyper-V-servrar](hyper-v-prepare-on-premises-tutorial.md).
- För fysisk server disaster recovery [ställa in miljön för configuration server- och källor](physical-azure-disaster-recovery.md)
- [Lär dig om](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure-nätverk.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) hanterade diskar.
