---
title: vCPU-kvoter för Azure | Microsoft Docs
description: Läs mer om vCPU-kvoter för Azure.
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
ms.openlocfilehash: 56ee63e15c429c5a6212be36d420ae59afa48546
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629097"
---
# <a name="virtual-machine-vcpu-quotas"></a>VM vCPU-kvoter

VCPU-kvoter för virtuella datorer och VM-skalningsuppsättningar är ordnade i två nivåer för varje prenumeration i varje region. Den första nivån är det totalt antal regionala virtuella processorer och det andra lagret är de olika virtuella datorns storlek family kärnorna, till exempel vcpu: er för D-serien. Varje gång en ny virtuell dator är distribuerat den virtuella processorer för den virtuella datorn inte får överstiga vCPU-kvoten för VM-storleksfamilj eller totalt antal regionala vCPU-kvoten. Om något av dessa kvoter överskrids får inte VM-distributionen. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Information om vart och ett av dessa kvoter kan ses i den **användning + kvoter** delen av den **prenumeration** sidan i den [Azure-portalen](https://portal.azure.com), eller du kan fråga efter värden med hjälp av Azure CLI.


## <a name="check-usage"></a>Kontrollera användning

Du kan kontrollera din kvot för användning med hjälp av [az vm list-usage](/cli/azure/vm#az_vm_list_usage).

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
Reserverade VM-instanser som är begränsade till en enda prenumeration, lägger till en ny aspekt vCPU-kvoter. Dessa värden beskrivs hur många instanser av den angivna storleken som måste kunna distribueras i prenumerationen. De fungerar som platshållare i kvotsystemet för att se till att kvoten är reserverat för att säkerställa att Azure reservationer kan distribueras i prenumerationen. Till exempel om en viss prenumeration har 10 Standard_D1 reservationer debiteras användningar gränsen för Standard_D1 reservationer 10. Detta innebär att Azure för att se till att det finns alltid minst 10 virtuella processorer i totalt antal regionala kvoten för virtuella processorer som ska användas för Standard_D1 instanser och minst 10 virtuella processorer är tillgängliga i Standard D-serien vCPU-kvoten som ska användas för Standard_D1 instanser.

Om en utökad kvot krävs för att köpa en enskild prenumeration RI, kan du [öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) på din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
