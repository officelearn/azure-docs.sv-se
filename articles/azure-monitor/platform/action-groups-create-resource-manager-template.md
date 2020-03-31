---
title: Skapa åtgärdsgrupper med Resource Manager-mallar
description: Lär dig hur du skapar en åtgärdsgrupp med hjälp av en Azure Resource Manager-mall.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 50ad9d57b24fab9ee57c2f9caae8f4c39d2681f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669088"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Skapa en åtgärdsgrupp med en Resource Manager-mall
Den här artikeln visar hur du använder en [Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) för att konfigurera åtgärdsgrupper. Genom att använda mallar kan du automatiskt ställa in åtgärdsgrupper som kan återanvändas i vissa typer av aviseringar. Dessa åtgärdsgrupper säkerställer att alla rätt parter meddelas när en avisering utlöses.

De grundläggande stegen är:

1. Skapa en mall som en JSON-fil som beskriver hur du skapar åtgärdsgruppen.

2. Distribuera mallen med valfri [distributionsmetod](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Först beskriver vi hur du skapar en Resource Manager-mall för en åtgärdsgrupp där åtgärdsdefinitionerna är hårdkodade i mallen. För det andra beskriver vi hur du skapar en mall som tar webhook-konfigurationsinformationen som indataparametrar när mallen distribueras.

## <a name="resource-manager-templates-for-an-action-group"></a>Resource Manager-mallar för en åtgärdsgrupp

Om du vill skapa en åtgärdsgrupp med hjälp `Microsoft.Insights/actionGroups`av en Resource Manager-mall skapar du en resurs av typen . Sedan fyller du i alla relaterade egenskaper. Här är två exempelmallar som skapar en åtgärdsgrupp.

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
* Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).
* Läs mer om [aviseringar](alerts-overview.md).
* Lär dig hur du lägger till [aviseringar med hjälp av en Resource Manager-mall](../../azure-monitor/platform/alerts-activity-log.md).

