---
title: Distribuera Azure-mall med SAS-token och PowerShell | Microsoft Docs
description: Använda Azure Resource Manager och Azure PowerShell för att distribuera resurser till Azure från en mall som är skyddat med SAS-token.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 818aa63ced56d7cf382536f10bb6150199ab74e9
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268948"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Distribuera en privat Resource Manager-mall med SAS-token och Azure PowerShell

När mallen finns i ett lagringskonto, kan du begränsa åtkomsten till mallen och ange en token för delad åtkomst (signatur) under distributionen. Det här avsnittet beskriver hur du använder Azure PowerShell med Resource Manager-mallar för att ange en SAS-token under distributionen. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>Lägg till privat mall till storage-konto

Du kan lägga till dina mallar till ett lagringskonto och en länk till dem under distributionen med en SAS-token.

> [!IMPORTANT]
> Blobben som innehåller mallen är tillgänglig för endast ägare genom att följa stegen nedan. Men när du skapar en SAS-token för bloben är blob tillgängliga för alla med denna URI. Om en annan användare spärras URI: N, kan som användaren komma åt mallen. Med hjälp av en SAS-token är ett bra sätt för att begränsa åtkomsten till dina mallar, men du får inte innehålla känsliga data som lösenord direkt i mallen.
> 
> 

I följande exempel ställer in en privat lagringskontobehållare och överför en mall:
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzStorageContainer -Name templates -Permission Off
Set-AzStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Ange SAS-token under distributionen
Generera en SAS-token för att distribuera en privat mall i ett lagringskonto, och inkludera den i URI: N för mallen. Ange förfallotiden till att det finns tillräckligt med tid att slutföra distributionen.
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Ett exempel på hur du använder en SAS-token med länkade mallar finns i [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Nästa steg
* En introduktion till att distribuera mallar finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy.md).
* En fullständig exempelskript som distribuerar en mall finns i [skript för distribuera Resource Manager-mallar](resource-manager-samples-powershell-deploy.md)
* För att definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
