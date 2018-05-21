---
title: Json-exempel i Azure Policy – Granska transparent datakryptering för SQL Database | Microsoft Docs
description: Den här json-exempelprincipen granskar om transparent datakryptering inte är aktiverat för SQL-databasen.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 04/27/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 5151a4ac930f5f6fb11a9aad7fb2ab872ef04e6c
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="audit-sql-database-encryption"></a>Granska SQL-databaskryptering

Den här inbyggda principen granskar om transparent datakryptering inte är aktiverat för SQL-databasen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Sql/servers/databases"
      },
      {
        "field": "name",
        "notEquals": "master"
      }
    ]
  },
  "then": {
    "effect": "AuditIfNotExists",
    "details": {
      "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
      "name": "current",
      "existenceCondition": {
        "allOf": [
          {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "enabled"
          }
        ]
      }
    }
  }
}
```

Du kan distribuera den här mallen med hjälp av [Azure Portal](#deploy-with-the-portal), med [PowerShell](#deploy-with-powershell) eller med [Azure CLI](#deploy-with-azure-cli). Du hämtar den inbyggda principen med hjälp av ID:t `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

När du tilldelar en princip väljer du **Granska status för transparent datakryptering** från de tillgängliga inbyggda definitionerna.

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzureRmPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Rensa PowerShell-distribution

Ta bort principtilldelningen genom att köra följande kommando.

```powershell
Remove-AzureRmPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Rensa Azure CLI-distributionen

Ta bort principtilldelningen genom att köra följande kommando.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Ytterligare Azure Policy-mallexempel finns i [mallar för Azure Policy](../json-samples.md).
