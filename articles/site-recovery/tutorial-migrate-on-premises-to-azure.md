---
title: Migrera lokala datorer till Azure med Azure Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hur du migrerar lokala datorer till Azure med Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ee9397406cbca21d8bd53019d9daac5a037f508c
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure

Förutom att använda den [Azure Site Recovery](site-recovery-overview.md) -tjänsten för att hantera och samordna haveriberedskap för lokala datorer och virtuella Azure-datorer för affärskontinuitet och haveriberedskap (BCDR) du kan också använda plats Återställning för att hantera migreringen av lokala datorer till Azure.


Den här kursen visar hur du migrera lokala virtuella datorer och fysiska servrar till Azure. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Välj ett mål för replikering
> * Konfigurera miljön för källa och mål
> * Konfigurera en princip för lösenordsreplikering
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Kör en redundansväxling till Azure

Detta är den tredje vägledningen i en serie. Den här kursen förutsätter att du redan har slutfört uppgifterna i de föregående självstudiekurser:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. Förbereda lokalt [VMware](tutorial-prepare-on-premises-vmware.md) eller Hyper-V-servrar.

Innan du börjar är det bra att granska den [VMware](concepts-vmware-to-azure-architecture.md) eller [Hyper-V](concepts-hyper-v-to-azure-architecture.md) arkitekturer för katastrofåterställning.


## <a name="prerequisites"></a>Förutsättningar

Enheter som exporteras av paravirtualized drivrutiner som inte stöds.


## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Nytt** > **Övervakning + hantering** > **Backup och Site Recovery**.
3. I **namn**, ange det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du en.
4. Skapa en resursgrupp **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För att snabbt komma åt valvet från instrumentpanelen, klickar du på **fäst på instrumentpanelen** och klicka sedan på **skapa**.

   ![Nytt valv](./media/tutorial-migrate-on-premises-to-azure/onprem-to-azure-vault.png)

Det nya valvet har lagts till i den **instrumentpanelen** under **alla resurser**, och på primära **Recovery Services-valv** sidan.



## <a name="select-a-replication-goal"></a>Välj ett mål för replikering

Välj vad och vart du vill replikera.
1. Klicka på **Recovery Services-valv** > valvet.
2. Resurs-menyn klickar du på **Site Recovery** > **Förbered infrastrukturen** > **skyddsmål**.
3. I **skyddsmål**, väljer du vill migrera.
    - **VMware**: Välj **till Azure** > **Ja, med VMWare vSphere-Hypervisor**.
    - **Fysisk dator**: Välj **till Azure** > **inte virtualiserade/andra**.
    - **Hyper-V**: Välj **till Azure** > **Ja, med Hyper-V**. Om Hyper-V-datorer hanteras av VMM, Välj **Ja**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

- [Ställ in](tutorial-vmware-to-azure.md#set-up-the-source-environment) källmiljön för VMwares virtuella datorer.
- [Ställ in](tutorial-physical-to-azure.md#set-up-the-source-environment) källmiljön för fysiska servrar.
- [Ställ in](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) källmiljön för Hyper-V virtuella datorer.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera target-resurser.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="set-up-a-replication-policy"></a>Konfigurera en princip för lösenordsreplikering

- [Konfigurera en replikeringsprincip](tutorial-vmware-to-azure.md#create-a-replication-policy) för VMwares virtuella datorer.
- [Konfigurera en replikeringsprincip](tutorial-physical-to-azure.md#create-a-replication-policy) för fysiska servrar.
- [Konfigurera en replikeringsprincip](tutorial-hyper-v-to-azure.md#set-up-a-replication-policy) för Hyper-V virtuella datorer.


## <a name="enable-replication"></a>Aktivera replikering

- [Aktivera replikering](tutorial-vmware-to-azure.md#enable-replication) för VMwares virtuella datorer.
- [Aktivera replikering](tutorial-physical-to-azure.md#enable-replication) för fysiska servrar.
- [Aktivera replikering](tutorial-hyper-v-to-azure.md#enable-replication) för Hyper-V virtuella datorer.


## <a name="run-a-test-migration"></a>Kör ett testmigrering

Kör en [redundanstestningen](tutorial-dr-drill-azure.md) till Azure, för att se till att allt fungerar som förväntat.


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. I **inställningar** > **replikerade objekt** klickar du på datorn > **redundans**.
2. I **redundans** väljer en **återställningspunkt** att växla över till. Välj den senaste återställningspunkten.
3. Krypteringsinställningen inte är relevanta för det här scenariot.
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker göra en avstängning av virtuella källdatorer innan växling vid fel. Redundans fortsätter även om avstängning misslyckas. Du kan följa förloppet för växling vid fel på den **jobb** sidan.
5. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
6. I **replikerade objekt**, högerklicka på den virtuella datorn > **slutföra migreringen**. Detta avslutar migreringsprocessen, stoppar replikering för den virtuella datorn och stoppar Site Recovery-faktureringen för den virtuella datorn.

    ![Slutföra migrering](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Inte avbryta en växling pågår**: VM-replikeringen stoppas innan du startar redundans. Om du avbryter en växling pågår, stoppar för växling vid fel, men den virtuella datorn inte kommer att replikeras igen.

I vissa scenarier kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter för att slutföra. Du kanske ser längre testa redundans tider för fysiska servrar, VMware Linux-datorer, virtuella VMware-datorer som inte har möjliggör DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen migreras lokala virtuella datorer till virtuella Azure-datorer. Du kan nu konfigurera katastrofåterställning för virtuella Azure-datorer.

> [!div class="nextstepaction"]
> [Ställ in katastrofåterställning](site-recovery-azure-to-azure-after-migration.md) för virtuella Azure-datorer efter migreringen från en lokal plats.
