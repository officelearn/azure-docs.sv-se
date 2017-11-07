---
title: Migrera lokala virtuella datorer och fysiska servrar till Azure med Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hur du migrerar lokala virtuella datorer och fysiska servrar till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Migrera till Azure med Site Recovery

Läs den här artikeln för att lära dig om att använda [Azure Site Recovery](site-recovery-overview.md)-tjänsten för att migrera lokala virtuella datorer och fysiska servrar till virtuella Azure-datorer.

## <a name="before-you-start"></a>Innan du börjar

Se det här videoklippet för en snabb översikt över de steg som krävs för att migrera till Azure.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>Vad menar vi med migrering?

Du kan distribuera Site Recovery för replikering av lokala virtuella datorer och fysiska servrar och sedan migrera dem.

- När du replikerar konfigurerar du lokala datorer för att regelbundet replikera till Azure. När ett avbrott uppstår kan du sedan redundansväxla datorerna från den lokala platsen till Azure och komma åt dem därifrån. När den lokala platsen är tillgänglig igen kan du återställa efter fel från Azure.
- När du använder Site Recovery för migrering replikerar du lokala datorer till Azure. Sedan redundansväxlar du dem från den lokala platsen till Azure och slutför migreringsprocessen. I den här processen ingår ingen återställning efter fel.  

## <a name="what-can-site-recovery-migrate"></a>Vad kan Site Recovery migrera?

Du kan:

- **Migrate from on-premises** (Migrera från lokal plats) Migrera lokala virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska servrar som till Azure. Efter migreringen körs arbetsbelastningar som körs på lokala datorer på virtuella Azure-datorer. 
- **Migrate within Azure** (Migrera inom Azure): Migrera virtuella Azure-datorer mellan Azure-regioner. 
- **Migrate AWS** (Migrera AWS): Migrera AWS Windows-instanser till virtuella Azure IaaS-datorer. 

## <a name="migrate-from-on-premises-to-azure"></a>Migrera från lokal plats till Azure

För att migrera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar följer du i princip samma steg som för fullständig replikering. 


## <a name="migrate-between-azure-regions"></a>Migrera mellan Azure-regioner

För att migrera virtuella Azure-datorer följer du i princip samma steg som för fullständig migrering.

1. Du [aktiverar replikering](azure-to-azure-tutorial-enable-replication.md) för de datorer som du vill migrera.
2. Du [kör ett snabbt redundanstest](azure-to-azure-tutorial-dr-drill.md) för att kontrollera att allt fungerar.
3. Sedan kör du en [oplanerad redundans](azure-to-azure-tutorial-failover-failback.md) med alternativet **Fullständig migrering**.
4. När du har slutfört migreringen kan du [konfigurera replikering för haveriberedskap](site-recovery-azure-to-azure-after-migration.md) från Azure-regionen som du har migrerat till, till en sekundär region.



## <a name="migrate-aws-to-azure"></a>Migrera AWS till Azure

Du kan migrera AWS-instanser till virtuella Azure-datorer.
- I det här scenariot stöds bara migrering. Med andra ord kan du replikera AWS-instanser och växla över dem till Azure, men du kan inte återställa.
- AWS-instanser hanteras på samma sätt som fysiska servrar när det gäller migrering. Du ställer in ett Recovery Services-valv, distribuerar en lokal konfigurationsserver för att hantera replikering, lägger till den till valvet och anger replikeringsinställningar.
- Du aktiverar replikering för de datorer som du vill migrera och kör en snabb testredundans. Sedan kör du en oplanerad redundans med alternativet **Fullständig migrering**.






## <a name="next-steps"></a>Nästa steg

- [Migrera lokala datorer till Azure](tutorial-migrate-on-premises-to-azure.md)
- [Migrera virtuella Azure-datorer från en Azure-region till en annan](site-recovery-migrate-azure-to-azure.md)
- [Migrera AWS till Azure](tutorial-migrate-aws-to-azure.md)
