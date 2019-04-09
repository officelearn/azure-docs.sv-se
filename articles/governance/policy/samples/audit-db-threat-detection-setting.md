---
title: Exempel – Granska inställningar för hotidentifiering på DB-nivå
description: Den här exempelprincipdefinitionen granskar principer för SQL-databassäkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 8fbe687a08e36cfa9503078cbcb75ee85f43b7a1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266230"
---
# <a name="sample---audit-db-level-threat-detection-setting"></a>Exempel – Granska inställningar för hotidentifiering på DB-nivå

Den här principen granskar principer för SQL-databassäkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-threat-detection/azurepolicy.json "Audit DB level threat detection setting")]

Du kan distribuera den här mallen med hjälp av [Azure Portal](#deploy-with-the-portal) eller med [PowerShell](#deploy-with-powershell) eller [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

[![Deploy princip exemplet till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Faudit-sql-db-threat-detection%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-sql-db-threat-detection" -DisplayName "Audit DB level threat detection setting" -description "Audit threat detection setting for SQL databases" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-db-threat-detection/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-db-threat-detection/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -setting <Threat Detection Setting> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Rensa PowerShell-distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-sql-db-threat-detection' --display-name 'Audit DB level threat detection setting' --description 'Audit threat detection setting for SQL databases' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-db-threat-detection/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-db-threat-detection/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-sql-db-threat-detection"
```

### <a name="clean-up-azure-cli-deployment"></a>Rensa Azure CLI-distributionen

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Granska fler exempel på [Azure-principexempel](index.md)