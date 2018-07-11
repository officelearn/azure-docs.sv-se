---
title: Återställning efter fel i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över olika typer av återställning efter fel och varningar för att anses vara vid misslyckas tillbaka till lokalt med Azure Site Recovery-tjänsten.
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanki
ms.openlocfilehash: 2a9ee380fc16c4088d98875dd465509c4023d037
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920397"
---
# <a name="overview-of-failback"></a>Översikt över återställning efter fel

När du har redundansväxlat till Azure kan växla du tillbaka till din lokala plats. Det finns två olika typer av återställning efter fel som är möjligt med Azure Site Recovery: 

- Växla tillbaka till den ursprungliga platsen 
- Växla tillbaka till en alternativ plats

Om du redundansväxlade en virtuell VMware-dator kan du växla tillbaka till samma lokala källdatorn om det fortfarande finns. I det här scenariot replikeras endast ändringar tillbaka. Det här scenariot kallas **ursprungsplatsen**. Om den lokala virtuella datorn inte finns, scenariot som är en **alternativa platsåterställningen**.

> [!NOTE]
> Du kan bara återställa till ursprungliga vCenter och konfigurationsservern. Du kan inte distribuera en ny Configuration server och växla tillbaka med hjälp av den. Du inte också lägga till en ny vCenter till befintliga konfigurationsservern och växla tillbaka till nya vCenter.

## <a name="original-location-recovery-olr"></a>Ursprungsplatsen (OLR)
Om du vill växla tillbaka till den ursprungliga virtuella datorn, måste följande villkor vara uppfyllda:

* Om den virtuella datorn hanteras av en vCenter-server, ska huvudmålserverns ESX-värd ha åtkomst till den virtuella datorns datalager.
* Om den virtuella datorn finns på en ESX-värd men inte hanteras av vCenter, måste hårddisk för den virtuella datorn vara i ett datalager som huvudmålserverns värd kan komma åt.
* Om den virtuella datorn är på en ESX-värd och inte använder vCenter, bör du genomföra identifiering av ESX-värd på Huvudmålet innan du återaktivera skyddet. Detta gäller om du växlar tillbaka fysiska servrar för.
* Du kan växla tillbaka till ett virtuellt SAN-nätverk (vSAN) eller en disk som baseras på raw-enhet mappning (RDM) om diskarna redan finns och är anslutna till den lokala virtuella datorn.

> [!IMPORTANT]
> Det är viktigt att aktivera disk.enableUUID= SANT så att Azure Site Recovery-tjänsten är att identifiera den ursprungliga VMDK på den virtuella datorn som väntande ändringar kommer att skrivas under återställningen. Om det här värdet inte är inställt på vara sant sedan tjänsten försöker identifiera den motsvarande lokala VMDK efter bästa förmåga. Om rätt VMDK inte hittas, skapas en extra disk och data skrivs in som.

## <a name="alternate-location-recovery-alr"></a>Alternativ platsåterställning till
Om den lokala virtuella datorn inte finns innan skydda den virtuella datorn, kallas scenariot en återställning till alternativ plats. Arbetsflöde för återaktivering av skydd skapar den lokala virtuella datorn igen. Detta innebär också att en fullständig nedladdning.

* När du växlar tillbaka till en annan plats, återställs den virtuella datorn till samma ESX-värden där huvudmålservern har distribuerats. Databasen som används för att skapa disken kommer att samma datalager som valdes när skydda den virtuella datorn.
* Du kan växla tillbaka endast till en virtuell dator-filsystemet (VMFS) eller virtuellt SAN-datalagret. Om du har en RDM fungerar inte återaktivering av skydd och återställning efter fel.
* Återaktivering av skydd innebär en stor inledande dataöverföring som följs av ändringarna. Den här processen finns eftersom den virtuella datorn inte finns lokalt. Replikeras tillbaka har slutförts. Den här återaktivering av skydd tar också längre tid än en återställning på ursprungsplatsen.
* Du kan inte återställa till RDM-baserade diskar. Den nya virtuella diskar (vmdk: er) kan endast skapas på en VMFS/virtuellt SAN-datalagret.

> [!NOTE]
> En fysisk dator vid redundansväxling till Azure, kan att växlas tillbaka endast som en virtuell VMware-dator. Det följer samma arbetsflöde som den alternativa platsåterställningen. Se till att identifiera minst en huvudmålservern och de nödvändiga ESX/ESXi-värdar som du behöver inte återställa.

## <a name="next-steps"></a>Nästa steg

Följ stegen för att utföra den [återställning efter fel åtgärden](vmware-azure-failback.md).

