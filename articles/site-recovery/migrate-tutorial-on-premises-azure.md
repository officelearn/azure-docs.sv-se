---
title: Migrera lokala datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala datorer till Azure med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b297e2ef2f4c276b9183d1874e104d686b304a14
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919129"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure

Förutom att använda tjänsten [Azure Site Recovery](site-recovery-overview.md) för att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer i syftet affärskontinuitet och haveriberedskap (BCDR) kan du även använda Site Recovery för att hantera migrering av lokala datorer till Azure.


Den här kursen visar hur du migrerar lokala virtuella datorer och fysiska servrar till Azure. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Välj ett replikeringsmål
> * Konfigurerar käll- och målmiljön
> * Konfigurerar en replikeringsprincip
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Kör en engångsredundans till Azure

Detta är den tredje självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. Förbereda lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md)- eller [Hyper-V]-servrar (hyper-v-prepare-on-premises-tutorial.md).

Innan du börjar är det bra att granska [VMware](vmware-azure-architecture.md)- eller [Hyper-V](hyper-v-azure-architecture.md)-arkitekturen för haveriberedskap.


## <a name="prerequisites"></a>Nödvändiga komponenter

Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
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
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
5. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Slutför migrering**. Detta avslutar migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Slutföra migrering](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Replikeringen av den virtuella datorn stoppas innan redundansen startas. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att redundanstiden är längre för fysiska servrar, VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen migrerade du lokala virtuella datorer till virtuella Azure-datorer. Nu kan du konfigurera haveriberedskap för de virtuella Azure-datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap](azure-to-azure-replicate-after-migration.md) för virtuella Azure-datorer efter migrering från en lokal plats.
