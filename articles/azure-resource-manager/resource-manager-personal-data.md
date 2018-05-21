---
title: Azure Resource Manager personuppgifter | Microsoft Docs
description: Lär dig mer om att hantera personliga data som är associerade med Azure Resource Manager-åtgärder.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 71928be07080ed14fdcb93f33ea64d2572955b53
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Hantera personliga data som är associerade med Azure Resource Manager

Ta bort någon personlig information som du har angett i distributioner, resursgrupp eller taggar för att undvika att exponera känslig information. Azure Resource Manager innehåller åtgärder som kan du hantera personliga data som du har angett i distributioner, resursgrupp eller taggar.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Ta bort personliga data i distributionshistoriken

För distributioner behåller Resource Manager parametervärden och statusmeddelanden i distributionshistoriken. Dessa värden sparas tills du tar bort distributionen från historiken. Lista över distributioner för att se om du har angett personliga data i dessa värden. Om du hittar personliga data, kan du ta bort distributioner från historiken.

Listan **distributioner** i tidigare använda:

* [Lista med resursgrupp](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [AZ grupplistan för distribution](/cli/azure/group/deployment#az-group-deployment-list)

Ta bort **distributioner** från tidigare använda:

* [Ta bort](/rest/api/resources/deployments/delete)
* [Ta bort AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [ta bort AZ distribution](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Ta bort personliga data i resursgruppnamn

Namnet på resursgruppen kvar tills du tar bort resursgruppen. Lista över resursgrupper för att se om du har angett personliga data i namn. Om du hittar dina personliga data, [flytta resurserna](resource-group-move-resources.md) till en ny resursgrupp och ta bort resursgruppen med personliga data i namnet.

Listan **resursgrupper**, Använd:

* [lista](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [AZ grupplistan](/cli/azure/group#az-group-list)

Ta bort **resursgrupper**, Använd:

* [Ta bort](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Ta bort personliga data i taggar

Taggar namn och värden sparas tills du tar bort eller ändra taggen. Lista över taggar för att se om du har angett personuppgifter inom taggarna. Ta bort taggar om du hittar personliga data.

Listan **taggar**, Använd:

* [lista](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [AZ tagglista](/cli/azure/tag#az-tag-list)

Ta bort **taggar**, Använd:

* [Ta bort](/rest/api/resources/tags/delete)
* [Ta bort AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [ta bort AZ-tagg](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Nästa steg
* En översikt över Azure Resource Manager finns i [vad är Resource Manager?](resource-group-overview.md)