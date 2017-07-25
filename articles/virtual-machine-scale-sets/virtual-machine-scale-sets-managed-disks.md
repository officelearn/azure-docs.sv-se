---
title: "Använda Managed Disks med skalningsuppsättningar för virtuella Azure-datorer | Microsoft Docs"
description: "Lär dig varför och hur du använder hanterade diskar med skalningsuppsättningar för virtuella datorer"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: 8ec9709bfb553f6ee89611e92330dedb6129055d
ms.contentlocale: sv-se
ms.lasthandoff: 07/13/2017

---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Skalningsuppsättningar för virtuella Azure-datorer och hanterade diskar

[Skalningsuppsättningar för virtuella Azure-datorer](/azure/virtual-machine-scale-sets/) stöder nu virtuella datorer med hanterade diskar. Det finns många fördelar med att använda hanterade diskar med skalningsuppsättningar, däribland:

* Du behöver inte längre skapa och hantera lagringskonton för att lagra OS-diskar för de virtuella datorerna med skalningsuppsättningarna.

* Du kan koppla hanterade diskar till skalningsuppsättningen.

* En skalningsuppsättning med en hanterad disk kan ha en kapacitet så hög som 1 000 virtuella datorer om den är baserad på en plattformsavbildning eller 100 virtuella datorer om den är baserad på en anpassad avbildning.

## <a name="get-started"></a>Kom igång

Ett enkelt sätt att komma igång med skalningsuppsättningar med hanterade diskar är att distribuera en från Azure Portal. Mer information finns i [den här artikeln](./virtual-machine-scale-sets-portal-create.md). Ett annat enkelt sätt att komma igång är att använda [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) för att distribuera en skalningsuppsättning. Följande exempel visar hur du skapar en Ubuntu-baserad skalningsuppsättning med 10 virtuella datorer, där var och en har en datadisk på 50 och 100 GB:

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Alternativt kan du titta i [GitHub-databasen för Azure-snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates) och leta efter mappar som innehåller `vmss` för att se fördefinierade exempel på mallar som distribuerar skalningsuppsättningar. Om du vill se vilka mallar som redan använder hanterade diskar kan du referera till [den här listan](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md).

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om hanterade diskar i allmänhet i [den här artikeln](../storage/storage-managed-disks-overview.md).

Se [den här artikeln](./virtual-machine-scale-sets-convert-template-to-md.md) för information om hur du konverterar Resource Manager-mall för att etablera skalningsuppsättningar med hanterade diskar. Ändringarna till Resource Manager-mallar tillämpas även på Azure REST API.

Mer information om hur du använder hanterade datadiskar med skalningsuppsättningar finns i [den här artikeln](./virtual-machine-scale-sets-attached-disks.md).

Referera till [den här artikeln](./virtual-machine-scale-sets-placement-groups.md) för att börja arbeta med stora skalningsuppsättningar.



