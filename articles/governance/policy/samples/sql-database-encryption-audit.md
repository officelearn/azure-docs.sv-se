---
title: Exempel – granska transparent data kryptering på SQL
description: Den här exempelprincipdefinitionen granskar om transparent datakryptering inte är aktiverat för SQL-databasen.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 60f617445c33de524cf71b0c81514bf93b3592d4
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069722"
---
# <a name="sample---audit-sql-database-encryption"></a>Exempel – Granska SQL-databaskryptering

Den här inbyggda principen granskar om transparent datakryptering inte är aktiverat för SQL-databasen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

Du kan distribuera den här mallen med hjälp av [Azure Portal](#deploy-with-the-portal) eller med [PowerShell](#deploy-with-powershell) eller [Azure CLI](#deploy-with-azure-cli). Du hämtar den inbyggda principen med hjälp av ID:t `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

När du tilldelar en princip väljer du **Granska status för transparent datakryptering** från de tillgängliga inbyggda definitionerna.

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Rensa PowerShell-distribution

Ta bort principtilldelningen genom att köra följande kommando.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Rensa Azure CLI-distributionen

Ta bort principtilldelningen genom att köra följande kommando.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Granska fler exempel på [Azure Policy-exempel](index.md)