---
title: vCPU-kvoter
description: Läs mer om vCPU-kvoter för Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 4f8d181194328725c2e7c02f6e73752cf568ef4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759180"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-kvoter för virtuella datorer

VCPU-kvoterna för virtuella datorer och skaluppsättningar för virtuella datorer är ordnade i två nivåer för varje prenumeration i varje region. Den första nivån är den totala regionala virtuella processorer, och den andra nivån är de olika vm-storleksfamiljens kärnor, till exempel virtuella processorer i D-serien. Varje gång en ny virtuell dator distribueras får vCPU:erna för den virtuella datorn inte överskrida vCPU-kvoten för den virtuella datorns storleksfamilj eller den totala regionala vCPU-kvoten. Om någon av dessa kvoter överskrids tillåts inte vm-distributionen. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Informationen om var och en av dessa kvoter kan ses i avsnittet **Användning + kvoter** på sidan **Prenumeration** i [Azure-portalen](https://portal.azure.com)eller så kan du fråga efter värdena med hjälp av Azure CLI.


## <a name="check-usage"></a>Kontrollera användning

Du kan kontrollera din kvotanvändning med [az vm-listanvändning](/cli/azure/vm).

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
Reserverade VM-instanser, som är begränsade till en enda prenumeration utan vm-storleksflexibilitet, lägger till en ny aspekt i vCPU-kvoterna. Dessa värden beskriver antalet instanser av den angivna storleken som måste kunna distribueras i prenumerationen. De fungerar som platshållare i kvotsystemet för att säkerställa att kvoten är reserverad för att säkerställa att Azure-reservationer kan distribueras i prenumerationen. Om en viss prenumeration till exempel har 10 Standard_D1 reservationer kommer användningsgränsen för Standard_D1 reservationer att vara 10. Detta gör att Azure säkerställer att det alltid finns minst 10 vCPU:er tillgängliga i kvoten Totalt regionalt vCPU som ska användas för Standard_D1 instanser och det finns minst 10 vCPU:er tillgängliga i VCPU-kvoten standard D-familjen som ska användas för Standard_D1 instanser.

Om en kvotökning krävs för att antingen köpa en enda prenumeration RI, kan du [begära en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) på din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
