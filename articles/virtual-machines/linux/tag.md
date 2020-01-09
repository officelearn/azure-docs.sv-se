---
title: Tagga en virtuell Azure Linux-dator
description: Lär dig mer om att tagga en virtuell Azure Linux-dator som skapats i Azure med hjälp av distributions modellen för Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 871159a660d03469b67461f5eb1ce8245d4c3611
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369158"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Tagga en virtuell Linux-dator i Azure
I den här artikeln beskrivs olika sätt att tagga en virtuell Linux-dator i Azure via distributions modellen för Resource Manager. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resurs grupp. Azure stöder för närvarande 15 Taggar per resurs och resurs grupp. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller läggs till i en befintlig resurs. Observera att Taggar endast stöds för resurser som skapats via distributions modellen för Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagga med Azure CLI

Innan du börjar måste du ha det senaste [Azure CLI](/cli/azure/install-azure-cli) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index#az-login).

Du kan visa alla egenskaper för en specifik virtuell dator, inklusive taggarna, med hjälp av det här kommandot:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Om du vill lägga till en ny virtuell dator med hjälp av Azure CLI kan du använda kommandot `azure vm update` tillsammans med tag **-parametern--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Om du vill ta bort taggar kan du använda parametern **--Remove** i kommandot `azure vm update`.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Nu när vi har använt taggar till våra resurser Azure CLI och portalen, tar vi en titt på användnings informationen för att se taggarna i fakturerings portalen.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om att tagga dina Azure-resurser i [Azure Resource Manager översikt][Azure Resource Manager Overview] och [använda taggar för att ordna dina Azure-resurser][Using Tags to organize your Azure Resources].
* För att se hur taggar kan hjälpa dig att hantera din användning av Azure-resurser, se [förstå din Azure-faktura][Understanding your Azure Bill] och [få insikter om din Microsoft Azure resursförbrukning][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
