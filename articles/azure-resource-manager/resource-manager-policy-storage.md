---
title: "Principer för Azure-resurs för lagringskonton | Microsoft Docs"
description: "Beskriver principer för Azure Resource Manager för att hantera distributionen av storage-konton."
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
ms.date: 07/05/2017
ms.author: tomfitz
ms.openlocfilehash: 6612ee61f5c50e743241b92030660cea7ae7094d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-to-storage-accounts"></a>Använda resursprinciper för storage-konton
Det här avsnittet beskrivs flera [resursprinciper](resource-manager-policy.md) du kan använda Azure storage-konton. Dessa principer säkerställa konsekvens för storage-konton som har distribuerats i organisationen. 

## <a name="define-permitted-storage-account-types"></a>Definiera tillåtna lagringskontotyper

Följande princip begränsar som [lagringskontotyper](../storage/common/storage-redundancy.md) kan distribueras:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

En liknande regel med en parameter för att acceptera tillåtna SKU: er är tillgängliga som en inbyggd principdefinition. Den inbyggda principen har resurs-ID för `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1`. 

## <a name="define-permitted-access-tier"></a>Definiera tillåten åtkomstnivå

Följande princip anger vilken typ av [åtkomstnivå](../storage/blobs/storage-blob-storage-tiers.md) som kan anges för storage-konton:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>Se till att kryptering är aktiverat

Följande principen kräver att alla lagringskonton för att aktivera [lagringstjänstens kryptering](../storage/common/storage-service-encryption.md):

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Den här principregeln är också tillgängliga som en inbyggd principdefinitionen med resurs-ID för `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f`.

## <a name="next-steps"></a>Nästa steg
* När du definierar en regel (som visas i föregående exempel) behöver du skapar principdefinitionen och kopplar den till ett omfång. Omfattningen kan vara en prenumeration, resursgrupp eller resurs. Om du vill tilldela principer via portalen finns [Använd Azure-portalen för att tilldela och hantera resursprinciper](resource-manager-policy-portal.md). Om du vill tilldela principer via REST-API, PowerShell eller Azure CLI, se [tilldela och hantera principer via skript](resource-manager-policy-create-assign.md). 
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

