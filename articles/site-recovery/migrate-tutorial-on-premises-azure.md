---
title: Migrera lokala datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala datorer till Azure med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6c97289cd1ad406769613621afdc16c8096f4aeb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116175"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure

Förutom att använda tjänsten [Azure Site Recovery](site-recovery-overview.md) för att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer i syftet affärskontinuitet och haveriberedskap (BCDR) kan du även använda Site Recovery för att hantera migrering av lokala datorer till Azure.


Den här kursen visar hur du migrerar lokala virtuella datorer och fysiska servrar till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Välj ett replikeringsmål
> * Konfigurerar käll- och målmiljön
> * Konfigurerar en replikeringsprincip
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Kör en engångsredundans till Azure

Detta är den tredje självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. Förbered lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md)- eller [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-servrar.

Innan du börjar är det bra att granska [VMware](vmware-azure-architecture.md)- eller [Hyper-V](hyper-v-azure-architecture.md)-arkitekturen för haveriberedskap.


## <a name="prerequisites"></a>Nödvändiga komponenter

Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Hanteringsverktyg** > **Backup och Site Recovery**.
3. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Nytt valv](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.



## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

Välj vad och vart du vill replikera.
1. Klicka på **Recovery Services-valv** > valv.
2. På Resurs-menyn klickar du på **Site Recovery** > **Förbered infrastrukturen** > **Skyddsmål**.
3. I **Skyddsmål** väljer du vad du vill migrera.
    - **VMware**: Välj **Till Azure** > **Ja, med VMWare vSphere Hypervisor**.
    - **Fysisk dator**: Välj **Till Azure** > **Inte virtualiserad/övrigt**.
    - **Hyper-V**: Välj **Till Azure** > **Ja, med Hyper-V**. Om de virtuella Hyper-V-datorerna hanteras av VMM väljer du **Ja**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

- [Konfigurera](vmware-azure-tutorial.md#set-up-the-source-environment) källmiljön för virtuella VMware-datorer.
- [Konfigurera](physical-azure-disaster-recovery.md#set-up-the-source-environment) källmiljön för fysiska servrar.
- [Konfigurera](hyper-v-azure-tutorial.md#set-up-the-source-environment) källmiljön för virtuella Hyper-V-datorer.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

- [Konfigurera en replikeringsprincip](vmware-azure-tutorial.md#create-a-replication-policy) för virtuella VMware-datorer.
- [Konfigurera en replikeringsprincip](physical-azure-disaster-recovery.md#create-a-replication-policy) för fysiska servrar.
- [Konfigurera en replikeringsprincip](hyper-v-azure-tutorial.md#set-up-a-replication-policy) för virtuella Hyper-V-datorer.


## <a name="enable-replication"></a>Aktivera replikering

- [Aktivera replikering](vmware-azure-tutorial.md#enable-replication) för virtuella VMware-datorer.
- [Aktivera replikering](physical-azure-disaster-recovery.md#enable-replication) för fysiska servrar.
- Aktivera replikering för virtuella Hyper-V-datorer [med](hyper-v-vmm-azure-tutorial.md#enable-replication) eller [utan VMM](hyper-v-azure-tutorial.md#enable-replication).


## <a name="run-a-test-migration"></a>Kör en testmigrering

Kör en [testredundansväxling](tutorial-dr-drill-azure.md) till Azure för att kontrollera att allt fungerar som förväntat.


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. I **Inställningar** > **Replikerade objekt** klickar du på datorn > **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Välj den senaste återställningspunkten.
3. Inställningen för krypteringsnyckeln är inte relevant för det här scenariot.
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker att stänga av virtuella datorer innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
5. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Slutför migrering**. Det här gör följande:

    - Avslutar migreringsprocessen, stoppar replikeringen för virtuella datorer i AWS och stoppar Site Recovery-debitering för den virtuella datorn.
    - Det här steget rensar replikeringsdata. Men det raderar inte de migrerade virtuella datorerna.

    ![Slutföra migrering](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Avbryt inte en redundansväxling som pågår**: Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att redundanstiden är längre för fysiska servrar, VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Efter migrering

När datorerna har migrerats till Azure, finns det ett antal steg du bör utföra.

Vissa steg kan automatiseras som en del av migreringsprocessen med den inbyggda funktionen automationsskript i [återställningsplanerna]( https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)   


### <a name="post-migration-steps-in-azure"></a>Eftermigreringssteg i Azure

- Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer. 
- Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- [Azure VM-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security.
    - Om du migrerar VMware-datorer och fysiska servrar, installerar installationsprogrammet för Mobilitetstjänsten tillgängliga Azure VM-agenter på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter redundansväxling.
    - Om du migrerar virtuella Azure-datorer till en sekundär region, måste Azure VM-agenten etableras på den virtuella datorn före migreringen.
    - Om du migrerar virtuella Hyper-V-datorer till Azure, ska du installera Azure VM-agenten på den virtuella Azure-datorn efter migreringen.
- Ta bort alla Site Recovery-providers/agenter manuellt från den virtuella datorn. Om du migrerar virtuella VMware-datorer eller fysiska servrar [avinstallera Mobilitetstjänsten][vmware-azure-install-mobility-service.md#uninstall-mobility-service-on-a-windows-server-computer] från den virtuella Azure-datorn.
- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](azure-to-azure-quickstart.md).
- För ökad säkerhet:
    - Lås och begränsa åtkomsten för inkommande trafik med Azure Security Centers [just-in-time-administration]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.

### <a name="post-migration-steps-on-premises"></a>Lokala eftermigreringssteg

- Flytta över apptrafiken till den app som körs på den migrerade Azure-VM-instansen.
- Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
- Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
- Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen migrerade du lokala virtuella datorer till virtuella Azure-datorer. Nu kan du [konfigurera haveriberedskap](azure-to-azure-replicate-after-migration.md) för de virtuella Azure-datorerna till en sekundär Azure-region.

  
