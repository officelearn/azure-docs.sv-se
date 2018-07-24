---
title: Om migrering i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala och virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 2cc030bf9724e98c1ef0a43134c5066c81b38b6b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213096"
---
# <a name="about-migration"></a>Om migrering

Läs den här artikeln för en snabb överblick över hur [Azure Site Recovery](site-recovery-overview.md) tjänsten hjälper dig att migrera datorer. 

Här är vad du kan migrera med hjälp av Site Recovery:

- **Migrera från lokal till Azure**: migrera lokala virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska servrar till Azure. Efter migreringen körs arbetsbelastningar som körs på lokala datorer på virtuella Azure-datorer. 
- **Migrate within Azure** (Migrera inom Azure): Migrera virtuella Azure-datorer mellan Azure-regioner. 
- **Migrate AWS** (Migrera AWS): Migrera AWS Windows-instanser till virtuella Azure IaaS-datorer. 


## <a name="what-do-we-mean-by-migration"></a>Vad menar vi med migrering?

Förutom att använda Site Recovery för haveriberedskap för lokala och virtuella Azure-datorer kan använda du Site Recovery-tjänsten för att migrera dem. Vad är skillnaden?

- För disaster recovery replikera datorer med jämna mellanrum till Azure. När ett avbrott uppstår kan du redundansväxla datorerna från den primära platsen till den sekundära platsen för Azure och komma åt dem därifrån. När den primära platsen är tillgänglig igen, redundansväxla från Azure.
- För migrering kan replikera du lokala datorer till Azure eller virtuella Azure-datorer till en sekundär region. Sedan du redundansväxlar den virtuella datorn från den primära platsen till sekundärt och slutföra migreringen. I den här processen ingår ingen återställning efter fel.  


## <a name="migration-scenarios"></a>Migreringsscenarier

**Scenario** | **Detaljer**
--- | ---
**Migrera från lokal till Azure** | Du kan migrera lokala virtuella VMware-datorer, Hyper-V-datorer och fysiska servrar till Azure. Om du vill göra detta måste slutföra du nästan likadant, precis som för fullständig haveriberedskap. Du inte helt enkelt inte datorer från Azure till den lokala platsen.
**Migrera mellan Azure-regioner** | Du kan migrera virtuella Azure-datorer från en Azure-region till en annan. När migreringen är klar kan konfigurera du haveriberedskap för virtuella Azure-datorer nu i den sekundära regionen som du har migrerat.
**Migrera AWS till Azure** | Du kan migrera AWS-instanser till virtuella Azure-datorer. Site Recovery behandlar AWS-instanser som fysiska servrar för migrering. 

## <a name="next-steps"></a>Nästa steg

- [Migrera lokala datorer till Azure](migrate-tutorial-on-premises-azure.md)
- [Migrera virtuella Azure-datorer från en Azure-region till en annan](azure-to-azure-tutorial-migrate.md)
- [Migrera AWS till Azure](migrate-tutorial-aws-azure.md)
