---
title: vCPU kvoter för Azure | Microsoft Docs
description: Läs mer om vCPU kvoter för Azure.
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
ms.openlocfilehash: dffc76151e0739bf56091d987bf21d02b5bfb1e2
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716551"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuella vCPU kvoter

VCPU kvoter för virtuella datorer och virtuella datorer är ordnade i två nivåer för varje prenumeration i varje region. I det andra lagret är olika VM storlek family kärnor, till exempel D-serien vCPUs på första nivån är den totala regionala vCPUs. När en ny virtuell dator är distribuerat i vCPUs för den virtuella datorn inte får överstiga vCPU kvoten för familjen för VM-storlek eller totala regionala vCPU kvoten. Om någon av dessa kvoter överskrids tillåts inte VM-distribution. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Information om var och en av dessa kvoter kan ses i den **användning + kvoter** avsnitt i den **prenumeration** sidan i den [Azure-portalen](https://portal.azure.com), eller fråga efter värden med PowerShell.

 
## <a name="check-usage"></a>Kontrollera användning

Du kan använda den [Get-AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) för att kontrollera din kvotanvändningen.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

Resultatet ser ut ungefär så här:

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
Reserverade VM-instanser som är begränsade till en enda prenumeration, lägger till en ny aspekt vCPU kvoter. Dessa värden beskrivs antal instanser av den angivna storleken som ska distribueras i prenumerationen. De fungerar som platshållare i kvotsystemet så som är reserverat för att säkerställa reserverade instanserna är distribueras i prenumerationen. Om en viss prenumeration har 10 Standard_D1 reserverade kommer instanser av användningsområden begränsa för Standard_D1 reserverade instanser vara 10. Detta gör att Azure för att se till att det finns alltid minst 10 vCPUs i den totala regionala vCPUs kvoten som ska användas för Standard_D1 instanser och det finns minst 10 vCPUs i Standard D-serien vCPU kvot som ska användas för Standard_D1 instanser.

Om en databaskvot krävs för att köpa en enda prenumeration RI, kan du [begära en ökad kvot](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) på din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
