---
title: Sparade sökningar i hanteringslösningar | Microsoft-dokument
description: Hanteringslösningar innehåller vanligtvis sparade loggfrågor för att analysera data som samlas in av lösningen. I den här artikeln beskrivs hur du definierar Sparade sökningar i Log Analytics i en Resource Manager-mall.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663036"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Lägga till sparade sökningar och aviseringar för Logganalys i hanteringslösningen (förhandsversion)

> [!IMPORTANT]
> Som [tidigare meddelats](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)kan logganalysarbetsytor som skapats efter *den 1 juni 2019* hantera varningsregler med **endast** Azure scheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), Azure Resource Manager [Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) och [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Kunder kan enkelt byta sina önskade sätt att [hantera varningsregel för](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) äldre arbetsytor för att utnyttja Azure Monitor-schemalagdasekvenser som standard och få många [nya fördelar](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) som möjligheten att använda inbyggda PowerShell-cmdlets, ökad tillbakablickstid i regler, skapande av regler i separat resursgrupp eller prenumeration och mycket mer.

> [!NOTE]
> Detta är preliminär dokumentation för att skapa hanteringslösningar som för närvarande förhandsgranskas. Alla scheman som beskrivs nedan kan komma att ändras.

[Hanteringslösningar](solutions.md) innehåller vanligtvis [sparade sökningar](../../azure-monitor/log-query/log-query-overview.md) i Log Analytics för att analysera data som samlas in av lösningen. De kan också definiera [aviseringar](../../azure-monitor/platform/alerts-overview.md) för att meddela användaren eller automatiskt vidta åtgärder som svar på ett kritiskt problem. I den här artikeln beskrivs hur du definierar Sparade sökningar och aviseringar för Log Analytics i en [resurshanteringsmall](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) så att de kan inkluderas i [hanteringslösningar](solutions-creating.md).

> [!NOTE]
> I exemplen i den här artikeln används parametrar och variabler som antingen krävs eller är gemensamma för hanteringslösningar och beskrivs i [Design och bygg en hanteringslösning i Azure](solutions-creating.md)

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan är bekant med hur du [skapar en hanteringslösning](solutions-creating.md) och strukturen för en [Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) och lösningsfil.


## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Alla resurser i Logganalys finns på en [arbetsyta](../../azure-monitor/platform/manage-access.md). Som beskrivs i [Log Analytics arbetsyta och Automation-konto](solutions.md#log-analytics-workspace-and-automation-account)ingår arbetsytan inte i hanteringslösningen utan måste finnas innan lösningen installeras. Om den inte är tillgänglig misslyckas lösningsinstallationen.

Namnet på arbetsytan finns i namnet på varje Log Analytics-resurs. Detta görs i lösningen med **arbetsytans** parameter som i följande exempel på en SavedSearch-resurs.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Api-version för logganalys
Alla Log Analytics-resurser som definierats i en Resource Manager-mall har en egenskap **apiVersion** som definierar den version av API:et som resursen ska använda.

I följande tabell visas API-versionen för den resurs som används i det här exemplet.

| Resurstyp | API-version | Söka i data |
|:---|:---|:---|
| savedSearches sparadeSearches | 2017-03-15-förhandsvisning | Händelse &#124; där EventLevelName == "Fel"  |


## <a name="saved-searches"></a>Sparade sökningar
Inkludera [sparade sökningar](../../azure-monitor/log-query/log-query-overview.md) i en lösning så att användarna kan fråga data som samlas in av din lösning. Sparade sökningar visas under **Sparade sökningar** i Azure-portalen. En sparad sökning krävs också för varje avisering.

[Sparade sökresurser för logganalys](../../azure-monitor/log-query/log-query-overview.md) har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches` och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Varje egenskap för en sparad sökning beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| category | Kategorin för den sparade sökningen.  Alla sparade sökningar i samma lösning delar ofta en enda kategori så att de grupperas tillsammans i konsolen. |
| displayname (visningsnamn) | Namn som ska visas för den sparade sökningen i portalen. |
| DocumentDB | Fråga som ska köras. |

> [!NOTE]
> Du kan behöva använda escape-tecken i frågan om det innehåller tecken som kan tolkas som JSON. Om din fråga till exempel var **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"** bör den skrivas i lösningsfilen som **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Aviseringar
[Azure Log-aviseringar](../../azure-monitor/platform/alerts-unified-log.md) skapas av Azure Alert-regler som kör angivna loggfrågor med jämna mellanrum. Om resultatet av frågematchningen matchar angivna villkor skapas en varningspost och en eller flera åtgärder körs med hjälp av [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

För användare som utökar aviseringar till Azure kontrolleras nu åtgärder i Azure-åtgärdsgrupper. När en arbetsyta och dess aviseringar utökas till Azure kan du hämta eller lägga till åtgärder med hjälp av [mallen Åtgärdsgrupp - Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Varningsregler i äldre hanteringslösning består av följande tre olika resurser.

- **Sparad sökning.** Definierar loggsökningen som körs. Flera varningsregler kan dela en enda sparad sökning.
- **Schema.** Definierar hur ofta loggsökningen körs. Varje varningsregel har ett och bara ett schema.
- **Varningsåtgärd.** Varje aviseringsregel har en åtgärdsgruppresurs eller åtgärdsresurs (äldre) med en typ av **avisering** som definierar information om aviseringen, till exempel villkoren för när en aviseringspost skapas och aviseringens allvarlighetsgrad. [Åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) resurs kan ha en lista över konfigurerade åtgärder att vidta när avisering utlöses - till exempel röstsamtal, SMS, e-post, webhook, ITSM-verktyg, automatisering runbook, logik app, etc.

Sparade sökresurser beskrivs ovan. De övriga resurserna beskrivs nedan.

### <a name="schedule-resource"></a>Schemalägg resurs

En sparad sökning kan ha ett eller flera scheman med varje schema som representerar en separat varningsregel. Schemat definierar hur ofta sökningen körs och det tidsintervall som data hämtas över. Schemalägg resurser har `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` en typ av och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
Egenskaperna för schemaresurser beskrivs i följande tabell.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| enabled       | Ja | Anger om aviseringen är aktiverad när den skapas. |
| interval      | Ja | Hur ofta frågan körs på några minuter. |
| frågaTimeSpan | Ja | Tid i minuter för att utvärdera resultaten. |

Schemaresursen bör vara beroende av den sparade sökningen så att den skapas före schemat.
> [!NOTE]
> Schemanamn måste vara unikt på en viss arbetsyta. två scheman kan inte ha samma ID även om de är associerade med olika sparade sökningar. Namn för alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics API måste vara i gemener.

### <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer som ska utföras, till exempel skicka ett e-postmeddelande eller starta en runbook, eller så kan den definiera ett tröskelvärde som avgör när resultatet av en sökning matchar vissa villkor. Vissa åtgärder definierar båda så att processerna utförs när tröskelvärdet uppfylls.
Åtgärder kan definieras med hjälp av [åtgärdsgrupp] resurs eller åtgärdsresurs.

Det finns två typer av åtgärdsresurs som anges av egenskapen **Type.** Ett schema kräver en **varningsåtgärd** som definierar information om aviseringsregeln och vilka åtgärder som vidtas när en avisering skapas. Åtgärdsresurser har `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`en typ av .

Varningsåtgärder har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

Egenskaperna för aviseringsåtgärdsresurser beskrivs i följande tabeller.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| `type` | Ja | Typ av åtgärd.  Detta är **Avisering** för varningsåtgärder. |
| `name` | Ja | Visningsnamn för aviseringen.  Det här är namnet som visas i konsolen för varningsregeln. |
| `description` | Inga | Valfri beskrivning av aviseringen. |
| `severity` | Ja | Allvarlighetsgrad för varningsposten från följande värden:<br><br> **Kritiska**<br>**Varning**<br>**Informativt**

#### <a name="threshold"></a>Tröskelvärde
Det här avsnittet krävs. Den definierar egenskaperna för aviseringströskelvärdet.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| `Operator` | Ja | Operator för jämförelsen från följande värden:<br><br>**gt =<br>större än lt = mindre än** |
| `Value` | Ja | Värdet för att jämföra resultaten. |

##### <a name="metricstrigger"></a>MetricsTrigger
Det här avsnittet är valfritt. Inkludera den för en måttmätningsavisering.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| `TriggerCondition` | Ja | Anger om tröskelvärdet är för totalt antal överträdelser eller på varandra följande överträdelser från följande värden:<br><br>**Totalt<br>i följd** |
| `Operator` | Ja | Operator för jämförelsen från följande värden:<br><br>**gt =<br>större än lt = mindre än** |
| `Value` | Ja | Antal gånger som villkoren måste uppfyllas för att utlösa aviseringen. |


#### <a name="throttling"></a>Begränsning
Det här avsnittet är valfritt. Inkludera det här avsnittet om du vill ignorera aviseringar från samma regel under en viss tid efter att en avisering har skapats.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| VaraktighetInMinutes | Ja om begränsningselement ingår | Antal minuter för att undertrycka aviseringar efter att en från samma varningsregel har skapats. |

#### <a name="azure-action-group"></a>Azure-åtgärdsgrupp
Alla aviseringar i Azure använder Åtgärdsgrupp som standardmekanism för att hantera åtgärder. Med Åtgärdsgrupp kan du ange dina åtgärder en gång och sedan associera åtgärdsgruppen till flera aviseringar – i Azure. Utan behov, att upprepade gånger förklara samma åtgärder om och om igen. Åtgärdsgrupper stöder flera åtgärder - inklusive e-post, SMS, röstsamtal, ITSM-anslutning, Automation Runbook, Webhook URI med mera.

För användare som har utökat sina aviseringar till Azure - ett schema bör nu ha åtgärdsgruppinformation skickad tillsammans med tröskelvärdet, för att kunna skapa en avisering. E-postinformation, Webhook-url:er, Runbook Automation-information och andra åtgärder måste definieras i sidan en åtgärdsgrupp först innan du skapar en avisering. Man kan skapa [åtgärdsgrupp från Azure Monitor](../../azure-monitor/platform/action-groups.md) i Portal eller använda [Åtgärdsgrupp - Resursmall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| AzNsNotification | Ja | Resurs-ID:n för azure-åtgärdsgruppen som ska associeras med avisering för att vidta nödvändiga åtgärder när varningskriterierna uppfylls. |
| CustomEmailSbject | Inga | Anpassad ämnesrad för e-postmeddelandet som skickas till alla adresser som anges i den associerade åtgärdsgruppen. |
| Anpassad WebbkrokBetalningsbelastning | Inga | Anpassad nyttolast som ska skickas till alla webhook-slutpunkter som definierats i associerad åtgärdsgrupp. Formatet beror på vad webhook förväntar sig och bör vara en giltig serialiserad JSON. |

## <a name="sample"></a>Exempel

Följande är ett exempel på en lösning som innehåller följande resurser:

- Sparad sökning
- Schema
- Åtgärdsgrupp

I exemplet används [standardvariabler för lösningsparametrar]( solutions-solution-file.md#parameters) som ofta används i en lösning i motsats till hårdkodningsvärden i resursdefinitionerna.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

Följande parameterfil innehåller exempelvärden för den här lösningen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
* [Lägg till vyer](solutions-resources-views.md) i hanteringslösningen.
* [Lägg till Automation-runbooks och andra resurser](solutions-resources-automation.md) i hanteringslösningen.
