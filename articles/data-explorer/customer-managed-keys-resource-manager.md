---
title: Konfigurera kundhanterade nycklar i Azure Data Explorer med azure Resource Manager-mallen
description: I den här artikeln beskrivs hur du konfigurerar kryptering med kundhanterade nycklar på dina data i Azure Data Explorer med hjälp av Azure Resource Manager-mallen.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297920"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Konfigurera kundhanterade nycklar med azure Resource Manager-mallen

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-mall](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med kundhanterade nycklar

I det här avsnittet konfigurerar du kundhanterade nycklar med Azure Resource Manager-mallar. Som standard använder Azure Data Explorer-kryptering Microsoft-hanterade nycklar. I det här steget konfigurerar du Azure Data Explorer-klustret så att de använder kundhanterade nycklar och anger nyckeln som ska associeras med klustret.

Du kan distribuera Azure Resource Manager-mallen med hjälp av Azure-portalen eller med PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Uppdatera nyckelversionen

När du skapar en ny version av en nyckel måste du uppdatera klustret för att använda den nya versionen. Ring först `Get-AzKeyVaultKey` för att få den senaste versionen av nyckeln. Uppdatera sedan klustrets nyckelvalvsegenskaper för att använda den nya versionen av nyckeln, vilket visas i [Konfigurera kryptering med kundhanterade nycklar](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Nästa steg

* [Säkra Azure Data Explorer-kluster i Azure](security.md)
* [Konfigurera hanterade identiteter för ditt Azure Data Explorer-kluster](managed-identities.md)
* [Skydda klustret i Azure Data Explorer - Azure-portalen](manage-cluster-security.md) genom att aktivera kryptering i vila.
* [Konfigurera kundhanterade nycklar med C #](customer-managed-keys-csharp.md)

