---
title: Distribuera Azure-mall med SAS-token och PowerShell | Microsoft Docs
description: "Använd Azure Resource Manager och Azure PowerShell för att distribuera resurser till Azure från en mall som är skyddat med SAS-token."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 1e3cea027b599e2b1af1ced0fdf14e2cc8a0db82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Distribuera privata Resource Manager-mall med SAS-token och Azure PowerShell

När mallen finns i ett lagringskonto kan du begränsa åtkomsten till mallen och erbjuder en token för delad åtkomst-signatur (SAS) under distributionen. Det här avsnittet beskriver hur du använder Azure PowerShell med Resource Manager-mallar för att ange en SAS-token under distributionen. 

## <a name="add-private-template-to-storage-account"></a>Lägg till privata mall i storage-konto

Du kan lägga till dina mallar i ett lagringskonto och en länk till dem under distributionen med en SAS-token.

> [!IMPORTANT]
> Blob som innehåller mallen som är tillgänglig för endast kontoägaren genom att följa stegen nedan. Men när du skapar en SAS-token för blobben är blob tillgänglig för alla med den URI. Om en annan användare spärras URI: N, har som användare tillgång till mallen. Använda en SAS-token är ett bra sätt att begränsa åtkomst till dina mallar bör, men du inte känsliga data som lösenord direkt i mallen.
> 
> 

I följande exempel ställer in en behållare för privat lagring-konto och överför en mall:
   
```powershell
# create a storage account for templates
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Ange SAS-token under distributionen
Generera en SAS-token för att distribuera en privat mallen i ett lagringskonto och inkludera den i URI: N för mallen. Ange förfallotiden så att tillräckligt med tid att slutföra distributionen.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Ett exempel på hur du använder en SAS-token med länkade mallar finns [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Nästa steg
* En introduktion till att distribuera mallar finns [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy.md).
* En fullständig exempelskript som distribuerar en mall finns [skript för distribuera Resource Manager-mallar](resource-manager-samples-powershell-deploy.md)
* Om du vill definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

