---
title: Flytta virtuella Azure IaaS-datorer till en annan Azure-region med hjälp av Azure Site Recovery-tjänsten | Microsoft Docs
description: Använd Azure Site Recovery för att flytta virtuella IaaS-datorer i Azure från en Azure-region till en annan.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2ce9c486dee3f26d23db5da67abfea4701f85796
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040482"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Utöver att använda tjänsten [Azure Site Recovery](site-recovery-overview.md) för att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer i syftet affärskontinuitet och haveriberedskap (BCDR) kan du även använda Site Recovery för att hantera flytt av virtuella Azure-datorer till en sekundär region. För att flytta virtuella Azure-datorer aktiverar du replikering för dem och redundansväxlar dem från den primära regionen till den sekundära region som du väljer.

I den här självstudien får du lära dig att flytta virtuella Azure-datorer till en annan region. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv
> * Aktivera replikering för en virtuell dator
> * Köra en redundans för att flytta den virtuella datorn

I den här självstudien förutsätts att du redan har en Azure-prenumeration. Om du inte har det kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.





## <a name="prerequisites"></a>Nödvändiga komponenter

- Kontrollera att du har virtuella Azure-datorer i den Azure-region som du vill flytta från.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).



## <a name="before-you-start"></a>Innan du börjar

Slutför de här stegen innan du konfigurerar replikering.


### <a name="verify-target-resources"></a>Verifiera målresurser

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen för haveriberedskap. Kontakta supporten och aktivera den kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Site Recovery väljer samma storlek eller närmaste möjliga storlek för den virtuella måldatorn.


### <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du nyligen skapade ditt kostnadsfria Azure-konto är du administratör för prenumerationen. Om du inte är administratör för prenumerationen kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha:

1. Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen ”Virtuell datordeltagare” har dessa behörigheter, som omfattar:
    - Behörighet att skapa en virtuell dator i den valda resursgruppen
    - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
    - Behörighet att skriva till det valda lagringskontot

2. Du behöver också behörighet för att hantera åtgärder i Azure Site Recovery. Rollen Site Recovery-bidragsgivare har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i ett Recovery Services-valv.


### <a name="verify-vm-outbound-access"></a>Verifiera utgående åtkomst till VM

1. Använd inte en autentiseringsproxy för att kontrollera nätverksanslutning för virtuella datorer som du vill flytta. 
2. För den här självstudien antar vi att de virtuella datorer som du vill flytta kan ansluta till Internet och att de inte använder en brandväggsproxy för att kontrollera utgående åtkomst. Om du gör det kan du läsa kraven [här](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Verifiera certifikat för virtuella datorer

Kontrollera att alla de senaste rotcertifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rotcertifikaten inte finns kan de virtuella datorerna inte registreras i Site Recovery på grund av säkerhetsbegränsningar.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
- I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.



## <a name="create-a-vault"></a>Skapa ett valv

Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Hanteringsverktyg** > **Backup och Site Recovery**.
3. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Nytt valv](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.






## <a name="select-the-source"></a>Välj källan

1. I Recovery Services-valven klickar du på **ConsotoVMVault** > **+Replikera**.
2. I **Källa** väljer du **Azure**.
3. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
4. Välj Resource Manager-distributionsmodellen. Välj sedan **källresursgrupp**.
5. Spara inställningarna genom att klicka på **OK**.


## <a name="enable-replication-for-azure-vms"></a>Aktivera replikering för virtuella Azure-datorer

Site Recovery hämtar en lista med de virtuella datorer som är kopplade till prenumerationen och resursgruppen.


1. Klicka på **Virtuella datorer** på Azure-portalen.
2. Välj den virtuella dator som du vill flytta. Klicka sedan på **OK**.
3. I **Inställningar** klickar du på **Haveriberedskap**.
4. I **Konfigurera haveriberedskap** > **Målregion** väljer du den målregion som du ska replikera till.
5. I den här självstudiekursen accepterar du de andra standardinställningarna.
6. Klicka på **Aktivera replikering**. Ett jobb startas som aktiverar replikering för den virtuella datorn.

    ![aktivera replikering](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>Köra en redundansväxling

1. I **Inställningar** > **Replikerade objekt** klickar du på datorn och sedan på **Redundans**.
2. I **Redundans** väljer du **Senaste**. Inställningen för krypteringsnyckeln är inte relevant för det här scenariot.
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
5. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar migreringsprocessen,
6. När genomförandet är klart klickar du på **Inaktivera replikering**.  Detta stoppar replikeringen för den virtuella datorn.



## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en virtuell Azure-dator till en annan Azure-region. Nu kan du konfigurera haveriberedskap för de flyttade virtuella datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)

