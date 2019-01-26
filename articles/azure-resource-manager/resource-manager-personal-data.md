---
title: Azure Resource Manager-personuppgifter | Microsoft Docs
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
ms.openlocfilehash: 3948bf08f0309072de57f70d0c7c2e539b4fc28b
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075744"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Hantera personliga data som är associerade med Azure Resource Manager

Ta bort någon personlig information som du har angett i distributioner, resursgrupper eller taggar för att undvika exponera känslig information. Azure Resource Manager tillhandahåller åtgärder som låter dig hantera personliga data som du har angett i distributioner, resursgrupper eller taggar.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Ta bort personliga data i distributionshistoriken

För distributioner behåller Resource Manager parametervärden och statusmeddelanden i distributionshistoriken. Dessa värden sparas tills du tar bort distributionen från historiken. Om du har angett personliga data i de här värdena visas en lista över distributioner. Om du hittar personuppgifter kan du ta bort distributioner från historiken.

Listan **distributioner** i historiken, Använd:

* [Listan efter resursgrupp](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [distribution av AZ grupplistan](/cli/azure/group/deployment#az-group-deployment-list)

Att ta bort **distributioner** från historiken, använder du:

* [Ta bort](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [ta bort AZ-gruppdistribution](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Ta bort personliga data i resursgruppnamn

Namnet på resursgruppen håller kvar tills du tar bort resursgruppen. Lista över resursgrupper för att se om du har angett personliga data i namnen. Om du hittar personuppgifter, [flytta resurserna](resource-group-move-resources.md) till en ny resursgrupp och ta bort resursgruppen med personliga data i namnet.

Listan **resursgrupper**, Använd:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [AZ grupplistan](/cli/azure/group#az-group-list)

Att ta bort **resursgrupper**, Använd:

* [Ta bort](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Ta bort personliga data i taggar

Taggar namn och värden kvar tills du tar bort eller ändra taggen. Om du vill se om du har angett personliga data i taggar, en lista över taggar. Ta bort taggar om du hittar personuppgifter.

Listan **taggar**, Använd:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.tags/get-aztag)
* [AZ tag list](/cli/azure/tag#az-tag-list)

Att ta bort **taggar**, Använd:

* [Ta bort](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.tags/remove-aztag)
* [AZ tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Nästa steg
* En översikt över Azure Resource Manager finns i den [vad är Resource Manager?](resource-group-overview.md)