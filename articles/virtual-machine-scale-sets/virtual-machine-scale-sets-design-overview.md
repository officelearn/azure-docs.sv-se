---
title: "Utformning för Skalningsuppsättningar i virtuella Azure-datorn | Microsoft Docs"
description: "Lär dig mer om designöverväganden för din Azure Virtual Machine-Skalningsuppsättningar"
keywords: Anger om Linux-dator, virtuella datorn
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 0b05359938f4da544c4cb2a6fe60cfaf228478e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="design-considerations-for-scale-sets"></a>Designöverväganden för Skalningsuppsättningar
Det här avsnittet beskrivs överväganden vid utformning för Skalningsuppsättningar i virtuella datorer. Information om vilka virtuella datorer är avser [översikt över virtuella datorer skala anger](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>När du ska använda skala anger i stället för virtuella datorer?
I allmänhet är skaluppsättningar användbara för att distribuera hög tillgänglighet infrastruktur där en uppsättning datorer har liknande konfiguration. Vissa funktioner är dock endast tillgängliga i skalningsuppsättningar medan andra funktioner är bara tillgängliga i virtuella datorer. Vi borde ta en titt på några av de vanligaste funktionerna som är tillgängliga i skalningsuppsättningar men inte virtuella datorer för att kunna fatta ett välgrundat beslut om när du ska använda de olika teknikerna:

### <a name="scale-set-specific-features"></a>Scale set-specifika funktioner

- När du anger skaluppsättning konfiguration, kan du uppdatera egenskapen ”kapacitet” för att distribuera flera virtuella datorer parallellt. Detta är mycket enklare än att skriva ett skript för att dirigera distribuera många enskilda virtuella datorer parallellt.
- Du kan [använda Azure Autoskala för att skala automatiskt en skalningsuppsättning](./virtual-machine-scale-sets-autoscale-overview.md) men inte enskilda virtuella datorer.
- Du kan [avbildningsåterställning skaluppsättning för virtuella datorer](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm) men [inte enskilda virtuella datorer](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Du kan [overprovision](./virtual-machine-scale-sets-design-overview.md) skaluppsättning för virtuella datorer för ökad tillförlitlighet och snabbare distributionstider. Du kan göra detta med enskilda virtuella datorer om du skriver anpassade kod för att göra detta.
- Du kan ange en [uppgradera princip](./virtual-machine-scale-sets-upgrade-scale-set.md) att göra det lättare att distribuera uppgraderingar över virtuella datorer i en skaluppsättning. Med enskilda virtuella datorer, måste du samordnar uppdateringar själv.

### <a name="vm-specific-features"></a>VM-specifika funktioner

Å andra sidan vissa funktioner är endast tillgängliga i virtuella datorer (minst för närvarande):

- Du kan koppla datadiskar till specifika enskilda virtuella datorer, men bifogade datadiskar konfigureras för alla virtuella datorer i en skaluppsättning.
- Du kan koppla icke-tom datadiskar till enskilda virtuella datorer men inte virtuella datorer i en skaluppsättning.
- Du kan ögonblicksbilder för en enskild VM men inte en virtuell dator i en skaluppsättning.
- Du kan göra en avbildning från en enskild VM men inte från en virtuell dator i en skaluppsättning.
- Du kan migrera en enskild VM från interna diskar till hanterade diskar, men du kan inte göra detta för virtuella datorer i en skaluppsättning.
- Du kan tilldela enskilda VM-nätverkskort IPv6 offentliga IP-adresser men inte för virtuella datorer i en skaluppsättning. Observera att du kan tilldela IPv6 offentliga IP-adresser till belastningsutjämnare framför antingen enskilda virtuella datorer eller skaluppsättning för virtuella datorer.

## <a name="storage"></a>Lagring

### <a name="scale-sets-with-azure-managed-disks"></a>Med Azure hanterade diskar
Skaluppsättningar kan skapas med [Azure hanterade diskar](../virtual-machines/windows/managed-disks-overview.md) i stället för traditionella Azure storage-konton. Hanterade diskar ger följande fördelar:
- Du behöver inte skapa en uppsättning Azure storage-konton för skalan som virtuella datorer.
- Du kan definiera [anslutna datadiskar](virtual-machine-scale-sets-attached-disks.md) för de virtuella datorerna i din skala.
- Skaluppsättningar kan konfigureras för [stöder upp till 1 000 virtuella datorer i en mängd](virtual-machine-scale-sets-placement-groups.md). 

Om du har en befintlig mall kan du också [uppdatera mallen så att den använder hanterade diskar](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Användarhanterat lagring
En skalningsuppsättning som inte är definierad med Azure hanterade diskar är beroende av användarskapade storage-konton för att lagra OS-diskar på de virtuella datorerna i uppsättningen. Förhållandet 20 virtuella datorer per lagringskonto eller mindre rekommenderas att uppnå högsta i/o och också dra nytta av _överetablering_ (se nedan). Det rekommenderas också att du sprids början tecknen i lagringskontonamn alfabetet. Om du gör det hjälper till att sprida belastningen över olika interna system. 


## <a name="overprovisioning"></a>Överetablering
Skalningsuppsättningarna för närvarande standard ”överetablering” virtuella datorer. Med överetablering aktiverat skalan ange faktiskt varv upp flera virtuella datorer än du uppge och tar bort de extra virtuella datorerna när det begärda antalet virtuella datorer har etablerats. Överetablering förbättrar etablering slutförandefrekvenser och minskar distributionstiden för. Du debiteras inte för de extra virtuella datorerna och de räknas inte in i din kvotgränser.

Överetablering förbättra etablering slutförandefrekvenser, kan orsaka förvirrande beteendet för ett program som inte är avsedda att hantera extra VM: ar som visas och sedan försvinner. Om du vill aktivera överetablering av, se till att du har följande sträng i mallen: `"overprovision": "false"`. Mer information finns i den [skala ange REST API-dokumentation](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Om du inaktiverar överetablering din skaluppsättning använder användarhanterat lagring, du kan ha fler än 20 virtuella datorer per lagringskonto, men det rekommenderas inte över 40 på grund av i/o-prestanda. 

## <a name="limits"></a>Begränsningar
En skalningsuppsättning bygger på Marketplace-avbildning (även kallat en plattformsavbildning) och konfigurerat för att använda Azure hanterade diskar stöder en kapacitet på upp till 1 000 virtuella datorer. Om du konfigurerar din skaluppsättningen som stöd för fler än 100 virtuella datorer fungerar på samma sätt (till exempel belastningsutjämning) i alla scenarier. Mer information finns i [arbeta med stora virtuella datorer](virtual-machine-scale-sets-placement-groups.md). 

En skaluppsättningen som är konfigurerad med användarhanterat storage-konton är för närvarande begränsad till 100 virtuella datorer (och rekommenderas för skalans 5 storage-konton).

En skalningsuppsättning som bygger på en anpassad avbildning (en som skapats av du) kan ha en kapacitet på upp till 300 VMs konfigurerades med Azure hanterade diskar. Om skaluppsättning är konfigurerad med användarhanterat storage-konton, måste det skapa alla OS-disk VHD i ett lagringskonto. Därför kan högsta rekommenderade antalet virtuella datorer i en skaluppsättning som bygger på en anpassad avbildning och användarhanterat lagring är 20. Om du inaktiverar överetablering går du upp till 40.

För flera virtuella datorer än dessa gränser tillåter du behöver distribuera flera skaluppsättningar enligt [mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

