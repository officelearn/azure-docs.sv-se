---
title: Konfigurera Kundhanterade nycklar i Azure Datautforskaren med hjälp av Azure Resource Manager-mallen
description: Den här artikeln beskriver hur du konfigurerar kryptering av Kundhanterade nycklar på dina data i Azure Datautforskaren med hjälp av Azure Resource Manager-mallen.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: bff8c50cdece803e030c0975a9b14ac5739baaf7
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725811"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Konfigurera Kundhanterade nycklar med hjälp av Azure Resource Manager mall

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Azure Resource Manager-mall](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

I det här avsnittet konfigurerar du Kundhanterade nycklar med hjälp av Azure Resource Manager mallar. Som standard använder Azure Datautforskaren-kryptering Microsoft-hanterade nycklar. I det här steget konfigurerar du Azure Datautforskaren-klustret för att använda Kundhanterade nycklar och anger nyckeln som ska associeras med klustret.

Du kan distribuera Azure Resource Manager-mallen med hjälp av Azure Portal eller med hjälp av PowerShell.

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

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du skapar en ny version av en nyckel måste du uppdatera klustret för att använda den nya versionen. Kontakta först `Get-AzKeyVaultKey` för att hämta den senaste versionen av nyckeln. Uppdatera sedan klustrets nyckel valvs egenskaper så att de använder den nya versionen av nyckeln, som visas i [Konfigurera kryptering med Kundhanterade nycklar](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Datautforskaren kluster i Azure](security.md)
* [Konfigurera hanterade identiteter för ditt Azure Datautforskaren-kluster](managed-identities.md)
* [Skydda ditt kluster i Azure datautforskaren-Azure Portal](manage-cluster-security.md) genom att aktivera kryptering i vila.
* [Konfigurera Kundhanterade nycklar medC#](customer-managed-keys-csharp.md)

