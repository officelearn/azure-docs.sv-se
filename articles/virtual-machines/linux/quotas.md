---
title: vCPU-kvoter
description: Lär dig mer om vCPU kvoter för Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 4f8d181194328725c2e7c02f6e73752cf568ef4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759180"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-kvoter för virtuella datorer

VCPU kvoter för virtuella datorer och skalnings uppsättningar för virtuella datorer ordnas på två nivåer för varje prenumeration i varje region. Den första nivån är den totala regionala virtuella processorer och den andra nivån är de olika virtuella datorerna i VM-serien, till exempel D-seriens virtuella processorer. När en ny virtuell dator distribueras får inte virtuella processorer för den virtuella datorn överskrida vCPU-kvoten för den virtuella datorns storleks familj eller den totala regionala vCPU-kvoten. Om någon av dessa kvoter överskrids, kommer distributionen av virtuella datorer inte att tillåtas. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Informationen om var och en av dessa kvoter visas i avsnittet **användning och kvoter** på **prenumerations** sidan i [Azure Portal](https://portal.azure.com), eller så kan du fråga efter värden med hjälp av Azure CLI.


## <a name="check-usage"></a>Kontrollera användning

Du kan kontrol lera kvot användningen med [AZ VM List-Usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Utdata bör se ut ungefär så här:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Reserverade VM-instanser
Reserverade VM-instanser, som är begränsade till en enda prenumeration utan flexibilitet för virtuella datorer, lägger till en ny aspekt i vCPU-kvoterna. Värdena beskriver antalet instanser av den angivna storleken som måste distribueras i prenumerationen. De fungerar som en plats hållare i kvot systemet för att säkerställa att kvoten är reserverad för att säkerställa att Azure-reservationer kan distribueras i prenumerationen. Om till exempel en enskild prenumeration har 10 Standard_D1 reservationer, är användnings gränsen för Standard_D1-reservationer 10. Detta gör att Azure ser till att det alltid finns minst 10 virtuella processorer tillgängligt i den totala regionala virtuella processorer-kvoten som ska användas för Standard_D1 instanser och det finns minst 10 virtuella processorer i standard kvoten på D-serien som ska användas för Standard_D1-instanser.

Om en kvot ökning krävs för att antingen köpa en enskild prenumeration i RI, kan du [begära en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns i Azure- [prenumerationer, tjänst gränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
