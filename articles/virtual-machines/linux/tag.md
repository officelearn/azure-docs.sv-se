---
title: Tagga en virtuell Azure Linux-dator | Microsoft Docs
description: Läs mer om att tagga en Azure Linux-dator som skapats i Azure med hjälp av Resource Manager-distributionsmodellen.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 297d3bc201b4bc9d9db0b0bed7a364769fa72859
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993059"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Tagga en virtuell Linux-dator i Azure
Den här artikeln beskrivs olika sätt att tagga en virtuell Linux-dator i Azure via Resource Manager-distributionsmodellen. Taggar är en användardefinierad nyckel/värde-par som kan placeras direkt på en resurs eller resursgrupp. Azure stöder för närvarande upp till 15 taggar per resurs och resursgruppen. Taggar kan placeras på en resurs när den skapas eller lagts till i en befintlig resurs. Observera att taggar stöds för resurser som har skapats via Resource Manager-distributionsmodellen endast.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagga med Azure CLI

Om du vill börja, du behöver senast [Azure CLI](/cli/azure/install-azure-cli) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index#az-login).

Du kan visa alla egenskaper för en viss virtuell dator, inklusive taggarna, med hjälp av det här kommandot:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Om du vill lägga till en ny VM-tagg via Azure CLI, du kan använda den `azure vm update` kommandot tillsammans med parametern taggen **--ange**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Om du vill ta bort taggar som du kan använda den **– ta bort** parametern i den `azure vm update` kommando.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Nu när vi har använt taggar i våra resurser Azure CLI och -portalen, låt oss ta en titt på de användningsinformation om du vill visa taggarna på faktureringsportal.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om tagga dina Azure-resurser i [översikt över Azure Resource Manager] [ Azure Resource Manager Overview] och [med taggar för att organisera dina Azure-resurser] [ Using Tags to organize your Azure Resources].
* Om du vill se hur taggar kan hjälpa dig att hantera din användning av Azure-resurser, se [förstå din Azure-faktura] [ Understanding your Azure Bill] och [insyn i din Microsoft Azure-resursförbrukning] [Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
