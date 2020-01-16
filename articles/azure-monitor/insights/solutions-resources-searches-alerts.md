---
title: Sparade sökningar i hanterings lösningar | Microsoft Docs
description: Hanterings lösningar innehåller vanligt vis sparade logg frågor för att analysera data som samlas in av lösningen. I den här artikeln beskrivs hur du definierar Log Analytics sparade sökningar i en Resource Manager-mall.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ff9c45ffb636f53951a763f617c25a2e8c09088
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977729"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Lägga till Log Analytics sparade sökningar och varningar till hanterings lösningen (förhands granskning)

> [!IMPORTANT]
> Som du [presenterade tidigare](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), kan Log Analytics-arbetsytor som skapats efter den *1 juni 2019* -hantera aviserings regler med **bara** Azure-ScheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), [Azure Resource Manager Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) och PowerShell- [cmdleten](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Kunder kan enkelt [ändra sina önskade metoder för varnings regel hantering](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) för äldre arbets ytor för att utnyttja Azure Monitor scheduledQueryRules som standard och få många [nya fördelar](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) som möjligheten att använda inbyggda PowerShell-cmdlets, ökad lookback tids period i regler, skapande av regler i separata resurs grupper eller prenumerationer och mycket mer.

> [!NOTE]
> Det här är en preliminär dokumentation för att skapa hanterings lösningar som för närvarande finns i för hands version. Alla scheman som beskrivs nedan kan komma att ändras.

[Hanterings lösningar](solutions.md) innehåller vanligt vis [sparade sökningar](../../azure-monitor/log-query/log-query-overview.md) i Log Analytics för att analysera data som samlas in av lösningen. De kan också definiera [aviseringar](../../azure-monitor/platform/alerts-overview.md) för att meddela användaren eller automatiskt vidta åtgärder som svar på ett kritiskt problem. I den här artikeln beskrivs hur du definierar Log Analytics sparade sökningar och aviseringar i en [mall för resurs hantering](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) så att de kan ingå i [hanterings lösningar](solutions-creating.md).

> [!NOTE]
> I exemplen i den här artikeln används parametrar och variabler som antingen är obligatoriska eller vanliga för hanterings lösningar och som beskrivs i [utforma och skapa en hanterings lösning i Azure](solutions-creating.md)

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan är bekant med hur du [skapar en hanterings lösning](solutions-creating.md) och strukturen i en [Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) och lösnings fil.


## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Alla resurser i Log Analytics finns i en [arbets yta](../../azure-monitor/platform/manage-access.md). Som det beskrivs i [Log Analytics arbets yta och Automation-konto](solutions.md#log-analytics-workspace-and-automation-account)ingår inte arbets ytan i hanterings lösningen utan måste finnas innan lösningen installeras. Om den inte är tillgänglig går det inte att installera lösningen.

Namnet på arbets ytan är namnet på varje Log Analytics resurs. Detta görs i lösningen med parametern **Workspace** som i följande exempel för en SavedSearch-resurs.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-version
Alla Log Analytics resurser som definierats i en Resource Manager-mall har en egenskaps- **API version** som definierar den version av API som resursen ska använda.

I följande tabell visas API-versionen för den resurs som används i det här exemplet.

| Resurstyp | API-version | Söka i data |
|:---|:---|:---|
| savedSearches | 2017-03-15 – för hands version | Händelse &#124; där EventLevelName = = "Error"  |


## <a name="saved-searches"></a>Sparade sökningar
Inkludera [sparade sökningar](../../azure-monitor/log-query/log-query-overview.md) i en lösning så att användarna kan fråga data som samlas in av din lösning. Sparade sökningar visas under **sparade sökningar** i Azure Portal. En sparad sökning krävs också för varje avisering.

[Log Analytics sparade Sök](../../azure-monitor/log-query/log-query-overview.md) resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches` och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen.

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

Varje egenskap i en sparad sökning beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| category | Kategorin för den sparade sökningen.  Alla sparade sökningar i samma lösning delar ofta en enda kategori så att de grupperas tillsammans i-konsolen. |
| displayname (visningsnamn) | Namn som ska visas för den sparade sökningen i portalen. |
| DocumentDB | Fråga att köra. |

> [!NOTE]
> Du kan behöva använda escape-tecken i frågan om den innehåller tecken som kan tolkas som JSON. Om din fråga exempelvis var **AzureActivity | OperationName: "Microsoft. Compute/virtualMachines/Write"** , den ska vara skriven i lösnings filen som **AzureActivity | OperationName:/\"Microsoft. Compute/virtualMachines/Write\"** .

## <a name="alerts"></a>Aviseringar
[Azures logg aviseringar](../../azure-monitor/platform/alerts-unified-log.md) skapas av Azures aviserings regler som kör angivna logg frågor med jämna mellanrum. Om resultatet av frågan matchar de angivna villkoren skapas en aviserings post och en eller flera åtgärder körs med hjälp av [Åtgärds grupper](../../azure-monitor/platform/action-groups.md).

För användare som utökar aviseringar till Azure, styrs nu åtgärder i Azure åtgärdsgrupper. När en arbets yta och dess aviseringar utökas till Azure kan du hämta eller lägga till åtgärder med hjälp av [Åtgärds gruppen-Azure Resource Manager mall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Aviserings regler i en äldre hanterings lösning består av följande tre olika resurser.

- **Sparad sökning.** Definierar loggs ökningen som körs. Flera varnings regler kan dela en enda Sparad sökning.
- **Ange.** Definierar hur ofta loggs ökningen körs. Varje varnings regel har ett och endast ett schema.
- **Aviserings åtgärd.** Varje varnings regel har en åtgärds grupp resurs eller åtgärds resurs (bakåtkompatibelt) med en typ av **avisering** som definierar information om aviseringen, till exempel villkoren för när en aviserings post skapas och aviseringens allvarlighets grad. En [Åtgärds grupp](../../azure-monitor/platform/action-groups.md) resurs kan ha en lista över konfigurerade åtgärder som ska vidtas när aviseringen utlöses, till exempel röst samtal, SMS, e-post, webhook, ITSM-verktyget, Automation Runbook, Logic app osv.

De sparade Sök resurserna beskrivs ovan. De andra resurserna beskrivs nedan.

### <a name="schedule-resource"></a>Schemalägg resurs

En sparad sökning kan ha ett eller flera scheman med varje schema som representerar en separat aviserings regel. Schemat definierar hur ofta sökningen körs och det tidsintervall under vilket data hämtas. Schemalägg resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen.

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
Egenskaperna för schema resurser beskrivs i följande tabell.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| enabled       | Ja | Anger om aviseringen är aktive rad när den skapas. |
| interval      | Ja | Hur ofta frågan körs på några minuter. |
| queryTimeSpan | Ja | Hur lång tid i minuter som resultatet ska utvärderas. |

Schema resursen bör vara beroende av den sparade sökningen så att den skapas före schemat.
> [!NOTE]
> Schema namnet måste vara unikt på en specifik arbets yta. två scheman kan inte ha samma ID även om de är kopplade till olika sparade sökningar. Namn på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics API måste vara i gemener.

### <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer för att utföra, till exempel skickar ett e-postmeddelande eller starta en runbook eller den kan definiera ett tröskelvärde som bestämmer när resultatet av en sökning som matchar vissa kriterier. Vissa åtgärder kommer definiera både så att processerna som utförs när tröskelvärdet är uppfyllt.
Åtgärder kan definieras med hjälp av [åtgärds grupp] resurs eller åtgärds resurs.

Det finns två typer av åtgärds resurser som anges av **typ** egenskapen. Ett schema kräver en **aviserings** åtgärd som definierar information om varnings regeln och vilka åtgärder som vidtas när en avisering skapas. Åtgärds resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Aviserings åtgärder har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen.

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

Egenskaperna för aviserings åtgärds resurser beskrivs i följande tabeller.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| `type` | Ja | Typ av åtgärd.  Detta är en **avisering** för aviserings åtgärder. |
| `name` | Ja | Visningsnamn för aviseringen.  Detta är det namn som visas i konsolen för varnings regeln. |
| `description` | Inga | Valfri beskrivning av aviseringen. |
| `severity` | Ja | Allvarlighets grad för aviserings posten från följande värden:<br><br> **mindre**<br>**honom**<br>**informations**

#### <a name="threshold"></a>Tröskelvärde
Det här avsnittet är obligatoriskt. Den definierar egenskaperna för aviserings tröskelvärdet.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| `Operator` | Ja | Operator för jämförelsen från följande värden:<br><br>**gt = större än<br>lt = mindre än** |
| `Value` | Ja | Värdet att jämföra resultaten. |

##### <a name="metricstrigger"></a>MetricsTrigger
Det här avsnittet är valfritt. Ta med en mått mätnings avisering.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| `TriggerCondition` | Ja | Anger om tröskelvärdet gäller för det totala antalet överträdelser eller över flera överträdelser från följande värden:<br><br>**Totalt antal<br>i följd** |
| `Operator` | Ja | Operator för jämförelsen från följande värden:<br><br>**gt = större än<br>lt = mindre än** |
| `Value` | Ja | Antalet gånger som kriteriet måste uppfyllas för att utlösa aviseringen. |


#### <a name="throttling"></a>Begränsning
Det här avsnittet är valfritt. Inkludera det här avsnittet om du vill ignorera aviseringar från samma regel under en viss tid efter att en avisering har skapats.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| DurationInMinutes | Ja om begränsnings element ingår | Antal minuter att ignorera aviseringar efter att en från samma aviserings regel har skapats. |

#### <a name="azure-action-group"></a>Azure-åtgärds grupp
Alla aviseringar i Azure, använda åtgärdsgrupp som standardmekanism för att hantera åtgärder. Med åtgärdsgrupp kan du ange dina åtgärder en gång och sedan associerar åtgärdsgrupp att flera aviseringar – i Azure. Utan att behöva flera gånger deklarera samma åtgärder om och om igen. Åtgärdsgrupper stöd för flera åtgärder – inklusive e-post, SMS, röstsamtal, ITSM-anslutningen, Automation-Runbook, Webhook URI med mera.

För användare som har utökat sin aviseringar i Azure – bör ett schema nu ha åtgärdsgrupp information skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. E-postinformation, webhook-URL: er, information om Runbook-automatisering och andra åtgärder måste definieras på sidan en åtgärds grupp först innan en avisering skapas. en kan skapa en [Åtgärds grupp från Azure Monitor](../../azure-monitor/platform/action-groups.md) i portalen eller använda [Åtgärds grupp-resurs mal len](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| AzNsNotification | Ja | Resurs-ID för den Azure-åtgärds grupp som ska associeras med avisering för att vidta nödvändiga åtgärder när aviserings villkoret är uppfyllt. |
| CustomEmailSubject | Inga | Anpassad ämnesrad för e-postmeddelandet som skickas till alla adresser som anges i den associerade åtgärds gruppen. |
| CustomWebhookPayload | Inga | Anpassad nytto last som ska skickas till alla webhook-slutpunkter som definierats i den associerade åtgärds gruppen. Formatet beror på vad webhook förväntar sig och bör vara en giltig serialiserad JSON. |

## <a name="sample"></a>Exempel

Följande är ett exempel på en lösning som innehåller följande resurser:

- Sparad sökning
- Schema
- Åtgärds grupp

Exemplet använder variabler för [standardlösnings parametrar]( solutions-solution-file.md#parameters) som vanligt vis används i en lösning i stället för hårdkoda-värden i resurs definitionerna.

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

Följande parameter fil innehåller exempel värden för den här lösningen.

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
* [Lägg till vyer](solutions-resources-views.md) i hanterings lösningen.
* [Lägg till Automation-runbooks och andra resurser](solutions-resources-automation.md) i hanterings lösningen.
