---
title: Hur du tagga en virtuell Azure Linux-dator | Microsoft Docs
description: Mer information om taggar en Azure Linux-dator som skapats i Azure med hjälp av Resource Manager-distributionsmodellen.
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
ms.openlocfilehash: 103784d97301313379b2fd336624e5cda591c5a6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914172"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Hur du tagga en Linux-dator i Azure
Den här artikeln beskrivs olika sätt att märka en Linux-dator i Azure via Resource Manager-distributionsmodellen. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resursgrupp. Azure stöder för närvarande upp till 15 taggar per resurs och resursgruppen. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller lägga till en befintlig resurs. Observera taggar stöds för resurser som skapats via den Resource Manager distributionsmodellen.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagga med Azure CLI
Ska börja, [installera och konfigurera Azure CLI](../../xplat-cli-azure-resource-manager.md) och kontrollera att du är i läget Resource Manager (`azure config mode arm`).

Du kan visa alla egenskaper för en viss virtuell dator, inklusive taggarna, med det här kommandot:

        azure vm show -g MyResourceGroup -n MyTestVM

Om du vill lägga till en ny VM-tagg med Azure CLI, kan du använda den `azure vm set` kommandot tillsammans med parametern taggen **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Du kan använda för att ta bort alla taggar i **– T** parametern i den `azure vm set` kommando.

        azure vm set – g MyResourceGroup –n MyTestVM -T


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
