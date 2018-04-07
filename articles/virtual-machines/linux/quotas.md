---
title: vCPU kvoter för Azure | Microsoft Docs
description: Läs mer om vCPU kvoter för Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a4e0bbe1c6d9b121dfb422934cdd67ff19f80482
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuella vCPU kvoter

VCPU kvoter för virtuella datorer och virtuella datorer är ordnade i två nivåer för varje prenumeration i varje region. Den första nivån är den totala regionala vCPUs och i det andra lagret är olika VM storlek family kärnor, till exempel Standard D-serien vCPUs. När en ny virtuell dator är distribuerat i vCPUs för den nyligen distribuerade virtuella datorn inte får överstiga vCPU kvoten för familjen för specifika VM-storlek eller totala regionala vCPU kvoten. Om någon av dessa kvoter överskrids sedan tillåts VM-distribution inte. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Information om var och en av dessa kvoter kan ses i den **användning + kvoter** avsnitt i den **prenumeration** sidan i den [Azure-portalen](https://portal.azure.com), eller du kan fråga efter värden med hjälp av Azure CLI .


## <a name="check-usage"></a>Kontrollera användning

Du kan kontrollera din kvot användning med [az vm-användning av](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Reserverade VM-instanser
Reserverade VM-instanser som är begränsade till en enda prenumeration, lägger till en ny aspekt vCPU kvoter. Dessa värden beskrivs antal instanser av den angivna storleken som ska distribueras i prenumerationen. De fungerar som platshållare i kvotsystemet så som är reserverat för att säkerställa reserverade instanserna är distribueras i prenumerationen. Om en viss prenumeration har 10 Standard_D1 reserverade kommer instanser av användningsområden begränsa för Standard_D1 reserverade instanser vara 10. Detta gör att Azure för att se till att det finns alltid minst 10 vCPUs i den totala regionala vCPUs kvoten som ska användas för Standard_D1 instanser och det finns minst 10 vCPUs i Standard D-serien vCPU kvot som ska användas för Standard_D1 instanser.

Om en databaskvot krävs för att köpa en enda prenumeration RI, kan du [begära en ökad kvot](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) på din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
