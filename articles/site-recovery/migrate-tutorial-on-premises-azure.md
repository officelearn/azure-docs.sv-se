---
title: Migrera lokala datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala datorer till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fc15db91b8f4cc6dbdecd0e7321abdbf81744f08
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357973"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure


Den här artikeln beskriver hur du migrerar lokala datorer till Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md). I allmänhet används Site Recovery för att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer. Det kan dock också användas för migrering. Migreringen använder samma steg som katastrofåterställning med ett undantag. Vid en migrering är redundansväxla datorer från din lokala plats det sista steget. Till skillnad från haveriberedskap, kan du inte återställa till lokalt i ett Migreringsscenario.


Den här kursen visar hur du migrerar lokala virtuella datorer och fysiska servrar till Azure. Lär dig att:

> [!div class="checklist"]
> * Ställa in källa och mål-miljön för migrering
> * Konfigurerar en replikeringsprincip
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Kör en engångsredundans till Azure


> [!TIP]
> Tjänsten Azure Migrate erbjuder nu en förhandsgranskning för en ny, utan Agent upplevelse för migrera virtuella VMware-datorer till Azure. [Lär dig mer](https://aka.ms/migrateVMs-signup).


## <a name="before-you-start"></a>Innan du börjar

Observera att enheter som exporteras av paravirtualiserade drivrutiner inte stöds.


## <a name="prepare-azure-and-on-premises"></a>Förbereda Azure och lokalt

1. Förbereda Azure enligt beskrivningen i [i den här artikeln](tutorial-prepare-azure.md). Även om den här artikeln beskriver steg för förberedelse för katastrofåterställning, kan stegen användas för migrering.
2. Förbered lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md)- eller [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-servrar. Om du migrerar fysiska datorer, behöver du inte förbereda något. Verifiera bara den [stödmatris](vmware-physical-azure-support-matrix.md).


## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

Välj vad och vart du vill replikera.
1. Klicka på **Recovery Services-valv** > valv.
2. På Resurs-menyn klickar du på **Site Recovery** > **Förbered infrastrukturen** > **Skyddsmål**.
3. I **Skyddsmål** väljer du vad du vill migrera.
    - **VMware**: Välj **Till Azure** > **Ja, med VMWare vSphere Hypervisor**.
    - **Fysisk dator**: Välj **Till Azure** > **Inte virtualiserad/övrigt**.
    - **Hyper-V**: Välj **Till Azure** > **Ja, med Hyper-V**. Om de virtuella Hyper-V-datorerna hanteras av VMM väljer du **Ja**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

**Scenario** | **Information**
--- | --- 
VMware | Konfigurera den [källmiljö](vmware-azure-set-up-source.md), och Ställ in den [konfigurationsservern](vmware-azure-deploy-configuration-server.md).
Fysisk dator | [Konfigurera](physical-azure-set-up-source.md) miljön och konfigurationen för källservern.
Hyper-V | Konfigurera den [källmiljö](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Konfigurera den [källmiljö](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) för Hyper-V som distribuerats med System Center VMM.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar Azure-resurser.
    - Om du migrerar virtuella VMware-datorer eller fysiska servrar, kontrollerar Site Recovery du har ett Azure-nätverk som virtuella Azure-datorer kommer att finnas när de skapas efter en redundansväxling.
    - Om du migrerar Hyper-V-datorer, kontrollerar Site Recovery du har en kompatibel Azure storage-konto och nätverk.
4. Om du migrerar Hyper-V-datorer som hanteras av System Center VMM, ställa in [nätverksmappning](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

**Scenario** | **Information**
--- | --- 
VMware | Konfigurera en [replikeringsprincip](vmware-azure-set-up-replication.md) för virtuella VMware-datorer.
Fysisk dator | Konfigurera en [replikeringsprincip](physical-azure-disaster-recovery.md#create-a-replication-policy) för fysiska datorer.
Hyper-V | Konfigurera en [replikeringsprincip](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Konfigurera en [replikeringsprincip](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) för Hyper-V som distribuerats med System Center VMM.

## <a name="enable-replication"></a>Aktivera replikering

**Scenario** | **Information**
--- | --- 
VMware | [Aktivera replikering](vmware-azure-enable-replication.md) för virtuella VMware-datorer.
Fysisk dator | [Aktivera replikering](physical-azure-disaster-recovery.md#enable-replication) för fysiska datorer.
Hyper-V | [Aktivera replikering](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Aktivera replikering](hyper-v-vmm-azure-tutorial.md#enable-replication) för Hyper-V som distribuerats med System Center VMM.


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

   - Avslutar migreringsprocessen, stoppar replikeringen för den lokala virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.
   - Det här steget rensar replikeringsdata. Men det raderar inte de migrerade virtuella datorerna.

     ![Slutföra migrering](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Avbryt inte en redundansväxling som pågår**: Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att redundanstiden är längre för fysiska servrar, VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Efter migrering

När datorerna har migrerats till Azure, finns det ett antal steg du bör utföra.

Vissa steg kan automatiseras som en del av migreringsprocessen med den inbyggda funktionen automationsskript i [återställningsplanerna](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Eftermigreringssteg i Azure

- Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer. 
- Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- [Azure VM-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security.
    - Om du migrerar VMware-datorer och fysiska servrar, installerar installationsprogrammet för Mobilitetstjänsten tillgängliga Azure VM-agenter på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter redundansväxling.
    - Om du migrerar virtuella Azure-datorer till en sekundär region, måste Azure VM-agenten etableras på den virtuella datorn före migreringen.
    - Om du migrerar virtuella Hyper-V-datorer till Azure, ska du installera Azure VM-agenten på den virtuella Azure-datorn efter migreringen.
- Ta bort alla Site Recovery-providers/agenter manuellt från den virtuella datorn. Om du migrerar virtuella VMware-datorer eller fysiska servrar, avinstallera mobilitetstjänsten från den virtuella datorn.
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

I den här självstudiekursen migrerade du lokala virtuella datorer till virtuella Azure-datorer. Nu

> [!div class="nextstepaction"]
> [Ställ in katastrofåterställning](azure-to-azure-replicate-after-migration.md) till en sekundär Azure-region för virtuella Azure-datorer.

  
