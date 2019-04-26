---
title: Om migreringen av lokala datorer och Azure virtuella datorer Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala och virtuella Azure IaaS-datorer till Azure med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 856d03b1ecc1c7a3bd527eb265061f9a305d8f50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196056"
---
# <a name="about-migration"></a>Om migrering

Läs den här artikeln för en snabb överblick över hur [Azure Site Recovery](site-recovery-overview.md) tjänsten hjälper dig att migrera datorer. 

Här är vad du kan migrera med hjälp av Site Recovery:

- **Migrera från lokal till Azure**: Migrera lokala virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska servrar till Azure. Efter migreringen körs arbetsbelastningar som körs på lokala datorer på virtuella Azure-datorer. 
- **Migrera inom Azure**: Migrera virtuella Azure-datorer mellan Azure-regioner. 
- **Migrera AWS**: Migrera AWS Windows-instanser till virtuella Azure IaaS-datorer. 


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
