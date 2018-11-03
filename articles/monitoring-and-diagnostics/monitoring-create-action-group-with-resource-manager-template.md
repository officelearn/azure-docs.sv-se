---
title: Skapa åtgärdsgrupper med Resource Manager-mallar
description: Lär dig hur du skapar en grupp med en Azure Resource Manager-mall.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 583a84fd943f6dab63f94e2811e21853e6e5f6a1
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959179"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Skapa en grupp med en Resource Manager-mall
Den här artikeln visar hur du använder en [Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) konfigurera åtgärdsgrupper. Med hjälp av mallar kan du automatiskt konfigurera åtgärdsgrupper som kan återanvändas i vissa typer av aviseringar. Dessa åtgärdsgrupper se till att rätt parterna får ett meddelande när en avisering utlöses.

De grundläggande stegen är:

1. Skapa en mall som en JSON-fil som beskriver hur du skapar åtgärdsgruppen.

2. Distribuera mallen med hjälp av [alla distributionsmetod](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Först beskrivs hur du skapar en Resource Manager-mall för en grupp där definitionerna för åtgärden är hårdkodad i mallen. Dessutom beskrivs hur du skapar en mall som tar en webhook-konfigurationsinformation som indataparametrar när mallen distribueras.

## <a name="resource-manager-templates-for-an-action-group"></a>Resource Manager-mallar för en åtgärdsgrupp

Om du vill skapa en grupp med en Resource Manager-mall, skapar du en resurs av typen `Microsoft.Insights/actionGroups`. Du fylla i alla relaterade egenskaper. Här följer två exempelmallar som skapar en åtgärdsgrupp.

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
      "apiVersion": "2018-03-01",
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
        "description": "Webhook receiver service Name."
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
      "apiVersion": "2018-03-01",
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
* Läs mer om [åtgärdsgrupper](monitoring-action-groups.md).
* Läs mer om [aviseringar](monitoring-overview-alerts.md).
* Lär dig hur du lägger till [aviseringar med hjälp av Resource Manager-mall](alert-activity-log.md).
