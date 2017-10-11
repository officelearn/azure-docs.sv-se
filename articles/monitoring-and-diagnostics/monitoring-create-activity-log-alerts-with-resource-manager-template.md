---
title: Skapa en aktivitet loggen avisering med en Resource Manager-mall | Microsoft Docs
description: "Håll dig informerad när resurserna i Azure skapas."
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
ms.date: 07/06/2017
ms.author: ancav
ms.openlocfilehash: 92076c7fe1f867919b7e02abf79cf0fb74fb7eb4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Skapa en aktivitet loggen avisering med en Resource Manager-mall
Den här artikeln visar hur du använder en [Azure Resource Manager-mall](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) att konfigurera aviseringar för aktiviteten loggen. Med hjälp av mallar kan du enkelt konfigurera många aviseringar som aktiverar baserat på specifika aktivitet loggen händelse villkor som en del av processen för automatisk distribution.

Stegen är:

1. Skapa en mall som en JSON-fil som beskriver hur du skapar aktiviteten loggen aviseringen.

2. Distribuera mallen med hjälp av [alla distributionsmetoden](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Resource Manager-mall för en aktivitet log-varning
Om du vill skapa en aktivitet loggen avisering med hjälp av en Resource Manager-mall som du skapar en resurs av typen `microsoft.insights/activityLogAlerts`. Du fylla i alla relaterade egenskaper. Här är en mall som skapar en avisering för aktiviteten loggen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Besök vår [Azure snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) några exempel på aviseringen aktivitetsmallar för loggen.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [aviseringar](monitoring-overview-alerts.md).
- Lär dig hur du lägger till [åtgärdsgrupper med hjälp av en Resource Manager-mall](monitoring-create-action-group-with-resource-manager-template.md).
- Lär dig hur du [skapa en varning om du vill övervaka alla Autoskala motorn åtgärder för din prenumeration loggen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Lär dig hur du [skapa en varning om du vill övervaka alla misslyckade Autoskala skala-/ skalbar åtgärder för din prenumeration loggen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
