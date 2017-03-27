---
title: Migrera till Azure med Site Recovery | Microsoft Docs
description: "Den här artikeln ger en översikt över migrering av virtuella datorer och fysiska servrar till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 981155c38bdc8cb54639d2271be1f3bd3036125c
ms.lasthandoff: 03/09/2017


---
# <a name="migrate-to-azure-with-site-recovery"></a>Migrera till Azure med Site Recovery

Läs den här artikeln för en översikt över användning av Azure Site Recovery-tjänsten för migrering av virtuella datorer och fysiska servrar.

Site Recovery är en Azure-tjänst som understödjer din BCDR-strategi genom att dirigera replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar fortsätter att vara tillgängliga. Du växlar tillbaka till den primära platsen när den har återgått till normal drift. Läs mer i [Vad är Site Recovery?](site-recovery-overview.md) Du kan även använda Site Recovery för att migrera dina befintliga lokala arbetsbelastningar till Azure för att påskynda ditt molnresa och utnyttja samlingen med funktioner som Azure erbjuder.

I den här videon får du en snabb översikt över hur du utför migrering.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

Den här artikeln beskriver distribution i [Azure-portalen](https://portal.azure.com). Den [klassiska Azure-portalen](https://manage.windowsazure.com/) kan användas för att bibehålla befintliga Site Recovery-valv men du kan inte skapa nya valv.

Skriva eventuella kommentarer längst ned i den här artikeln. Om du har tekniska frågor kan du ställa dem i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="what-do-we-mean-by-migration"></a>Vad menar vi med migrering?

Du kan distribuera Site Recovery för replikering av lokala virtuella datorer och fysiska servrar till Azure eller till en sekundär plats. Du kan replikera datorer, växla dem från den primära platsen när avbrott uppstår och återställa dem till den primära platsen när den har återhämtat sig. Förutom detta kan du använda Site Recovery för att migrera virtuella datorer och fysiska servrar till Azure så att användarna kan komma åt dem som virtuella Azure-datorer. Migrering innebär replikering, redundans från den primära platsen till Azure och en fullständig migreringsgest.

## <a name="what-can-site-recovery-migrate"></a>Vad kan Site Recovery migrera?

Du kan:

- Migrera arbetsbelastningar som körs på en lokal virtuell Hyper-V-dator, virtuella VMware-datorer och fysiska servrar som ska köras på virtuella Azure-datorer. Du kan också göra fullständig replikering och återställning i det här scenariot.
- Migrera [virtuella Azure IaaS-datorer](site-recovery-migrate-azure-to-azure.md) mellan Azure-regioner. För närvarande stöds endast migrering i detta scenario, vilket innebär att återställning efter fel inte stöds.
- Migrera [AWS Windows-instanser](site-recovery-migrate-aws-to-azure.md) till virtuella Azure IaaS-datorer. För närvarande stöds endast migrering i detta scenario, vilket innebär att återställning efter fel inte stöds.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Migrera lokala virtuella datorer och fysiska servrar

För att migrera lokala virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska servrar följer du i princip samma steg som vid vanlig replikering. Du ställer in ett Recovery Services-valv, konfigurerar de nödvändiga hanteringsservrarna (beroende på vad du vill migrera), lägger till dem i valvet och anger replikeringsinställningarna. Du aktiverar replikering för de datorer som du vill migrera och kör en snabb testredundans för att se till att allt fungerar som den ska.

När du har kontrollerat att din replikeringsmiljö fungerar, använder du en planerad eller oplanerad redundans beroende på [vad som stöds](site-recovery-failover.md) för scenariot. För migrering behöver du inte utföra någon redundansväxling. Istället väljer du alternativet **Fullständig migrering** för varje dator som du vill migrera. Åtgärden **Fullständig migrering** slutför migreringsprocessen, tar bort replikering för datorn och stoppar Site Recovery-debitering för datorn.

![fullständig migrering](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Migrera mellan Azure-regioner

Du kan migrera virtuella Azure-datorer mellan regioner med hjälp av Site Recovery. I det här scenariot stöds bara migrering. Med andra ord kan du replikera virtuella Azure-datorer och växla över dem till en annan region, men du kan inte återställa. I det här scenariot ställer du in ett Recovery Services-valv, distribuerar en lokal konfigurationsserver för att hantera replikering, lägger till den till valvet och anger replikeringsinställningar. Du aktiverar replikering för de datorer som du vill migrera och kör en snabb testredundans. Sedan kör du en oplanerad redundans med alternativet **Fullständig migrering**.

## <a name="migrate-aws-to-azure"></a>Migrera AWS till Azure

Du kan migrera AWS-instanser till virtuella Azure-datorer. I det här scenariot stöds bara migrering. Med andra ord kan du replikera AWS-instanser och växla över dem till Azure, men du kan inte återställa. AWS-instanser hanteras på samma sätt som fysiska servrar när det gäller migrering. Du ställer in ett Recovery Services-valv, distribuerar en lokal konfigurationsserver för att hantera replikering, lägger till den till valvet och anger replikeringsinställningar. Du aktiverar replikering för de datorer som du vill migrera och kör en snabb testredundans. Sedan kör du en oplanerad redundans med alternativet **Fullständig migrering**.




## <a name="next-steps"></a>Nästa steg

- [Migrera virtuella VMware-datorer till Azure](site-recovery-vmware-to-azure.md)
- [Migrera virtuella Hyper-V-datorer i VMM-moln till Azure](site-recovery-vmm-to-azure.md)
- [Migrera virtuella Hyper-V-datorer utan VMM till Azure](site-recovery-hyper-v-site-to-azure.md)
- [Migrera virtuella Azure-datorer mellan Azure-regioner](site-recovery-migrate-azure-to-azure.md)
- [Migrera AWS-instanser till Azure](site-recovery-migrate-aws-to-azure.md)

