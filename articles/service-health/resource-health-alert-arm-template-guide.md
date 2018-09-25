---
title: Konfigurera Azure resource health-aviseringar med hjälp av Resource Manager-mallar | Microsoft Docs
description: Skapa aviseringar programmässigt som meddelar dig när dina Azure-resurser blir otillgängliga.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/4/2018
ms.author: shtabriz
ms.openlocfilehash: b19a840e5d0d3434d3e3ff91e0d7af037d64fb5a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975368"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurera resource health-aviseringar med hjälp av Resource Manager-mallar

Den här artikeln visar hur du skapar Resource Health Aktivitetsloggaviseringar genom programmering med Azure Resource Manager-mallar och Azure PowerShell.

Azure Resource Health håller dig informerad om aktuell och historisk hälsotillståndet för dina Azure-resurser. Azure Resource Health-aviseringar kan meddela dig i nära realtid när resurserna har en ändring i deras hälsostatus. Skapa Resource Health Tillåt aviseringar programmässigt användare att skapa och anpassa aviseringar gruppvis.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa anvisningarna på den här sidan måste du konfigurera några saker i förväg:

1. Du måste installera den [Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (`AzureRm`)
2. Du behöver [skapa eller återanvända en åtgärdsgrupp](../monitoring-and-diagnostics/monitoring-action-groups.md) konfigurerad för att meddela dig

## <a name="instructions"></a>Instruktioner
1. Med hjälp av PowerShell, logga in på Azure med ditt konto och välj den prenumeration som du vill interagera med

        Login-AzureRmAccount
        Select-AzureRmSubscription -Subscription <subscriptionId>

    > Du kan använda `Get-AzureRmSubscription` för att lista prenumerationerna som du har åtkomst till.

2. Hitta och spara den fullständiga Azure Resource Manager-ID för din åtgärdsgrupp

        (Get-AzureRmActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Skapa och spara en Resource Manager-mall för Resource Health-aviseringar som `resourcehealthalert.json` ([se information nedan](#resource-manager-template-for-resource-health-alerts))

4. Skapa en ny Azure Resource Manager-distribution med hjälp av den här mallen

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Du uppmanas att ange Aviseringsnamn och åtgärden grupp resurs-ID som du kopierade tidigare:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resouceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Om allt har arbetat får du en bekräftelse i PowerShell

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Observera att om du planerar att den här processen automatiseras helt, behöver du bara redigera Resource Manager-mall för att fråga inte om värdena i steg 5.

## <a name="resource-manager-template-for-resource-health-alerts"></a>Resource Manager-mall för Resource Health-aviseringar

Du kan använda den här grundläggande mallen som utgångspunkt för att skapa Resource Health-aviseringar. Den här mallen fungerar likadant som och kommer att registrera dig att ta emot aviseringar för alla nyligen aktiverade resurshälsotillståndshändelser över alla resurser i en prenumeration.

> Längst ned i den här artikeln har vi också inkluderat en mer komplex avisering mall som bör öka signalen-brus-förhållande för Resource Health-aviseringar jämfört med den här mallen.

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
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
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

Men rekommenderas en bred avisering som den här Allmänt inte. Lär dig hur vi kan definiera omfattningen av den här aviseringen kan fokusera på de händelser som vi värnar om nedan.

### <a name="adjusting-the-alert-scope"></a>Justera aviseringar omfattningen

Resource Health-aviseringar kan konfigureras för att övervaka händelser i tre olika omfång:

 * Prenumerationsnivå
 * Resursgruppsnivå
 * Resurs

Aviseringen mallen har konfigurerats på prenumerationsnivån, men om du vill konfigurera aviseringen för att bara meddela dig om vissa resurser eller resurser inom en viss resursgrupp du behöver bara ändra den `scopes` avsnittet ovan mallen.

För en resurs på gruppomfattning, scope-avsnittet se ut som:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

Och för en resurs på omfattning avsnittet omfång bör se ut:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Exempel: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Du kan gå till Azure-portalen och titta på URL: en när du visar din Azure-resurs för att få den här strängen.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Justera resursen typer som varnar dig

Aviseringar i prenumeration eller resursgrupp kan ha olika typer av resurser. Om du vill begränsa aviseringar för att endast komma från en viss delmängd av resurstyper kan du definiera som i den `condition` avsnitt i mallen som detta:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Här använder vi den `anyOf` -gränssnitt för att tillåta resource health aviseringen matchar någon av villkor som vi anger, så att aviseringar som är avsedda för specifika resurstyper.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Resource Health-händelser som varnar dig
Om resurser genomgår en hälsohändelse, de kan gå igenom en serie steg som representerar tillståndet för hälsohändelsen: `Active`, `InProgress`, `Updated`, och `Resolved`.

Du bara vill meddelas när en resurs blir ohälsosamt, då du vill konfigurera aviseringen för att bara meddela när den `status` är `Active`. Men om du vill också bli meddelad på de andra stegen kan du lägga till detaljer som detta:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "InProgress"
                },
                        {
                    "field": "status",
                    "equals": "Resolved"
                }
            ]
        }
    ]
}
```

Om du vill bli informerad om alla fyra faser i health-händelser, kan du ta bort det här tillståndet allt på samma plats och aviseringen meddelar dig oavsett den `status` egenskapen.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Justera Resource Health-aviseringar för att undvika ”okänt” händelser

Azure Resource Health kan rapportera till du senaste hälsotillståndet för dina resurser genom att ständigt övervaka dem med hjälp av test-deltagare. Den relevanta rapporterade health-statusen är: ”tillgänglig”, ”ej tillgänglig” och ”degraderad”. Men i situationer där köraren och Azure-resursen är inte kan kommunicera, ett ”okänt” hälsotillståndet rapporteras för resursen och som betraktas som en ”aktiv” hälsotillståndshändelse.

När en resurs rapporterar ”okänt”, är det dock sannolikt att dess hälsostatus inte har ändrats sedan den senaste korrekta rapporten. Om du vill undvika aviseringar för händelser som ”okänt” kan du ange denna logik i mallen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

I det här exemplet vi bara meddela på händelser där aktuella och tidigare hälsostatus inte har ”okänt”. Den här ändringen kan vara ett användbart tillägg om dina aviseringar skickas direkt till din mobiltelefon eller e-post.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Justera aviseringen för att undvika användarinitierad händelser

Resource Health-händelser kan vara utlösare efter plattform som initierade och användarinitierad händelser. Det kan vara klokt att bara skicka en avisering när hälsohändelsen orsakas av Azure-plattformen.

Det är enkelt att konfigurera aviseringen för att filtrera för dessa typer av händelser:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```

## <a name="recommended-resource-health-alert-template"></a>Rekommenderade aviseringar Resource Health-mall

Med de olika justeringar som beskrivs i föregående avsnitt kan skapa vi en omfattande avisering mall som är konfigurerad för att maximera signalen-brus-förhållande.

Här är vad vi föreslår att du använder:
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
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "InProgress",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
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

Men du vet bäst vilka konfigurationer som gäller för dig, så Använd de verktyg som undervisats till dig i den här dokumentationen för att göra egna anpassning.

## <a name="next-steps"></a>Nästa steg

Läs mer om Resource Health:
-  [Översikt över Azure Resource Health](Resource-health-overview.md)
-  [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)

Skapa Service Health-aviseringar:
-  [Konfigurera aviseringar för Service Health](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) 
