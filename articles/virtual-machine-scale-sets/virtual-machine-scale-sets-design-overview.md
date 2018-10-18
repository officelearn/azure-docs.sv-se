---
title: Designöverväganden för Azure Virtual Machine Scale Sets | Microsoft Docs
description: Lär dig mer om designaspekter för ditt Azure Virtual Machine Scale Sets
keywords: skalningsuppsättningar för virtuell Linux-dator, virtuella datorer
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
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
ms.openlocfilehash: e03016b80b0a7043a72e55b6c8b68b67b55283b1
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388134"
---
# <a name="design-considerations-for-scale-sets"></a>Designöverväganden för Skalningsuppsättningar
Den här artikeln beskriver designöverväganden för Skalningsuppsättningar för virtuella datorer. Information om Virtual Machine Scale Sets finns i [översikt över Skaluppsättningar för virtuell dator](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>När du ska använda skala anger i stället för virtuella datorer?
I allmänhet är skalningsuppsättningar användbara för att distribuera infrastruktur med hög tillgänglighet där en uppsättning datorer har liknande konfiguration. Vissa funktioner är dock endast tillgängliga i skalningsuppsättningar medan andra funktioner är tillgängliga i virtuella datorer. För att kunna fatta ett välgrundat beslut om när du ska använda de olika teknikerna, bör du först ta en titt på några av de vanligaste funktionerna som är tillgängliga i skalningsuppsättningar men inte virtuella datorer:

### <a name="scale-set-specific-features"></a>Scale set-specifika funktioner

- När du anger skalningsuppsättningen konfiguration kan du uppdatera den *kapacitet* egenskapen att distribuera flera virtuella datorer samtidigt. Den här processen är bättre än att skriva ett skript för att dirigera distribuera många enskilda virtuella datorer samtidigt.
- Du kan [använder Azure Autoscale för att automatiskt skala en skalningsuppsättning](./virtual-machine-scale-sets-autoscale-overview.md) men inte för enskilda virtuella datorer.
- Du kan [återställa avbildningen av de virtuella datorerna med skalningsuppsättningarna](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) men [inte enskilda virtuella datorer](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Du kan [overprovision](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) skalningsuppsättningar i virtuella datorer för ökad tillförlitlighet och svarstider för snabbare distribution. Du kan därför överetablerar enskilda virtuella datorer, såvida inte du skriva anpassad kod för att utföra den här åtgärden.
- Du kan ange en [versionsuppgraderingsprincipen](./virtual-machine-scale-sets-upgrade-scale-set.md) att göra det lättare att distribuera uppgraderingar för virtuella datorer i din skalningsuppsättning. Med enskilda virtuella datorer, måste du dirigera uppdateringar själv.

### <a name="vm-specific-features"></a>VM-specifika funktioner

Vissa funktioner är för närvarande endast tillgängliga i virtuella datorer:

- Du kan göra en avbildning från en enskild virtuell dator, men inte från en virtuell dator i en skalningsuppsättning.
- Du kan migrera en enskild virtuell dator från interna diskar till hanterade diskar, men du kan inte migrera VM-instanser i en skalningsuppsättning.
- Du kan tilldela enskilda VM virtuella nätverkskort (NIC) IPv6 offentliga IP-adresser, men det går inte att göra det för VM-instanser i en skalningsuppsättning. Du kan tilldela IPv6 offentliga IP-adresser till belastningsutjämnare framför antingen enskilda virtuella datorer eller virtuella datorerna med skalningsuppsättningarna.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Skalningsuppsättningar med Azure Managed Disks
Skalningsuppsättningar kan skapas med [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) i stället för traditionella Azure storage-konton. Hanterade diskar ger följande fördelar:
- Du behöver inte skapa en uppsättning Azure storage-konton i förväg för de virtuella datorerna med skalningsuppsättningarna.
- Du kan definiera [anslutna datadiskar](virtual-machine-scale-sets-attached-disks.md) för de virtuella datorerna i din skalningsuppsättning.
- Skalningsuppsättningar kan konfigureras att [stöder upp till 1 000 virtuella datorer i en mängd](virtual-machine-scale-sets-placement-groups.md). 

Om du har en befintlig mall, kan du också [uppdatera mallen för att använda Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Användarhanterade lagring
En skalningsuppsättning som inte har definierats med Azure Managed Disks är beroende av användarskapade lagringskonton för att lagra OS-diskar av de virtuella datorerna i uppsättningen. Ett förhållande på 20 virtuella datorer per lagringskonto eller mindre rekommenderas att uppnå högsta i/o och även dra nytta av _överetablering_ (se nedan). Vi rekommenderar också att du sprida de inledande tecknen i storage-kontonamn över alfabetet. Om du gör det hjälper till att sprida belastningen över olika interna system. 


## <a name="overprovisioning"></a>Överetablering
Skalningsuppsättningar har för närvarande ”överetablering” virtuella datorer som standard. Med överetablering aktiverat, skalan ange faktiskt varv upp flera virtuella datorer än du tillfrågad om och sedan tar bort de extra virtuella datorerna när det begärda antalet virtuella datorer har etablerats. Överetablering förbättrar etablering Slutförandefrekvens och minskar distributionstiden för. Du debiteras inte för de extra virtuella datorerna och de räknas inte in i din kvotgräns för.

Medan överetablering förbättrar etablering framgångsfrekvens, kan det orsaka förvirrande beteendet för ett program som inte är avsedda att hantera extra virtuella datorer som visas och sedan försvinner. Om du vill aktivera överetablering av, se till att du har följande sträng i mallen: `"overprovision": "false"`. Mer information finns i den [skala ange REST API-dokumentation](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Om din skalningsuppsättning använder användarhanterade lagring och du väljer att inaktivera överetablering, du kan ha fler än 20 virtuella datorer per lagringskonto, men det rekommenderas inte att gå över 40 på grund av i/o-prestanda. 

## <a name="limits"></a>Begränsningar
En skalningsuppsättning som bygger på en Marketplace-avbildning (kallas även en plattformsavbildning) och konfigurerad för att använda Azure Managed Disks har stöd för en kapacitet på upp till 1 000 virtuella datorer. Om du konfigurerar din skalningsuppsättning till stöd för fler än 100 virtuella datorer fungerar på samma sätt (till exempel Utjämning av nätverksbelastning) i alla scenarier. Mer information finns i [arbeta med stora VM-skalningsuppsättningar](virtual-machine-scale-sets-placement-groups.md). 

En skalningsuppsättning som är konfigurerade med användarhanterade storage-konton är för närvarande begränsad till 100 virtuella datorer (och rekommenderas för skalans 5 storage-konton).

En skalningsuppsättning som bygger på en anpassad avbildning (en som skapats av dig) kan ha en kapacitet på upp till 300 virtuella datorer när den konfigurerats med Azure Managed disks. Om skalningsuppsättningen är konfigurerad med användarhanterade storage-konton, måste programmet skapa alla OS-disk VHD i ett lagringskonto. Därför kan högsta rekommenderade antalet virtuella datorer i en skalningsuppsättning som bygger på en anpassad avbildning och användarhanterade lagring är 20. Om du inaktiverar överetablering kan du gå upp till 40.

För virtuella datorer i mer än de här gränserna tillåter kan du behöva distribuera flera skalningsuppsättningar enligt [den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

