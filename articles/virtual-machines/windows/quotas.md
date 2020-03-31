---
title: vCPU-kvoter för Azure
description: Läs mer om vCPU-kvoter för Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893471"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-kvoter för virtuella datorer

VCPU-kvoterna för virtuella datorer och skaluppsättningar för virtuella datorer är ordnade i två nivåer för varje prenumeration i varje region. Den första nivån är den totala regionala virtuella processorer, och den andra nivån är de olika vm-storleksfamiljens kärnor, till exempel virtuella processorer i D-serien. Varje gång en ny virtuell dator distribueras får vCPU:erna för den virtuella datorn inte överskrida vCPU-kvoten för den virtuella datorns storleksfamilj eller den totala regionala vCPU-kvoten. Om någon av dessa kvoter överskrids tillåts inte vm-distributionen. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Informationen om var och en av dessa kvoter kan ses i avsnittet **Användning + kvoter** på sidan **Prenumeration** i [Azure-portalen](https://portal.azure.com)eller så kan du fråga efter värdena med PowerShell.

   
 
## <a name="check-usage"></a>Kontrollera användning

Du kan använda [cmdleten Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) för att kontrollera din kvotanvändning.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Utdata kommer att se ut ungefär så här:

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
Reserverade VM-instanser, som är begränsade till en enda prenumeration utan vm-storleksflexibilitet, lägger till en ny aspekt i vCPU-kvoterna. Dessa värden beskriver antalet instanser av den angivna storleken som måste kunna distribueras i prenumerationen. De fungerar som platshållare i kvotsystemet för att säkerställa att kvoten är reserverad för att säkerställa att reserverade VM-instanser kan distribueras i prenumerationen. Om en viss prenumeration till exempel har 10 Standard_D1 reserverade VM-instanser blir användningsgränsen för Standard_D1 reserverade VM-instanser 10. Detta gör att Azure säkerställer att det alltid finns minst 10 vCPU:er tillgängliga i kvoten Totalt regionalt vCPU som ska användas för Standard_D1 instanser och det finns minst 10 vCPU:er tillgängliga i VCPU-kvoten standard D-familjen som ska användas för Standard_D1 instanser.

Om en kvotökning krävs för att köpa en enda prenumeration RI, kan du [begära en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) på din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
