---
title: "Azure princip json-exemplet - tillåtna SKU: er för virtuella datorer och storage-konton | Microsoft Docs"
description: "Den här principen för json-exemplet kräver att storage-konton och virtuella datorer använder godkända SKU: er."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: a25f7738c8db335e6bd43c90009bb51bb3a03696
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="allowed-skus-for-storage-accounts-and-virtual-machines"></a>Tillåtna SKU: er för storage-konton och virtuella datorer

Den här principen kräver att storage-konton och virtuella datorer använder godkända SKU: er. Använder inbyggda principer för att säkerställa godkända SKU: er. Anger en matris med godkända virtuella datorer SKU: er och en matris med godkända lagringskonto SKU: er.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

[!code-json[main](../../../policy-templates/samples/PolicyInitiatives/skus-for-mutiple-types/azurepolicyset.json "Allowed SKUs for Storage Accounts and Virtual Machines")]


Du kan distribuera den här mallen med hjälp av den [Azure-portalen](#deploy-with-the-portal) eller med [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

[![Distribuera till Azure](http://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]


````powershell
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-mutiple-types/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-mutiple-types/azurepolicyset.parameters.json"

$policyset= New-AzureRmPolicySetDefinition -Name "skus-for-mutiple-types" -DisplayName "Allowed SKUs for Storage Accounts and Virtual Machines" -Description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzureRmPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -LISTOFALLOWEDSKUS_1 <VM SKUs> -LISTOFALLOWEDSKUS_2 <Storage Account SKUs >  -Sku @{"Name"="A1";"Tier"="Standard"}
````

### <a name="clean-up-powershell-deployment"></a>Rensa PowerShell-distribution

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```



## <a name="next-steps"></a>Nästa steg

- Nya Azure princip mallen exempel finns på [mallar för Azure princip](../json-samples.md).
