---
title: Personuppgifter
description: Lär dig hur du hanterar personuppgifter som är associerade med Azure Resource Manager-åtgärder.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485265"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Hantera personuppgifter som är associerade med Azure Resource Manager

Om du vill undvika att exponera känslig information tar du bort all personlig information som du kan ha angett i distributioner, resursgrupper eller taggar. Azure Resource Manager tillhandahåller åtgärder som gör att du kan hantera personuppgifter som du kan ha angett i distributioner, resursgrupper eller taggar.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Ta bort personuppgifter i distributionshistoriken

För distributioner behåller Resource Manager parametervärden och statusmeddelanden i distributionshistoriken. Dessa värden kvarstår tills du tar bort distributionen från historiken. Om du vill se om du har angett personuppgifter i dessa värden listar du distributionerna. Om du hittar personuppgifter tar du bort distributionerna från historiken.

Om du vill visa **distributioner** i historiken använder du:

* [Lista efter resursgrupp](/rest/api/resources/deployments/listbyresourcegroup)
* [Ta bort-AzResourceGroup-utläggning](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [distributionslista för az-gruppen](/cli/azure/group/deployment#az-group-deployment-list)

Om du vill ta bort **distributioner** från historiken använder du:

* [Ta bort](/rest/api/resources/deployments/delete)
* [Ta bort-AzResourceGroup-deldistribution](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az-gruppdistribution ta bort](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Ta bort personuppgifter i resursgruppsnamn

Namnet på resursgruppen sparas tills du tar bort resursgruppen. Om du vill se om du har angett personuppgifter i namnen listar du resursgrupperna. Om du hittar personuppgifter [flyttar du resurserna](move-resource-group-and-subscription.md) till en ny resursgrupp och tar bort resursgruppen med personuppgifter i namnet.

Om du vill visa en lista över **resursgrupper**använder du:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az grupplista](/cli/azure/group#az-group-list)

Om du vill ta bort **resursgrupper**använder du:

* [Ta bort](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Ta bort personuppgifter i taggar

Taggar namn och värden kvarstår tills du tar bort eller ändrar taggen. Om du vill se om du har lämnat personuppgifter i taggarna listar du taggarna. Om du hittar personuppgifter tar du bort taggarna.

Om du vill visa **taggar**använder du:

* [Lista](/rest/api/resources/tags/list)
* [Hämta AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tagglista](/cli/azure/tag#az-tag-list)

Om du vill ta bort **taggar**använder du:

* [Ta bort](/rest/api/resources/tags/delete)
* [Ta bort-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az-taggen tar bort](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Nästa steg
* En översikt över Azure Resource Manager finns i [Vad är Resurshanteraren?](overview.md)