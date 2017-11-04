---
title: "Azure princip json-exemplet - NSG x på varje nätverkskort | Microsoft Docs"
description: "Den här principen för json-exemplet kräver att en specifik nätverkssäkerhetsgrupp används med alla virtuella nätverksgränssnittet."
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
ms.openlocfilehash: ffd1e7c393cd27976718880099e11c6bc25a13ec
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="nsg-x-on-every-nic"></a>NSG x på varje nätverkskort

Den här principen kräver att en specifik nätverkssäkerhetsgrupp används med alla virtuella nätverksgränssnitt. Du kan ange ID för nätverkssäkerhetsgruppen ska användas.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

[!code-json[main](../../../policy-templates/samples/Network/enforce-nsg-on-nic/azurepolicy.json "NSG X on every nic")]


Du kan distribuera den här mallen med hjälp av den [Azure-portalen](#deploy-with-the-portal), med [PowerShell](#deploy-with-powershell) eller med den [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

[![Distribuera till Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

````powershell
$definition = New-AzureRmPolicyDefinition -Name "enforce-nsg-on-nic" -DisplayName "NSG X on every nic" -description "This policy enforces a specific NSG on every virtual network interface" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
````

### <a name="clean-up-powershell-deployment"></a>Rensa PowerShell-distribution

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]


````cli

az policy definition create --name 'enforce-nsg-on-nic' --display-name 'NSG X on every nic' --description 'This policy enforces a specific NSG on every virtual network interface' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-nsg-on-nic"

````

### <a name="clean-up-azure-cli-deployment"></a>Rensa Azure CLI-distribution

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Nya Azure princip mallen exempel finns på [mallar för Azure princip](../json-samples.md).
