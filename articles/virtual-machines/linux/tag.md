---
title: Så här taggar du en virtuell Azure Linux-dator
description: Lär dig mer om hur du taggar en virtuell Azure Linux-dator som skapats i Azure med hjälp av distributionsmodellen för Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: fd4a93f4c0b2f052fe5c9890bee01e5da0dcead2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460961"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Så här taggar du en virtuell Linux-dator i Azure
I den här artikeln beskrivs olika sätt att tagga en virtuell Linux-dator i Azure via resurshanterarens distributionsmodell. Taggar är användardefinierade nyckel-/värdepar som kan placeras direkt på en resurs eller en resursgrupp. Azure stöder för närvarande upp till 50 taggar per resurs och resursgrupp. Taggar kan placeras på en resurs när de skapas eller läggs till i en befintlig resurs. Observera att taggar stöds endast för resurser som skapats via Resurshanterarens distributionsmodell.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Taggning med Azure CLI

Till att börja med behöver du den senaste [Azure CLI](/cli/azure/install-azure-cli) installerat och inloggad på ett Azure-konto med [az login](/cli/azure/reference-index#az-login).

Du kan visa alla egenskaper för en viss virtuell dator, inklusive taggarna, med det här kommandot:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Om du vill lägga till en ny VM-tagg via Azure CLI kan du använda `azure vm update` kommandot tillsammans med taggparametern **--set:**

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Om du vill ta bort taggar kan `azure vm update` du använda parametern **--remove** i kommandot.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Nu när vi har tillämpat taggar på våra resurser Azure CLI och portalen, låt oss ta en titt på användningsinformation för att se taggarna i faktureringsportalen.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du taggar dina Azure-resurser finns i [Översikt över Azure Resource Manager][Azure Resource Manager Overview] och Använda taggar för att ordna dina [Azure-resurser][Using Tags to organize your Azure Resources].
* Information om hur taggar kan hjälpa dig att hantera din användning av Azure-resurser finns i [Förstå din Azure-faktura][Understanding your Azure Bill] och [få insikter om din Microsoft Azure-resursförbrukning][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
