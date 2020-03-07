---
title: Migrera servrar och virtuella datorer till Azure med Azure Site Recovery
description: Beskriver hur du migrerar lokala och virtuella Azure IaaS-datorer till Azure med hjälp av tjänsten Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 8e256aac16bb8c2d2f1eca494981458f71cc2e4d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363197"
---
# <a name="about-migration"></a>Om migrering

I den här artikeln finns en snabb översikt över hur tjänsten [Azure Site Recovery](site-recovery-overview.md) hjälper dig att migrera datorer. 

Det här kan du migrera med Site Recovery:

- **Migrera från lokalt till Azure**: migrera lokala virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska servrar till Azure. Efter migreringen körs arbetsbelastningar som körs på lokala datorer på virtuella Azure-datorer. 
- **Migrate within Azure** (Migrera inom Azure): Migrera virtuella Azure-datorer mellan Azure-regioner. 
- **Migrate AWS** (Migrera AWS): Migrera AWS Windows-instanser till virtuella Azure IaaS-datorer. 

> [!NOTE]
> Du kan nu migrera från en lokal plats till Azure med hjälp av tjänsten Azure Migrate. [Läs mer](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Vad menar vi med migrering?

Förutom att använda Site Recovery för haveri beredskap för lokala och virtuella Azure-datorer kan du använda tjänsten Site Recovery för att migrera dem. Vad är skillnaden?

- Vid haveri beredskap replikerar du datorer regelbundet till Azure. När ett avbrott inträffar kan du inte köra datorerna från den primära platsen till den sekundära Azure-platsen och få åtkomst till dem därifrån. När den primära platsen är tillgänglig igen går du tillbaka från Azure.
- Vid migrering replikerar du lokala datorer till Azure, eller virtuella Azure-datorer till en sekundär region. Sedan kan du inte återställa den virtuella datorn från den primära platsen till den sekundära och slutföra migreringsprocessen. I den här processen ingår ingen återställning efter fel.  


## <a name="migration-scenarios"></a>Migreringsscenarier

**Scenario** | **Detaljer**
--- | ---
**Migrera från lokal plats till Azure** | Du kan migrera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar till Azure. För att göra detta slutför du nästan samma steg som för fullständig haveri beredskap. Du växlar bara tillbaka datorer från Azure till den lokala platsen.
**Migrera mellan Azure-regioner** | Du kan migrera virtuella Azure-datorer från en Azure-region till en annan. När migreringen är klar kan du konfigurera haveri beredskap för virtuella Azure-datorer nu i den sekundära regionen som du migrerade till.
**Migrera AWS till Azure** | Du kan migrera AWS-instanser till virtuella Azure-datorer. Site Recovery behandlar AWS-instanser som fysiska servrar för migrering. 

## <a name="next-steps"></a>Nästa steg

- [Migrera lokala datorer till Azure](migrate-tutorial-on-premises-azure.md)
- [Migrera virtuella Azure-datorer från en Azure-region till en annan](azure-to-azure-tutorial-migrate.md)
- [Migrera AWS till Azure](migrate-tutorial-aws-azure.md)
