---
title: Mall för att skapa resurshälsoaviseringar
description: Skapa aviseringar programmässigt som meddelar dig när dina Azure-resurser blir otillgängliga.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: c01934cc88dc29d0503abfafc203ab0f04bf1761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062898"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurera resurshälsoaviseringar med hjälp av Resource Manager-mallar

Den här artikeln visar hur du skapar Resource Health Activity Log Alerts programmässigt med Azure Resource Manager-mallar och Azure PowerShell.

Azure Resource Health håller dig informerad om den aktuella och historiska hälsostatusen för dina Azure-resurser. Azure Resource Health-aviseringar kan meddela dig i nära realtid när dessa resurser har en ändring i sin hälsostatus. Genom att skapa resource health-aviseringar kan användarna skapa och anpassa aviseringar i grupp.

> [!NOTE]
> Resurshälsoaviseringar för närvarande är i förhandsgranskning.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Om du vill följa instruktionerna på den här sidan måste du skapa några saker i förväg:

1. Du måste installera [Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. Du måste [skapa eller återanvända en åtgärdsgrupp](../azure-monitor/platform/action-groups.md) som konfigurerats för att meddela dig

## <a name="instructions"></a>Instruktioner
1. Med PowerShell loggar du in på Azure med ditt konto och väljer den prenumeration du vill interagera med

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Du kan `Get-AzSubscription` använda för att lista de prenumerationer du har åtkomst till.

2. Hitta och spara det fullständiga Azure Resource Manager-ID:et för din åtgärdsgrupp

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Skapa och spara en Resource Manager-mall `resourcehealthalert.json` för resurshälsoaviseringar som ([se information nedan)](#resource-manager-template-options-for-resource-health-alerts)

4. Skapa en ny Azure Resource Manager-distribution med den här mallen

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Du uppmanas att skriva in det resurs-ID för aviseringsnamn och åtgärdsgrupp som du kopierade tidigare:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Om allt fungerade framgångsrikt får du en bekräftelse i PowerShell

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

Observera att om du planerar att helt automatisera den här processen behöver du bara redigera Resource Manager-mallen för att inte fråga efter värdena i steg 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Mallalternativ för Resurshanteraren för resurshälsoaviseringar

Du kan använda den här basmallen som utgångspunkt för att skapa resurshälsoaviseringar. Den här mallen fungerar som den är skriven och registrerar dig för att få aviseringar för alla nyligen aktiverade resurshälsohändelser i alla resurser i en prenumeration.

> Längst ner i den här artikeln har vi också inkluderat en mer komplex varningsmall som bör öka signal-brusförhållandet för Resource Health-varningar jämfört med den här mallen.

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

En bred varning som denna rekommenderas dock i allmänhet inte. Läs om hur vi kan begränsa den här aviseringen för att fokusera på de händelser vi bryr oss om nedan.

### <a name="adjusting-the-alert-scope"></a>Justera varningsomfattningen

Resurshälsoaviseringar kan konfigureras för att övervaka händelser med tre olika scope:

 * Prenumerationsnivå
 * Resursgruppsnivå
 * Resursnivå

Aviseringsmallen är konfigurerad på prenumerationsnivå, men om du vill konfigurera aviseringen så att den bara meddelar dig `scopes` om vissa resurser, eller resurser inom en viss resursgrupp, behöver du bara ändra avsnittet i mallen ovan.

För ett scope på resursgruppsnivå ska scopeavsnittet se ut:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

Och för ett scope på resursnivå bör scopeavsnittet se ut:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Exempel: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Du kan gå till Azure Portal och titta på URL:en när du visar din Azure-resurs för att hämta den här strängen.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Justera de resurstyper som varnar dig

Aviseringar på prenumerations- eller resursgruppsnivå kan ha olika typer av resurser. Om du vill begränsa aviseringar till att bara komma från en viss delmängd av resurstyper kan du definiera det i `condition` avsnittet i mallen så här:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Här använder `anyOf` vi omslaget för att resurshälsoaviseringen ska kunna matcha något av de villkor vi anger, vilket möjliggör aviseringar som riktar sig till specifika resurstyper.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Justera de resurshälsohändelser som varnar dig
När resurser genomgår en hälsohändelse kan de gå igenom en serie steg `Active` `In Progress`som `Updated`representerar hälsohändelsens tillstånd: , , och `Resolved`.

Du kanske bara vill bli meddelad när en resurs blir felfritt, i `status` vilket `Active`fall du vill konfigurera aviseringen så att den bara meddelas när den är . Men om du också vill bli meddelad om de andra stegen kan du lägga till dessa detaljer så här:

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
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Om du vill bli meddelad för alla fyra stadierna av hälsohändelser kan du ta `status` bort det här villkoret tillsammans och aviseringen meddelar dig oavsett egenskap.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Justera resurshälsoaviseringarna för att undvika "okända" händelser

Azure Resource Health kan rapportera till dig den senaste hälsan för dina resurser genom att ständigt övervaka dem med hjälp av testlöpare. De relevanta rapporterade hälsotillstånden är: "Tillgänglig", "Ej tillgänglig" och "Degraderad". I situationer där löparen och Azure-resursen inte kan kommunicera rapporteras dock en "okänd" hälsostatus för resursen och som anses vara en "aktiv" hälsohändelse.

Men när en resurs rapporterar "Okänd" är det troligt att dess hälsostatus inte har ändrats sedan den senaste korrekta rapporten. Om du vill ta bort aviseringar om okända händelser kan du ange den logiken i mallen:

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

I det här exemplet meddelar vi bara händelser där den aktuella och tidigare hälsostatusen inte har "Okänd". Den här ändringen kan vara ett användbart tillägg om dina aviseringar skickas direkt till din mobiltelefon eller e-post. 

Observera att det är möjligt att de aktuella hälsostatus- och tidigare Hälsostatusegenskaperna är null i vissa händelser. När en uppdaterad händelse till exempel inträffar är det troligt att resursens hälsostatus inte har ändrats sedan den senaste rapporten, bara att ytterligare händelseinformation är tillgänglig (t.ex. orsak). Därför kan användning av satsen ovan leda till att vissa aviseringar inte utlöses, eftersom värdena properties.currentHealthStatus och properties.previousHealthStatus kommer att anges till null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Justera aviseringen för att undvika användarinitierade händelser

Resource Health-händelser kan utlösas av plattformsinitierade och användarinitierade händelser. Det kan vara klokt att bara skicka ett meddelande när hälsohändelsen orsakas av Azure-plattformen.

Det är enkelt att konfigurera aviseringen så att den filtreras för endast den här typen av händelser:

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
Observera att det är möjligt att orsaksfältet är null i vissa händelser. Det vill säga en hälsoövergång sker (t.ex. tillgänglig för otillgänglig) och händelsen loggas omedelbart för att förhindra meddelandeförseningar. Därför kan användning av satsen ovan leda till att en avisering inte utlöses, eftersom egenskapsvärdet properties.clause kommer att anges till null.

## <a name="complete-resource-health-alert-template"></a>Varningsmall för fullständig resurshälsa

Med hjälp av de olika justeringar som beskrivs i föregående avsnitt är här en exempelmall som är konfigurerad för att maximera förhållandet mellan signal och bru. Tänk på de varningar som anges ovan där den aktuellaHealthStatus, tidigareHealthStatus och orsaka egenskapsvärden kan vara null i vissa händelser.

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
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
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

Du vet dock bäst vilka konfigurationer som är effektiva för dig, så använd de verktyg som du får lära dig i den här dokumentationen för att göra din egen anpassning.

## <a name="next-steps"></a>Nästa steg

Läs mer om Resurshälsa:
-  [Hälsoöversikt över Azure Resource](Resource-health-overview.md)
-  [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)


Skapa hälsovarningar för tjänsten:
-  [Konfigurera aviseringar för tjänstens hälsotillstånd](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Händelseschema för Azure Activity Log](../azure-monitor/platform/activity-log-schema.md)
