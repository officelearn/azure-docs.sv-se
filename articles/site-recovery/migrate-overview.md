---
title: Migrera servrar och virtuella datorer till Azure med Azure Site Recovery
description: Beskriver hur du migrerar lokala och virtuella Azure IaaS-datorer till Azure med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: a7107eae5c798deb78d4d35eccdf4adcf5273335
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388907"
---
# <a name="about-migration"></a>Om migrering

Läs den här artikeln för en snabb översikt över hur [Azure Site Recovery-tjänsten](site-recovery-overview.md) hjälper dig att migrera datorer. 

> [!TIP]
> Du bör nu använda Azure Migrate-tjänsten för att migrera virtuella datorer och servrar till Azure, i stället för Azure Site Recovery-tjänsten. [Läs mer](../migrate/migrate-services-overview.md).


Det här kan du migrera med site recovery:

- **Migrera från lokala till Azure:** Migrera lokala virtuella hyper-V-datorer, virtuella virtuella datorer med VMware och fysiska servrar till Azure. Efter migreringen körs arbetsbelastningar som körs på lokala datorer på virtuella Azure-datorer. 
- **Migrate within Azure** (Migrera inom Azure): Migrera virtuella Azure-datorer mellan Azure-regioner. 
- **Migrate AWS** (Migrera AWS): Migrera AWS Windows-instanser till virtuella Azure IaaS-datorer. 

> [!NOTE]
> Du kan nu migrera från lokalt till Azure med hjälp av Azure Migrate-tjänsten. [Läs mer](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Vad menar vi med migrering?

Förutom att använda Site Recovery för haveriberedskap av lokala och virtuella Azure-datorer kan du använda tjänsten Site Recovery för att migrera dem. Vad är skillnaden?

- För haveriberedskap replikerar du datorer regelbundet till Azure. När ett avbrott inträffar misslyckas datorerna från den primära platsen till den sekundära Azure-platsen och kommer åt dem därifrån. När den primära platsen är tillgänglig igen kan du återställa från Azure.
- För migrering replikerar du lokala datorer till Azure eller virtuella Azure-datorer till en sekundär region. Sedan kan du växla över den virtuella datorn från den primära platsen till den sekundära och slutföra migreringen. I den här processen ingår ingen återställning efter fel.  


## <a name="migration-scenarios"></a> Migreringsscenarier

**Scenario** | **Detaljer**
--- | ---
**Migrera från lokalt till Azure** | Du kan migrera lokala virtuella datorer, virtuella datorer med hyper-V och fysiska servrar till Azure. För att göra detta slutför du nästan samma steg som du skulle för fullständig haveriberedskap. Du misslyckas helt enkelt inte datorer tillbaka från Azure till den lokala platsen.
**Migrera mellan Azure-regioner** | Du kan migrera virtuella Azure-datorer från en Azure-region till en annan. När migreringen är klar kan du konfigurera haveriberedskap för virtuella Azure-datorer som nu finns i den sekundära regionen som du migrerade till.
**Migrera AWS till Azure** | Du kan migrera AWS-instanser till virtuella Azure-datorer. Site Recovery behandlar AWS-instanser som fysiska servrar för migreringsändamål. 

## <a name="next-steps"></a>Nästa steg

- [Migrera lokala datorer till Azure](migrate-tutorial-on-premises-azure.md)
- [Migrera virtuella Azure-datorer från en Azure-region till en annan](azure-to-azure-tutorial-migrate.md)
- [Migrera AWS till Azure](migrate-tutorial-aws-azure.md)
