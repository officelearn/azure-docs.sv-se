---
title: Sparade sökningar i hanteringslösningar | Microsoft Docs
description: I hanteringslösningarna ingår vanligtvis sparade sökningar i Log Analytics för att analysera data som samlas in av lösningen. De kan också definiera varningar som meddelar användaren eller automatiskt vidta åtgärder som svar på ett kritiskt problem. Den här artikeln beskriver hur du definierar sparade sökningar i en Resource Manager-mall så att de kan ingå i lösningar för hantering av Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8590c9ef89e68a823beefd7e74a894edd219359
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779393"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Lägga till Log Analytics sparade sökningar och aviseringar till lösning för hantering (förhandsversion)

> [!IMPORTANT]
> Informationen här för att skapa en avisering med en Resource Manager-mall är av datum nu när [Log Analytics-aviseringar har utökats till Azure Monitor](../platform/alerts-extend.md). Mer information om hur du skapar en avisering om log med en Resource Manager-mall finns i [Hantera aviseringar med hjälp av Azure-resursmall](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Det här är preliminära dokumentationen för att skapa lösningar för hantering som för närvarande i förhandsversion. Ett schema som beskrivs nedan kan komma att ändras.

[Lösningar för hantering av](solutions.md) inkluderar vanligtvis [sparade sökningar](../../azure-monitor/log-query/log-query-overview.md) i Log Analytics för att analysera data som samlas in av lösningen. De kan också definiera [aviseringar](../../azure-monitor/platform/alerts-overview.md) att meddela användaren eller automatiskt vidta åtgärder som svar på ett kritiskt problem. Den här artikeln beskriver hur du definierar Log Analytics sparade sökningar och aviseringar i en [Resource Manager-mall](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) så att de kan ingå i [hanteringslösningar](solutions-creating.md).

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [utforma och skapa en lösning i Azure](solutions-creating.md)

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med hur du [skapa en lösning för](solutions-creating.md) och strukturen för en [Resource Manager-mall](../../azure-resource-manager/resource-group-authoring-templates.md) och lösningsfilen.


## <a name="log-analytics-workspace"></a>Log Analytics Workspace
Alla resurser i Log Analytics finns i en [arbetsytan](../../azure-monitor/platform/manage-access.md). Mer information finns i [Log Analytics-arbetsytan och Automation-kontot](solutions.md#log-analytics-workspace-and-automation-account), arbetsytan ingår inte i hanteringslösningen men måste finnas innan lösningen är installerad. Om den inte är tillgänglig misslyckas lösning installationen.

Namnet på arbetsytan är namnet på varje Log Analytics-resurs. Detta görs i lösningen med den **arbetsytan** parameter som i följande exempel för en resurs för SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics-API-version
Alla Log Analytics-resurser som definierats i en Resource Manager-mallen har en egenskap **apiVersion** som definierar versionen av API: et som resursen ska använda.

I följande tabell visas den API-versionen för resursen i det här exemplet.

| Resurstyp | API-version | Söka i data |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Händelsen &#124; där EventLevelName == ”Error”  |


## <a name="saved-searches"></a>Sparade sökningar
Inkludera [sparade sökningar](../../azure-monitor/log-query/log-query-overview.md) i en lösning för att tillåta användare att köra frågor mot data som samlas in av din lösning. Sparade sökningar visas under **sparade sökningar** i Azure-portalen. En sparad sökning krävs också för varje avisering.

[Sparade log Analytics-sökningen](../../azure-monitor/log-query/log-query-overview.md) resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches` och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen.

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

I följande tabell beskrivs varje egenskap för en sparad sökning.

| Egenskap  | Beskrivning |
|:--- |:--- |
| category | Kategorin för den sparade sökningen.  Alla sparade sökningar i samma lösning kommer ofta att dela en enskild kategori så att de grupperas tillsammans i konsolen. |
| displayname (visningsnamn) | Namn som ska visas på den sparade sökningen i portalen. |
| DocumentDB | Frågan ska köras. |

> [!NOTE]
> Du kan behöva använda escape-tecken i frågan om den innehåller tecken som kan tolkas som JSON. Exempel: om din fråga var **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write”**, den måste skrivas i lösningsfilen som **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Aviseringar
[Azure loggaviseringar](../../azure-monitor/platform/alerts-unified-log.md) skapas av Azure Varningsregler som kör angivna loggfrågor med jämna mellanrum. Om resultatet av frågan matchar angivna villkor, skapas en aviseringspost och en eller flera åtgärder körs med hjälp av [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Från och den 14 maj 2018 började alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta kopieras till Azure. Mer information finns i [utöka aviseringarna till Azure](../../azure-monitor/platform/alerts-extend.md). För användare som utökar aviseringar till Azure, styrs nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärdsgrupp – Azure Resource Manager-mall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Aviseringsregler i en hanteringslösning består av följande tre olika resurser.

- **Sparad sökning.** Definierar loggsökningen som körs. Flera Varningsregler kan dela en sparad sökning.
- **Schema.** Definierar hur ofta loggsökningen körs. Varje regel för varning har ett och endast ett schema.
- **Aviseringsåtgärd.** Varje regel för varning har en åtgärd gruppresurs eller åtgärd resurs (äldre) med en typ av **avisering** som definierar information om aviseringen, till exempel kriterier för när en aviseringspost skapas och aviseringens allvarlighetsgrad. [Åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) resursen kan ha en lista över konfigurerade åtgärder som ska vidtas när aviseringen utlöses – till exempel röstsamtal, SMS, e-post, webhook, ITSM-verktyg, automation-runbook, logikapp, osv.

Åtgärd-resursen (äldre) kommer du även definiera ett e-post och runbook-svar.
- **Webhook-åtgärd (äldre).** Om regeln anropar en webhook, så det krävs en ytterligare åtgärd-resurs med en typ av **Webhook**.

Sparad sökning resurser som beskrivs ovan. De övriga resurserna som beskrivs nedan.

### <a name="schedule-resource"></a>Schema-resurs

En sparad sökning kan ha ett eller flera scheman med ett schema som representerar en separat aviseringsregel. Schemat definierar hur ofta sökningen ska köras och det tidsintervall som data ska hämtas. Schemalägga resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen.

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
Egenskaper för schema resurser beskrivs i följande tabell.
| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| aktiverad       | Ja | Anger om aviseringen är aktiverad när den skapas. |
| interval      | Ja | Hur ofta frågan körs på bara några minuter. |
| queryTimeSpan | Ja | Lång tid i minuter under som du vill beräkna resultat. |
Schema-resurs bör beror på den sparade sökningen så att den har skapats innan schemat.
> [!NOTE]
> Namn på schema måste vara unika i en viss arbetsyta; två scheman kan inte ha samma ID, även om de är associerade med olika sparade sökningar. Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API måste också vara med små bokstäver.

### <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer för att utföra, till exempel skickar ett e-postmeddelande eller starta en runbook eller den kan definiera ett tröskelvärde som bestämmer när resultatet av en sökning som matchar vissa kriterier. Vissa åtgärder kommer definiera både så att processerna som utförs när tröskelvärdet är uppfyllt.
Åtgärder kan definieras med hjälp av [åtgärdsgrupp] eller åtgärd resurs.
> [!NOTE]
> Från och den 14 maj 2018 alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta som började automatiskt ska utöka till Azure. Mer information finns i [utöka aviseringarna till Azure](../../azure-monitor/platform/alerts-extend.md). För användare som utökar aviseringar till Azure, styrs nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärdsgrupp – Azure Resource Manager-mall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Det finns två typer av åtgärden resursen som anges av den **typ** egenskapen. Ett schema som kräver en **avisering** åtgärd, som definierar varningsregeln och vilka åtgärder vidtas när en avisering skapas. Åtgärden resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Aviseringsåtgärder har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen.

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

Egenskaper för Aviseringsåtgärd resurser beskrivs i följande tabeller.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| Type | Ja | Typ av åtgärd.  Det här är **avisering** för aviseringsåtgärder. |
| Namn | Ja | Visningsnamn för aviseringen.  Detta är det namn som visas i konsolen för regeln. |
| Beskrivning | Nej | Valfri beskrivning av aviseringen. |
| Severity | Ja | Allvarlighetsgrad för avisering posten bland följande värden:<br><br> **Kritiska**<br>**warning**<br>**informational**


#### <a name="threshold"></a>Tröskelvärde
Det här avsnittet är obligatoriskt. Den definierar egenskaperna för tröskelvärdet.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| Operator | Ja | Operator för jämförelse bland följande värden:<br><br>**gt = större än<br>lt = mindre än** |
| Värde | Ja | Värde att jämföra resultatet. |

##### <a name="metricstrigger"></a>MetricsTrigger
Det här avsnittet är valfritt. Tar med riskfaktorn för en avisering om metriska måttenheter.

> [!NOTE]
> Metrisk måttenhet aviseringar är för närvarande i offentlig förhandsversion.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| TriggerCondition | Ja | Anger om tröskelvärdet för totalt antal överträdelser eller efterföljande överträdelser bland följande värden:<br><br>**Totalt antal<br>i följd** |
| Operator | Ja | Operator för jämförelse bland följande värden:<br><br>**gt = större än<br>lt = mindre än** |
| Värde | Ja | Antal gånger villkoren måste vara uppfyllda för att utlösa aviseringen. |


#### <a name="throttling"></a>Begränsning
Det här avsnittet är valfritt. Inkludera det här avsnittet om du vill visa inga aviseringar från samma regel under en tidsperiod när en avisering har skapats.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| DurationInMinutes | Ja om begränsning element som ingår | Antal minuter för att visa inga aviseringar när en från samma regeln har skapats. |

#### <a name="azure-action-group"></a>Azure åtgärdsgrupp
Alla aviseringar i Azure, använda åtgärdsgrupp som standardmekanism för att hantera åtgärder. Med åtgärdsgrupp kan du ange dina åtgärder en gång och sedan associerar åtgärdsgrupp att flera aviseringar – i Azure. Utan att behöva flera gånger deklarera samma åtgärder om och om igen. Åtgärdsgrupper stöd för flera åtgärder – inklusive e-post, SMS, röstsamtal, ITSM-anslutningen, Automation-Runbook, Webhook URI med mera.

För användare som har utökat sin aviseringar i Azure – bör ett schema nu ha åtgärdsgrupp information skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. Information om e-post, Webhook-URL: er, Runbook Automation-information och andra åtgärder måste vara definierade i sida en åtgärdsgrupp innan du kan skapa en avisering; går att skapa [åtgärdsgrupp från Azure Monitor](../../azure-monitor/platform/action-groups.md) i portalen eller Använd [åtgärdsgrupp - resursmall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| AzNsNotification | Ja | Resurs-ID för Azure åtgärdsgruppen som ska associeras med aviseringen för att utföra nödvändiga åtgärder när aviseringsvillkoren uppfylls. |
| CustomEmailSubject | Nej | Anpassade ämnesraden för e-postmeddelandet som skickas till alla adresser som anges i åtgärdsgruppen associerade. |
| CustomWebhookPayload | Nej | Anpassad nyttolast som ska skickas till alla webhook-slutpunkter som definierats i associerade åtgärdsgruppen. Formatet är beroende av vad webhooken förväntar och bör vara en giltig serialiserade JSON. |

#### <a name="actions-for-oms-legacy"></a>Åtgärder för OMS (äldre)

Varje schema har en **avisering** åtgärd. Detta definierar information om aviseringen och eventuellt meddelande- och reparationsloggarna åtgärder. Ett meddelande skickas ett e-postmeddelande till en eller flera adresser. En reparation startar en runbook i Azure Automation att försöka åtgärda ett identifierat problem.

> [!NOTE]
> Från och den 14 maj 2018 alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta som började automatiskt ska utöka till Azure. Mer information finns i [utöka aviseringarna till Azure](../../azure-monitor/platform/alerts-extend.md). För användare som utökar aviseringar till Azure, styrs nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärdsgrupp – Azure Resource Manager-mall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Det här avsnittet är valfritt inkluderar den om du vill att aviseringen för att skicka e-post till en eller flera mottagare.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| Mottagare | Ja | Kommaavgränsad lista över e-postadresser för att skicka meddelande när en avisering skapas som i följande exempel.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Subjekt | Ja | Ämnesrad i e-postmeddelandet. |
| Bifogad fil | Nej | Bifogade filer stöds inte för närvarande. Om det här elementet ingår, bör den vara **ingen**. |

##### <a name="remediation"></a>Åtgärd
Det här avsnittet är valfritt inkluderar den om du vill att en runbook startas som svar på aviseringen. 

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| RunbookName | Ja | Namnet på runbook att starta. |
| WebhookUri | Ja | URI för webhook för runbooken. |
| Förfallodatum | Nej | Datum och tid då reparationen upphör att gälla. |

##### <a name="webhook-actions"></a>Webhook-åtgärder

Webhook-åtgärder kan du starta en process genom att anropa en URL och att du kan också tillhandahålla en nyttolast som ska skickas. De liknar åtgärder förutom de är avsedda för webhooks som kan anropa processer än Azure Automation-runbooks. Användaren kan även ange ytterligare alternativ för att tillhandahålla en nyttolast som ska levereras till fjärr-processen.

Om aviseringen anropar en webhook, så måste en åtgärd-resurs med en typ av **Webhook** utöver den **avisering** åtgärd resurs.

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }
Egenskaper för Webhook-åtgärd resurser beskrivs i följande tabeller.
| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| typ | Ja | Typ av åtgärd. Det här är **Webhook** för webhook-åtgärder. |
| namn | Ja | Visningsnamn för åtgärden. Detta visas inte i konsolen. |
| webhookUri | Ja | URI för webhook. |
| customPayload | Nej | Anpassad nyttolast som ska skickas till webhooken. Formatet beror på vad webhooken förväntas. |

## <a name="sample"></a>Exempel

Nedan följer ett exempel på en lösning som innehåller följande resurser:

- Sparad sökning
- Schema
- Åtgärdsgrupp

I exemplet används [standardlösning parametrar]( solutions-solution-file.md#parameters) variabler som ofta används i en lösning till skillnad från hardcoding värden i resursdefinitionerna.

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

Följande parameterfilen ger exempel värden för den här lösningen.

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
* [Lägga till vyer](solutions-resources-views.md) till din lösning.
* [Lägg till Automation-runbooks och andra resurser](solutions-resources-automation.md) till din lösning.
