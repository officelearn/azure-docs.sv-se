---
title: Om migrering i Azure Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hur du migrerar lokala och virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 01/07/2018
ms.author: raynew
ms.openlocfilehash: 966d532a33626a8fcc3a3b93790d203aadfd81b4
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="about-migration"></a>Om migrering

Den här artikeln för en snabb överblick över hur [Azure Site Recovery](site-recovery-overview.md) service hjälper dig att migrera datorer. 

Här är vad du kan migrera med hjälp av Site Recovery:

- **Migrera från lokala till Azure**: migrera lokala Hyper-V virtuella datorer, virtuella VMware-datorer och fysiska servrar till Azure. Efter migreringen körs arbetsbelastningar som körs på lokala datorer på virtuella Azure-datorer. 
- **Migrate within Azure** (Migrera inom Azure): Migrera virtuella Azure-datorer mellan Azure-regioner. 
- **Migrate AWS** (Migrera AWS): Migrera AWS Windows-instanser till virtuella Azure IaaS-datorer. 


## <a name="what-do-we-mean-by-migration"></a>Vad menar vi med migrering?

Förutom att använda Site Recovery för katastrofåterställning av lokala och virtuella Azure-datorer kan använda du Site Recovery-tjänsten för att migrera dem. Vad är skillnaden?

- Vid katastrofåterställning kan replikera du datorer regelbundet till Azure. När ett avbrott inträffar du datorerna automatiskt växla över från den primära platsen till den sekundära platsen för Azure och komma åt dem därifrån. När den primära platsen är tillgänglig igen kan växla du tillbaka från Azure.
- För migrering kan replikera du lokala datorer till Azure eller virtuella Azure-datorer till en sekundär region. Sedan du redundansväxla den virtuella datorn från den primära platsen till sekundärt och slutföra migreringen. I den här processen ingår ingen återställning efter fel.  


## <a name="migration-scenarios"></a>Migreringsscenarier

**Scenario** | **Detaljer**
--- | ---
**Migrera från lokala till Azure** | Du kan migrera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar till Azure. Om du vill göra detta måste slutföra nästan samma steg som du skulle för fullständig katastrofåterställning. Du växla inte bara datorer tillbaka från Azure till den lokala platsen.
**Migrera mellan Azure-regioner** | Du kan migrera virtuella Azure-datorer från en Azure-region till en annan. När migreringen är klar, men du kan även konfigurera katastrofåterställning för virtuella Azure-datorer i den sekundära regionen som du har migrerat.
**Migrera AWS till Azure** | Du kan migrera AWS-instanser till virtuella Azure-datorer. Site Recovery behandlar AWS-instanser som fysiska servrar för för migrering. 

## <a name="next-steps"></a>Nästa steg

- [Migrera lokala datorer till Azure](tutorial-migrate-on-premises-to-azure.md)
- [Migrera virtuella Azure-datorer från en Azure-region till en annan](tutorial-migrate-azure-to-azure.md)
- [Migrera AWS till Azure](tutorial-migrate-aws-to-azure.md)
