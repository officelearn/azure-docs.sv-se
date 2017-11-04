---
title: Migrera lokala datorer till Azure med Azure Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hur du migrerar lokala datorer till Azure med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cfd44f7f06faa7d1d00efa9427dbf5d1d0a89ef1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten hanterar och samordnar replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här kursen visar hur du migrera lokala virtuella datorer och fysiska servrar till Azure med Site Recovery. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ställ in krav för distributionen
> * Skapa ett Recovery Services-valv för Site Recovery
> * Distribuera lokala hanteringsservrar
> * Konfigurera en replikeringsprincip och aktivera replikering
> * Kör disaster recovery visa se till att allt fungerar
> * Kör en redundansväxling till Azure

## <a name="overview"></a>Översikt

Du kan migrera en dator genom att aktivera replikering för den och växla över till Azure.


## <a name="prerequisites"></a>Krav

Det här är vad du behöver göra för den här självstudiekursen.

- [Förbereda](tutorial-prepare-azure.md) Azure-resurser, inklusive en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
- [Förbereda](tutorial-prepare-on-premises-vmware.md) VMware lokal VMware-servrar och virtuella datorer.
- Observera att enheter som exporteras av paravirtualized drivrutiner inte stöds.


## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Välj en skyddsmål

Välj vad och vart du vill replikera.
1. Klicka på **Recovery Services-valv** > valvet.
2. Resurs-menyn klickar du på **Site Recovery** > **Förbered infrastrukturen** > **skyddsmål**.
3. I **skyddsmål**väljer:
    - **VMware**: Välj **till Azure** > **Ja, med VMWare vSphere-Hypervisor**.
    - **Fysisk dator**: Välj **till Azure** > **inte virtualiserade/andra**.
    - **Hyper-V**: Välj **till Azure** > **Ja, med Hyper-V**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

- [Ställ in](tutorial-vmware-to-azure.md#set-up-the-source-environment) källmiljön för VMwares virtuella datorer.
- [Ställ in](tutorial-physical-to-azure.md#set-up-the-source-environment) källmiljön för fysiska servrar.
- [Ställ in](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) källmiljön för Hyper-V virtuella datorer.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera target-resurser.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange distributionsmodellen som mål.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

- [Konfigurera en replikeringsprincip](tutorial-vmware-to-azure.md#create-a-replication-policy) för VMwares virtuella datorer.


## <a name="enable-replication"></a>Aktivera replikering

- [Aktivera replikering](tutorial-vmware-to-azure.md#enable-replication) för VMwares virtuella datorer.


## <a name="run-a-test-migration"></a>Kör ett testmigrering

Kör en [redundanstestningen](tutorial-dr-drill-azure.md) till Azure, för att se till att allt fungerar som förväntat.


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. I **inställningar** > **replikerade objekt** klickar du på datorn > **redundans**.
2. I **redundans** väljer en **återställningspunkt** att växla över till. Välj den senaste återställningspunkten.
3. Krypteringsinställningen inte är relevanta för det här scenariot.
4. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan växling vid fel. Redundans fortsätter även om avstängning misslyckas. Du kan följa förloppet för växling vid fel på den **jobb** sidan.
5. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
6. I **replikerade objekt**, högerklicka på den virtuella datorn > **slutföra migreringen**. Detta avslutar migreringsprocessen, stoppar replikering för den virtuella datorn och stoppar Site Recovery-faktureringen för den virtuella datorn.

    ![Slutföra migrering](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Inte avbryta en växling pågår**: innan redundans startas VM replikeringen har stoppats. Om du avbryter en växling pågår, stoppar för växling vid fel, men den virtuella datorn inte kommer att replikeras igen.

I vissa scenarier kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter för att slutföra. Du kanske ser längre testa redundans tider för fysiska servrar, VMware Linux-datorer, virtuella VMware-datorer som inte har möjliggör DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Replikera virtuella Azure-datorer till en annan region efter migrering till Azure](site-recovery-azure-to-azure-after-migration.md)
