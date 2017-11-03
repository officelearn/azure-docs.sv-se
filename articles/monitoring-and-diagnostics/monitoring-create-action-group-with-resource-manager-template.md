---
title: "Skapa åtgärdsgrupper med Resource Manager-mallar | Microsoft Docs"
description: "Lär dig hur du skapar en grupp med en Azure Resource Manager-mall."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 76bf353cac13f1c2169380f8dd3c1e163d4f3f41
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Skapa en grupp med en Resource Manager-mall
Den här artikeln visar hur du använder en [Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) så här konfigurerar du åtgärdsgrupper. Med hjälp av mallar kan du automatiskt konfigurera åtgärdsgrupper som kan återanvändas i vissa typer av aviseringar. Åtgärdsgrupperna se till att rätt parterna meddelas när en avisering utlöses.

Stegen är:

1. Skapa en mall som en JSON-fil som beskriver hur du skapar åtgärdsgruppen.

2. Distribuera mallen med hjälp av [alla distributionsmetoden](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Först beskrivs hur du skapar en Resource Manager-mall för en grupp där definitionerna för åtgärden är hårdkodat i mallen. Därefter beskrivs hur du skapar en mall som tar webhook konfigurationsinformation som indataparametrar när mallen distribueras.

## <a name="resource-manager-templates-for-an-action-group"></a>Resource Manager-mallar för en grupp

Om du vill skapa en grupp med hjälp av en Resource Manager-mall som du skapar en resurs av typen `Microsoft.Insights/actionGroups`. Du fylla i alla relaterade egenskaper. Här är två exempelmallar som skapar en grupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md).
* Lär dig mer om [aviseringar](monitoring-overview-alerts.md).
* Lär dig hur du lägger till [aviseringar med hjälp av en Resource Manager-mall](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
