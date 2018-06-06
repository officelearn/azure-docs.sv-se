---
title: vCPU kvoter för Azure | Microsoft Docs
description: Läs mer om vCPU kvoter för Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: a880ee18bb13b2cd8471cc58157469555397b872
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716524"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuella vCPU kvoter

VCPU kvoter för virtuella datorer och virtuella datorer är ordnade i två nivåer för varje prenumeration i varje region. I det andra lagret är olika VM storlek family kärnor, till exempel D-serien vCPUs på första nivån är den totala regionala vCPUs. När en ny virtuell dator är distribuerat i vCPUs för den virtuella datorn inte får överstiga vCPU kvoten för familjen för VM-storlek eller totala regionala vCPU kvoten. Om någon av dessa kvoter överskrids tillåts inte VM-distribution. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Information om var och en av dessa kvoter kan ses i den **användning + kvoter** avsnitt i den **prenumeration** sidan i den [Azure-portalen](https://portal.azure.com), eller du kan fråga efter värden med hjälp av Azure CLI.


## <a name="check-usage"></a>Kontrollera användning

Du kan kontrollera din kvot användning med [az vm-användning av](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Resultatet bör se ut ungefär så här:


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
Reserverade VM-instanser som är begränsade till en enda prenumeration, lägger till en ny aspekt vCPU kvoter. Dessa värden beskrivs antal instanser av den angivna storleken som ska distribueras i prenumerationen. De fungerar som platshållare i kvotsystemet så som är reserverat för att säkerställa reserverade instanserna är distribueras i prenumerationen. Om en viss prenumeration har 10 Standard_D1 reserverade kommer instanser av användningsområden begränsa för Standard_D1 reserverade instanser vara 10. Detta gör att Azure för att se till att det finns alltid minst 10 vCPUs i den totala regionala vCPUs kvoten som ska användas för Standard_D1 instanser och det finns minst 10 vCPUs i Standard D-serien vCPU kvot som ska användas för Standard_D1 instanser.

Om en databaskvot krävs för att köpa en enda prenumeration RI, kan du [begära en ökad kvot](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) på din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
