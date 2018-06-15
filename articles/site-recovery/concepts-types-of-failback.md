---
title: Återställning efter fel i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över olika typer av återställning efter fel och varningar beaktas vid misslyckas tillbaka till lokala med Azure Site Recovery-tjänsten.
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanki
ms.openlocfilehash: 372a7867b47960338d7a1bf7e646fb9fffbe72e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29803560"
---
# <a name="overview-of-failback"></a>Översikt över återställning efter fel

När du har redundansväxlats till Azure, kan du växla tillbaka till den lokala platsen. Det finns två olika typer av återställning som är möjliga med Azure Site Recovery: 

- Växla tillbaka till den ursprungliga platsen 
- Växla tillbaka till en alternativ plats

Om du redundansväxlade en virtuell VMware-dator kan du växla tillbaka till samma lokala virtuella källdatorn om det fortfarande finns. I det här scenariot replikeras bara ändringarna tillbaka. Det här scenariot kallas **återställning av ursprunglig plats**. Om den lokala virtuella datorn inte finns, är en **alternativa platsåterställningen**.

> [!NOTE]
> Du kan endast återställning till det ursprungliga vCenter och konfigurationsservern. Du kan inte distribuera en ny konfigurationsserver och växla tillbaka använder den. Dessutom du inte lägga till en ny vCenter befintliga konfigurationsservern och återställning efter fel i den nya vCenter.

## <a name="original-location-recovery-olr"></a>Återställning av ursprunglig plats (OLR)
Om du vill växla tillbaka till den ursprungliga virtuella datorn, måste följande villkor uppfyllas:

* Om den virtuella datorn hanteras av en vCenter-server ska på huvudmålservern ESX-värden ha åtkomst till databasen för den virtuella datorn.
* Om den virtuella datorn finns på en ESX-värd men inte hanteras av vCenter, måste hårddisken på den virtuella datorn vara i ett datalager som har åtkomst till värden på huvudmålservern.
* Om den virtuella datorn finns på en ESX-värd och inte använder vCenter, bör du genomföra identifieringen av ESX-värd på Huvudmålet innan du skyddar. Detta gäller att om du växlar tillbaka fysiska servrar för.
* Du kan växla tillbaka till ett virtuellt SAN-nätverk (vSAN) eller en disk som baseras på raw-enhet mappning (RDM) om diskarna finns redan och är anslutna till den lokala virtuella datorn.

> [!IMPORTANT]
> Det är viktigt att aktivera disk.enableUUID= TRUE så att under återställning efter fel, Azure Site Recovery-tjänsten kan identifiera den ursprungliga VMDK på den virtuella datorn som skrivs den väntande ändringar. Om det här värdet är inte vara TRUE tjänsten försöker identifiera den motsvarande lokala VMDK för bästa prestanda. Om rätt VMDK inte hittas, skapas en extra disk och data skrivs in som.

## <a name="alternate-location-recovery-alr"></a>Alternativ platsåterställning till
Om den lokala virtuella datorn inte finns innan du skydda den virtuella datorn kallas scenariot en återställning till alternativ plats. Skapa nytt arbetsflöde skapar den lokala virtuella datorn igen. Detta medför även fullständiga data hämtas.

* När du växlar tillbaka till en alternativ plats för återställs den virtuella datorn till samma ESX-värd som är distribuerad på huvudmålservern. Databasen som används för att skapa disken kommer att samma datalager som valdes när skydda den virtuella datorn.
* Kan du växla tillbaka endast till en virtuell dator file system (VMFS) eller ett virtuellt SAN-datalagret. Om du har en RDM fungerar inte skyddar och återställning efter fel.
* Skapa nytt innebär en stor inledande dataöverföringen som följs av ändringarna. Den här processen finns eftersom den virtuella datorn inte finns lokalt. Fullständiga data måste replikeras tillbaka. Detta skyddar också tar längre tid än en återställning av ursprunglig plats.
* Du kan inte växla tillbaka till RDM-baserade diskar. Den nya virtuella diskar (VMDKs) kan bara skapas på en VMFS/virtuellt SAN-datalagret.

> [!NOTE]
> En fysisk dator när växlas över till Azure, kan flyttas tillbaka endast som en virtuell VMware-dator. Det följer samma arbetsflöde som den alternativa platsåterställningen. Se till att identifiera minst ett huvudmålservern och nödvändiga ESX/ESXi-värdar som du behöver återställas.

## <a name="next-steps"></a>Nästa steg

Följ stegen för att utföra den [återställning efter fel igen](vmware-azure-failback.md).

