---
title: "Azure resursprinciper för nätverksresurser | Microsoft Docs"
description: "Beskriver principer för Azure Resource Manager för att hantera distributionen av nätverksresurser."
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
ms.openlocfilehash: bca66bbdd9da9b3e4099d0d961f42c9368a17f5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-to-network-resources"></a>Använda resursprinciper för nätverksresurser
Den här artikeln visar ett exempel [resursprincip](resource-manager-policy.md) du kan använda Azure virtuella nätverksgatewayerna. Den här principen garanterar konsekvens för gateways som distribuerats i organisationen. 

## <a name="define-permitted-virtual-network-gateway-sku"></a>Definiera tillåtna virtuell nätverksgateway SKU

Följande princip begränsar vilka SKU: er kan distribueras för virtuella nätverksgatewayer:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Network/virtualNetworkGateways"
      },
      {
        "not": {
          "field": "Microsoft.Network/virtualNetworkGateways/sku.name",
          "in": [
            "Basic",
            "VpnGw1"
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

## <a name="next-steps"></a>Nästa steg
* När du definierar en regel (som visas i föregående exempel) behöver du skapar principdefinitionen och kopplar den till ett omfång. Omfattningen kan vara en prenumeration, resursgrupp eller resurs. Om du vill tilldela principer via portalen finns [Använd Azure-portalen för att tilldela och hantera resursprinciper](resource-manager-policy-portal.md). Om du vill tilldela principer via REST-API, PowerShell eller Azure CLI, se [tilldela och hantera principer via skript](resource-manager-policy-create-assign.md). 
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

