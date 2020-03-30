---
title: Migrera lokala datorer med Azure Site Recovery
description: Den här artikeln beskriver hur du migrerar lokala datorer till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b978190776aee3c89d3beadde76d20c4327b012f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388924"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure


I den här artikeln beskrivs hur du migrerar lokala datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md). 

> [!TIP]
> Du bör nu använda Azure Migrate för att migrera lokala datorer till Azure, i stället för Azure Site Recovery-tjänsten. [Läs mer](../migrate/migrate-services-overview.md).


Den här kursen visar hur du migrerar lokala virtuella datorer och fysiska servrar till Azure. Lär dig att:

> [!div class="checklist"]
> * Ställ in käll- och målmiljön för migrering
> * Konfigurerar en replikeringsprincip
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Kör en engångsredundans till Azure


> [!TIP]
> Du kan nu migrera lokala servrar till Azure med hjälp av Azure Migrate-tjänsten. [Läs mer](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Innan du börjar

Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.


## <a name="prepare-azure-and-on-premises"></a>Förbereda Azure och lokalt

1. Förbered Azure enligt beskrivningen i [den här artikeln](tutorial-prepare-azure.md). Även om den här artikeln beskriver förberedelsesteg för haveriberedskap gäller stegen även för migrering.
2. Förbered lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md)- eller [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-servrar. Om du migrerar fysiska maskiner behöver du inte förbereda något. Kontrollera bara [stödmatrisen](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

Välj vad och vart du vill replikera.
1. Klicka på **Recovery Services-valv** > valv.
2. Klicka på Målet för förbereda > **infrastrukturskydd**för **webbplatsåterställning** > **Prepare Infrastructure**på Resurs-menyn.
3. I **Skyddsmål** väljer du vad du vill migrera.
    - **VMware**: Välj **Till Azure** > **Ja, med VMWare vSphere Hypervisor**.
    - **Fysisk dator**: Välj **Till Azure** > **Inte virtualiserad/övrigt**.
    - **Hyper-V**: Välj **Till Azure** > **Ja, med Hyper-V**. Om de virtuella Hyper-V-datorerna hanteras av VMM väljer du **Ja**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

**Scenario** | **Detaljer**
--- | --- 
VMware | Konfigurera [källmiljön](vmware-azure-set-up-source.md)och konfigurera [konfigurationsservern](vmware-azure-deploy-configuration-server.md).
Fysisk maskin | [Konfigurera](physical-azure-set-up-source.md) källmiljön och konfigurationsservern.
Hyper-V | Konfigurera [källmiljön](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Konfigurera [källmiljön](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) för Hyper-V som distribueras med System Center VMM.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastrukturmål** > **Target**och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar Azure-resurserna.
    - Om du migrerar virtuella virtuella datorer för VMware eller fysiska servrar verifierar Site Recovery att du har ett Azure-nätverk där virtuella Azure-datorer kommer att finnas när de skapas efter redundans.
    - Om du migrerar virtuella virtuella hyper-v-datorer verifierar Site Recovery att du har ett kompatibelt Azure-lagringskonto och nätverk.
4. Om du migrerar virtuella hyper-V-datorer som hanteras av SYSTEM Center VMM konfigurerar du [nätverksmappning](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

**Scenario** | **Detaljer**
--- | --- 
VMware | Ställ in en [replikeringsprincip](vmware-azure-set-up-replication.md) för virtuella datorer med VMware.
Fysisk maskin | Ställ in en [replikeringsprincip](physical-azure-disaster-recovery.md#create-a-replication-policy) för fysiska datorer.
Hyper-V | Konfigurera en [replikeringsprincip](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Konfigurera en [replikeringsprincip](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) för Hyper-V som distribueras med SYSTEM Center VMM.

## <a name="enable-replication"></a>Aktivera replikering

**Scenario** | **Detaljer**
--- | --- 
VMware | [Aktivera replikering](vmware-azure-enable-replication.md) för virtuella VMware-datorer.
Fysisk maskin | [Aktivera replikering](physical-azure-disaster-recovery.md#enable-replication) för fysiska datorer.
Hyper-V | [Aktivera replikering](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Aktivera replikering](hyper-v-vmm-azure-tutorial.md#enable-replication) för Hyper-V som distribueras med SYSTEM Center VMM.


## <a name="run-a-test-migration"></a>Kör en testmigrering

Kör en [testredundansväxling](tutorial-dr-drill-azure.md) till Azure för att kontrollera att allt fungerar som förväntat.


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. Klicka på datorn > **Redundans**i **Inställningar** > **replikerade objekt** .
2. I **Redundans** väljer du en **återställningspunkt** att växla över till. Välj den senaste återställningspunkten.
3. Inställningen för krypteringsnyckeln är inte relevant för det här scenariot.
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker att stänga av virtuella datorer innan redundansen utlöses. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
5. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Slutför migrering**. Det här gör följande:

   - Slutför migreringsprocessen, stoppar replikeringen för den lokala virtuella datorn och stoppar platsåterställningsfakturering för den virtuella datorn.
   - Det här steget rensar replikeringsdata. Men det raderar inte de migrerade virtuella datorerna.

     ![Slutföra migrering](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Replikeringen av den virtuella datorn stoppas innan redundansen startas. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker längre test failover gånger för fysiska servrar, VMware Linux-maskiner, VMware virtuella datorer som inte har DHCP-tjänsten aktiverad och VMware virtuella datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Efter migrering

När datorerna har migrerats till Azure, finns det ett antal steg du bör utföra.

Vissa steg kan automatiseras som en del av migreringsprocessen med den inbyggda funktionen automationsskript i [återställningsplanerna](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Eftermigreringssteg i Azure

- Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer. 
- Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- [Azure VM-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security.
    - Om du migrerar VMware-datorer och fysiska servrar, installerar installationsprogrammet för Mobilitetstjänsten tillgängliga Azure VM-agenter på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter redundansväxling.
    - Om du migrerar virtuella Azure-datorer till en sekundär region måste Azure VM-agenten etableras på den virtuella datorn före migreringen.
    - Om du migrerar virtuella hyper-V-datorer till Azure installerar du Azure VM-agenten på Den virtuella Azure-datorn efter migreringen.
- Ta bort alla Site Recovery-providers/agenter manuellt från den virtuella datorn. Om du migrerar virtuella virtuella datorer eller fysiska servrar avinstallerar du mobilitetstjänsten från den virtuella datorn.
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

I den här självstudiekursen migrerade du lokala virtuella datorer till virtuella Azure-datorer. Now

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap](azure-to-azure-replicate-after-migration.md) till en sekundär Azure-region för virtuella Azure-datorer.

  
