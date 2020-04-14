---
title: Designöverväganden för Azure-skalningsuppsättningar för virtuella datorer
description: Lär dig mer om designöverväganden för dina Azure Virtual Machine Scale Sets. Jämför skalningsuppsättningar funktioner med vm-funktioner.
keywords: linux virtuell maskin, virtuella maskin skala uppsättningar
author: mimckitt
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 06/01/2017
ms.author: mimckitt
ms.openlocfilehash: 20f6cb08781c7c6aca7a4022e75a7be8640ef18a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273774"
---
# <a name="design-considerations-for-scale-sets"></a>Designöverväganden för skalningsuppsättningar
I den här artikeln beskrivs designöverväganden för skaluppsättningar för virtuella datorer. Information om vad skaluppsättningar för virtuella datorer är finns i [översikt över skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>När ska skaluppsättningar användas i stället för virtuella datorer?
I allmänhet är skalningsuppsättningar användbara för distribution av infrastruktur med högtillgänglighet där en uppsättning datorer har liknande konfiguration. Vissa funktioner är dock bara tillgängliga i skalningsuppsättningar medan andra funktioner endast är tillgängliga i virtuella datorer. För att fatta ett välgrundat beslut om när du ska använda varje teknik bör du först ta en titt på några av de vanliga funktioner som är tillgängliga i skalningsuppsättningar men inte virtuella datorer:

### <a name="scale-set-specific-features"></a>Skala setspecifika funktioner

- När du har angett skalningsuppsättningskonfigurationen kan du uppdatera *egenskapen kapacitet* för att distribuera fler virtuella datorer parallellt. Den här processen är bättre än att skriva ett skript för att dirigera distribuera många enskilda virtuella datorer parallellt.
- Du kan [använda Azure Autoscale för att automatiskt skala en skalningsuppsättning](./virtual-machine-scale-sets-autoscale-overview.md) men inte enskilda virtuella datorer.
- Du kan [anpassa skalningsuppsättningen till virtuella datorer](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) men inte enskilda virtuella [datorer.](https://docs.microsoft.com/rest/api/compute/virtualmachines)
- Du kan [överetablera](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) skalningsuppsättning virtuella datorer för ökad tillförlitlighet och snabbare driftsättningstider. Du kan inte överetablera enskilda virtuella datorer om du inte skriver anpassad kod för att utföra den här åtgärden.
- Du kan ange en [uppgraderingsprincip](./virtual-machine-scale-sets-upgrade-scale-set.md) för att göra det enkelt att distribuera uppgraderingar över virtuella datorer i skalningsuppsättningen. Med enskilda virtuella datorer måste du orkestrera uppdateringar själv.

### <a name="vm-specific-features"></a>VM-specifika funktioner

Vissa funktioner är för närvarande endast tillgängliga i virtuella datorer:

- Du kan ta en avbildning från en enskild virtuell dator, men inte från en virtuell dator i en skalningsuppsättning.
- Du kan migrera en enskild virtuell dator från inbyggda diskar till hanterade diskar, men du kan inte migrera VM-instanser i en skalningsuppsättning.
- Du kan tilldela offentliga IPv6-IP-adresser till enskilda virtuella nätverkskort för virtuella datorer, men kan inte göra det för VM-instanser i en skalningsuppsättning. Du kan tilldela offentliga IPv6-IP-adresser till belastningsutjämnare framför enskilda virtuella datorer eller skaluppsättning virtuella datorer.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Skalningsuppsättningar med Azure-hanterade diskar
Skalningsuppsättningar kan skapas med [Azure-hanterade diskar](../virtual-machines/windows/managed-disks-overview.md) i stället för traditionella Azure-lagringskonton. Hanterade diskar ger följande fördelar:
- Du behöver inte förskapa en uppsättning Azure-lagringskonton för skalningsuppsättningen virtuella datorer.
- Du kan definiera [kopplade datadiskar](virtual-machine-scale-sets-attached-disks.md) för de virtuella datorerna i skalningsuppsättningen.
- Skalningsuppsättningar kan konfigureras för att [stödja upp till 1 000 virtuella datorer i en uppsättning](virtual-machine-scale-sets-placement-groups.md). 

Om du har en befintlig mall kan du också [uppdatera mallen så att den använder hanterade diskar](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Användarhanterad lagring
En skalningsuppsättning som inte har definierats med Azure Managed Disks förlitar sig på användarskapade lagringskonton för att lagra operativsystemdiskarna för de virtuella datorerna i uppsättningen. Ett förhållande på 20 virtuella datorer per lagringskonto eller mindre rekommenderas för att uppnå maximal IO och även dra nytta av _överetablering_ (se nedan). Vi rekommenderar också att du sprider början tecken av lagringskontonamn över alfabetet. Detta bidrar till att sprida belastning över olika interna system. 


## <a name="overprovisioning"></a>Överetablering
Skalningsuppsättningar som för närvarande är standard för "överetablering" virtuella datorer. När överetablering är aktiverat snurrar skalningsuppsättningen faktiskt upp fler virtuella datorer än du bad om, och tar sedan bort de extra virtuella datorerna när det begärda antalet virtuella datorer har etablerats. Överetablering förbättrar etableringen av framgångsfrekvensen och minskar distributionstiden. Du faktureras inte för de extra virtuella datorerna och de räknas inte in i dina kvotgränser.

Även överetablering förbättrar etablering framgång, det kan orsaka förvirrande beteende för ett program som inte är utformad för att hantera extra virtuella datorer som visas och sedan försvinner. Om du vill inaktivera överetablering måste du se `"overprovision": "false"`till att du har följande sträng i mallen: . Mer information finns i [REST API-dokumentationen för skalningsuppsättning](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Om skalningsuppsättningen använder användarhanterad lagring och du inaktiverar överetablering kan du ha fler än 20 virtuella datorer per lagringskonto, men det rekommenderas inte att gå över 40 av IO-prestandaskäl. 

## <a name="limits"></a>Begränsningar
En skalningsuppsättning som bygger på en Marketplace-avbildning (kallas även en plattformsavbildning) och konfigurerad för att använda Azure Managed Disks stöder en kapacitet på upp till 1 000 virtuella datorer. Om du konfigurerar skalningsuppsättningen så att den stöder fler än 100 virtuella datorer fungerar inte alla scenarier på samma sätt (till exempel belastningsutjämning). Mer information finns i [Arbeta med skaluppsättningar för stora virtuella datorer](virtual-machine-scale-sets-placement-groups.md). 

En skalningsuppsättning som konfigurerats med användarhanterade lagringskonton är för närvarande begränsad till 100 virtuella datorer (och 5 lagringskonton rekommenderas för den här skalan).

En skalningsuppsättning som bygger på en anpassad avbildning (en som skapats av dig) kan ha en kapacitet på upp till 600 virtuella datorer när den konfigureras med Azure Managed-diskar. Om skalningsuppsättningen är konfigurerad med användarhanterade lagringskonton måste den skapa alla virtuella hårddiskar för OS-disk i ett lagringskonto. Därför är det maximala rekommenderade antalet virtuella datorer i en skalningsuppsättning som bygger på en anpassad avbildning och användarhanterad lagring 20. Om du stänger av överetablering kan du gå upp till 40.

För fler virtuella datorer än dessa gränser tillåter måste du distribuera flera skalningsuppsättningar som visas i [den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

