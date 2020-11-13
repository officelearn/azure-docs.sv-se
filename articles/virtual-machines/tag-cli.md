---
title: Tagga en virtuell Azure-dator med hjälp av CLI
description: Lär dig mer om att tagga en virtuell dator med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595070"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Tagga en virtuell dator med hjälp av CLI

Den här artikeln beskriver hur du taggar en virtuell dator med hjälp av Azure CLI. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resurs grupp. Azure har för närvarande stöd för upp till 50 Taggar per resurs och resurs grupp. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller läggs till i en befintlig resurs. Du kan också tagga en virtuell dator med hjälp av Azure [PowerShell](tag-powershell.md).


Du kan visa alla egenskaper för en specifik virtuell dator, inklusive taggar, med hjälp av `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Om du vill lägga till en ny virtuell dator med hjälp av Azure CLI kan du använda `azure vm update` kommandot tillsammans med tag-parametern `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Om du vill ta bort taggar kan du använda- `--remove` parametern i `azure vm update` kommandot.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Nu när vi har använt taggar till våra resurser Azure CLI och portalen, tar vi en titt på användnings informationen för att se taggarna i fakturerings portalen.


**Nästa steg**

- Läs mer om att tagga dina Azure-resurser i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md) och [använda taggar för att ordna dina Azure-resurser](../azure-resource-manager/management/tag-resources.md).
- För att se hur taggar kan hjälpa dig att hantera din användning av Azure-resurser, se [förstå din Azure-faktura](../cost-management-billing/understand/review-individual-bill.md) och [få insikter om din Microsoft Azure resursförbrukning](../cost-management-billing/manage/usage-rate-card-overview.md).
