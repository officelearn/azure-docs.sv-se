---
title: Återställning efter fel under haveriberedskap med Azure Site Recovery | Microsoft-dokument
description: Den här artikeln innehåller en översikt över olika typer av återställnings- och förbehåll som ska beaktas samtidigt som den inte går tillbaka till lokala under haveriberedskap med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281839"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Återställning efter fel för virtuella VMware-datorer efter haveriberedskap till Azure

När du har misslyckats med att azure som en del av din haveriberedskapsprocess kan du växla tillbaka till din lokala webbplats. Det finns två olika typer av återställning efter fel som är möjliga med Azure Site Recovery: 

- Växla tillbaka till den ursprungliga platsen 
- Växla tillbaka till en alternativ plats

Om du misslyckades över en virtuell VMware-dator kan du växla tillbaka till samma lokala källa om den fortfarande finns. I det här fallet replikeras endast ändringarna tillbaka. Det här scenariot kallas **ursprunglig platsåterställning**. Om den lokala virtuella datorn inte finns är scenariot en **alternativ platsåterställning**.

> [!NOTE]
> Du kan bara växla tillbaka till den ursprungliga vCenter- och konfigurationsservern. Du kan inte distribuera en ny konfigurationsserver och växla tillbaka med den. Du kan inte heller lägga till ett nytt vCenter på den befintliga konfigurationsservern och återställa återställningen i det nya virtuellacentret.

## <a name="original-location-recovery-olr"></a>Ursprunglig platsåterställning (OLR)
Om du väljer att växla tillbaka till den ursprungliga virtuella datorn måste följande villkor uppfyllas:

* Om den virtuella datorn hanteras av en vCenter-server bör huvudmålets ESX-värd ha åtkomst till den virtuella datorns datalager.
* Om den virtuella datorn finns på en ESX-värd men inte hanteras av vCenter måste hårddisken på den virtuella datorn finnas i ett datalager som huvudmålets värd kan komma åt.
* Om din virtuella dator finns på en ESX-värd och inte använder vCenter bör du slutföra identifieringen av ESX-värden för huvudmålet innan du roterar igen. Detta gäller om du misslyckas tillbaka fysiska servrar också.
* Du kan växla tillbaka till ett virtuellt lagringsnätverk (vSAN) eller en disk som baseras på fjärrenhetsmappning (RDM) om diskarna redan finns och är anslutna till den lokala virtuella datorn.

> [!IMPORTANT]
> Det är viktigt att aktivera disk.enableUUID= SANT så att Azure Site Recovery-tjänsten under återställning av återställning av återställning av återställning av den är klar kan identifiera den ursprungliga VMDK på den virtuella datorn som de väntande ändringarna skrivs till. Om det här värdet inte är sant försöker tjänsten identifiera motsvarande lokala VMDK på bästa sätt. Om rätt VMDK inte hittas skapas en extra disk och data skrivs vidare till det.

## <a name="alternate-location-recovery-alr"></a>Alternativ platsåterställning (ALR)
Om den lokala virtuella datorn inte finns innan den virtuella datorn återbeskyddas, kallas scenariot för en alternativ platsåterställning. Arbetsflödet för reprotect skapar den lokala virtuella datorn igen. Detta kommer också att orsaka en fullständig datahämtning.

* När du växlar tillbaka till en alternativ plats återställs den virtuella datorn till samma ESX-värd där huvudmålservern distribueras. Datalagret som används för att skapa disken kommer att vara samma datalager som valdes när den virtuella datorn återbeskyddades.
* Du kan bara växla tillbaka till ett VMFS-system (Virtual Machine File System) eller vSAN.You can fail back only to a virtual machine file system (VMFS) or vSAN datastore. Om du har en RDM fungerar inte reprotect och failback.
* Reprotect innebär en stor inledande dataöverföring som följs av ändringarna. Den här processen finns eftersom den virtuella datorn inte finns lokalt. Fullständiga data måste replikeras tillbaka. Den här reprotecten tar också mer tid än en ursprunglig platsåterställning.
* Du kan inte växla tillbaka till RDM-baserade diskar. Endast nya virtuella datordiskar (VMDK: er) kan skapas på ett VMFS/vSAN-datacenter.

> [!NOTE]
> En fysisk dator, när det misslyckades över till Azure, kan endast skickas tillbaka som en virtuell VMware-dator. Detta följer samma arbetsflöde som den alternativa platsåterställning. Se till att du upptäcker minst en huvudmålserver och de nödvändiga ESX/ESXi-värdar som du behöver för att växla tillbaka.

## <a name="next-steps"></a>Nästa steg

Följ stegen för att utföra [återställningsåtgärden](vmware-azure-failback.md).

