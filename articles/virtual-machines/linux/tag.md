---
title: Hur du tagga en virtuell Azure Linux-dator | Microsoft Docs
description: "Mer information om taggar en Azure Linux-dator som skapats i Azure med hjälp av Resource Manager-distributionsmodellen."
services: virtual-machines-linux
documentationcenter: 
author: mmccrory
manager: timlt
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
ms.openlocfilehash: e68b97461e8630750462f2c640901511aa2f8676
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Hur du tagga en Linux-dator i Azure
Den här artikeln beskrivs olika sätt att märka en Linux-dator i Azure via Resource Manager-distributionsmodellen. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resursgrupp. Azure stöder för närvarande upp till 15 taggar per resurs och resursgruppen. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller lägga till en befintlig resurs. Observera taggar stöds för resurser som skapats via den Resource Manager distributionsmodellen.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagga med Azure CLI
Om du vill börja, behöver du senast [Azure CLI 2.0 (förhandsgranskning)](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/reference-index#az_login).

Du kan också utföra dessa steg med [Azure CLI 1.0](tag-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan visa alla egenskaper för en viss virtuell dator, inklusive taggarna, med det här kommandot:

        az vm show --resource-group MyResourceGroup --name MyTestVM

Om du vill lägga till en ny VM-tagg med Azure CLI, kan du använda den `azure vm update` kommandot tillsammans med parametern taggen **--ange**:

        az vm update --resource-group MyResourceGroup --name MyTestVM –-set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2

Du kan använda för att ta bort taggar i **--ta bort** parametern i den `azure vm update` kommando.

        az vm update –-resource-group MyResourceGroup –-name MyTestVM --remove tags.myNewTagName1


Nu när vi har använt taggar i våra resurser Azure CLI och -portalen, låt oss ta en titt på användningsinformation för att se taggar i fakturerings-portalen.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om resurserna i Azure-märkning i [översikt över Azure Resource Manager] [ Azure Resource Manager Overview] och [med taggar för att organisera dina Azure-resurser][Using Tags to organize your Azure Resources].
* Om du vill se hur taggar kan hjälpa dig att hantera din användning av Azure-resurser, se [förstå fakturan Azure] [ Understanding your Azure Bill] och [få insikter om dina Microsoft Azure-resursförbrukning][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
