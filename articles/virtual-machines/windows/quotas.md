---
title: vCPU-kvoter för Azure | Microsoft Docs
description: Läs mer om vCPU-kvoter för Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 92fee850b6ba013c759d3441219f1946a0faedb3
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984157"
---
# <a name="virtual-machine-vcpu-quotas"></a>VM vCPU-kvoter

VCPU-kvoter för virtuella datorer och VM-skalningsuppsättningar är ordnade i två nivåer för varje prenumeration i varje region. Den första nivån är det totalt antal regionala virtuella processorer och det andra lagret är de olika virtuella datorns storlek family kärnorna, till exempel vcpu: er för D-serien. Varje gång en ny virtuell dator är distribuerat den virtuella processorer för den virtuella datorn inte får överstiga vCPU-kvoten för VM-storleksfamilj eller totalt antal regionala vCPU-kvoten. Om något av dessa kvoter överskrids får inte VM-distributionen. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Information om vart och ett av dessa kvoter kan ses i den **användning + kvoter** delen av den **prenumeration** sidan i den [Azure-portalen](https://portal.azure.com), eller du kan fråga efter värden med hjälp av PowerShell.

 [!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)] 
 
## <a name="check-usage"></a>Kontrollera användning

Du kan använda den [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) cmdlet för att kontrollera din för kvotanvändning.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Utdata ska se ut ungefär så här:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Reserverade VM-instanser
Reserverade VM-instanser som är begränsade till en enda prenumeration utan flexibilitet för VM-storlek, lägger till en ny aspekt vCPU-kvoter. Dessa värden beskrivs hur många instanser av den angivna storleken som måste kunna distribueras i prenumerationen. De fungerar som platshållare i kvotsystemet för att se till att kvoten är reserverat för att säkerställa reserverade VM-instanser kan distribueras i prenumerationen. Exempel: om en viss prenumeration har 10 Standard_D1 reserverade VM-instanser användningar gränsen för Standard_D1 reserverad VM-instanser är 10. Detta innebär att Azure för att se till att det finns alltid minst 10 virtuella processorer i totalt antal regionala kvoten för virtuella processorer som ska användas för Standard_D1 instanser och minst 10 virtuella processorer är tillgängliga i Standard D-serien vCPU-kvoten som ska användas för Standard_D1 instanser.

Om en utökad kvot måste köpa en enda prenumeration RI så kan du [öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) på din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
