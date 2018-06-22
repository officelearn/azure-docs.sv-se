---
title: Sparade sökningar och aviseringar i hanteringslösningar | Microsoft Docs
description: Hanteringslösningar inkluderar vanligtvis sparade sökningar i logganalys att analysera data som samlas in av lösningen.  De kan också definiera varningar som meddelar användaren eller automatiskt utföra åtgärder som svar på ett allvarligt problem.  Den här artikeln beskriver hur du definierar logganalys sparade sökningar och aviseringar i en Resource Manager-mall så att de kan tas med i hanteringslösningar.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/18/2018
ms.author: bwren, vinagara
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c29d6cb0da2e394912a2584b0d3c3cedf13f054c
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304488"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Lägga till logganalys sparade sökningar och aviseringar för lösning (förhandsgranskning)

> [!NOTE]
> Den här är dokumentationen preliminär för att skapa lösningar som för närvarande finns i förhandsgranskningen. Ett schema som beskrivs nedan kan ändras.   


[Hanteringslösningar](monitoring-solutions.md) inkluderar vanligtvis [sparade sökningar](../log-analytics/log-analytics-log-searches.md) i logganalys att analysera data som samlas in av lösningen.  De kan också definiera [aviseringar](../log-analytics/log-analytics-alerts.md) att meddela användaren eller automatiskt utföra åtgärder som svar på ett allvarligt problem.  Den här artikeln beskriver hur du definierar logganalys sparade sökningar och aviseringar i en [resurshantering mallen](../resource-manager-template-walkthrough.md) så att de kan tas med i [hanteringslösningar](monitoring-solutions-creating.md).

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [utforma och skapa en lösning i Azure](monitoring-solutions-creating.md)  

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med [skapar en lösning för](monitoring-solutions-creating.md) och strukturen för en [Resource Manager-mall](../resource-group-authoring-templates.md) och lösningsfilen.


## <a name="log-analytics-workspace"></a>Log Analytics Workspace
Alla resurser i logganalys finns i en [arbetsytan](../log-analytics/log-analytics-manage-access.md).  Enligt beskrivningen i [logganalys-arbetsytan och Automation-konto](monitoring-solutions.md#log-analytics-workspace-and-automation-account), arbetsytan ingår inte i hanteringslösningen men det måste finnas innan lösningen är installerad.  Om den inte är tillgänglig misslyckas lösning installationen.

Namnet på arbetsytan är namnet på varje logganalys-resurs.  Detta görs i lösningen med den **arbetsytan** parameter som i följande exempel på en SavedSearch resurs.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-version
Alla logganalys-resurser som definierats i en Resource Manager-mall har en egenskap **apiVersion** som definierar versionen av resursen ska använda API: et.   

I följande tabell visas API-version för den resurs som används i det här exemplet.

| Resurstyp | API-version | Fråga |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Händelsen &#124; där EventLevelName == ”Error”  |


## <a name="saved-searches"></a>Sparade sökningar
Inkludera [sparade sökningar](../log-analytics/log-analytics-log-searches.md) i en lösning för att tillåta användare att fråga efter data som samlas in av din lösning.  Sparade sökningar visas under **Favoriter** i OMS-portalen och **sparade sökningar** i Azure-portalen.  En sparad sökning krävs också för varje avisering.   

[Logganalys sparad sökning](../log-analytics/log-analytics-log-searches.md) resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches` och har följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 

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

| Egenskap  | Beskrivning |
|:--- |:--- |
| category | Kategorin för den sparade sökningen.  Alla sparade sökningar i samma lösning kommer ofta att dela en enda kategori så att de grupperas tillsammans i konsolen. |
| visningsnamn | Namnet som visas för den sparade sökningen i portalen. |
| DocumentDB | Frågan ska köras. |

> [!NOTE]
> Du kan behöva använda escape-tecken i fråga om den innehåller tecken som kan tolkas som JSON.  Om din fråga var till exempel **typ: AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write”**, bör vara skriven i lösningsfilen som **typ: AzureActivity OperationName:\" Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Aviseringar
[Azure Log aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) har skapats med Azure Varningsregler som kör angivna frågor med jämna mellanrum.  Om resultatet av frågan uppfyller angivna villkor, skapas en avisering post och en eller flera åtgärder körs med hjälp av [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md).  

> [!NOTE]
> Från den 14 maj 2018 startar alla aviseringar i en arbetsyta automatiskt utökas till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). För användare som utökar aviseringar till Azure, kontrolleras nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [grupp - Azure Resource Manager-mall](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

Varningsregler i en hanteringslösning består av följande tre olika resurser.

- **Sparad sökning.**  Definierar loggen sökningen som körs.  Flera Varningsregler kan dela en sparad sökning.
- **Schema.**  Definierar hur ofta loggen sökningen ska köras.  Varje regel för varning har ett och endast ett schema.
- **Åtgärd för aviseringen.**  Varje regel för varning har en åtgärd grupp eller åtgärd resurs (äldre) med en typ av **avisering** som definierar information om aviseringen, till exempel kriterier för när en avisering post har skapats och att beakta aviseringens allvarlighetsgrad. [Grupp](../monitoring-and-diagnostics/monitoring-action-groups.md) resurs kan ha en lista över konfigurerade åtgärder som ska vidtas när aviseringen utlöses – till exempel röstsamtal, SMS, e-post, webhook, ITSM verktyget, automation-runbook, logikapp, osv.
 
Resursen åtgärd (äldre) definierar du ett e-post och runbook-svar.
- **Webhook-åtgärd (äldre).**  Om regeln anropar en webhook, så det krävs en ytterligare åtgärd resurs med en typ av **Webhook**.    

Sparad sökning resurser som beskrivs ovan.  De andra resurserna beskrivs nedan.


### <a name="schedule-resource"></a>Schema för resurs

En sparad sökning kan ha en eller flera scheman med ett schema som representerar en separat avisering regel. Schemat definierar hur ofta sökningen ska köras och det tidsintervall under vilken data ska hämtas.  Schemalägga resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` och har följande struktur. Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 


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



I följande tabell beskrivs egenskaperna för schemalägga resurser.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| aktiverad       | Ja | Anger om aviseringen är aktiverad när den skapas. |
| interval      | Ja | Hur ofta frågan körs i minuter. |
| QueryTimeSpan | Ja | Lång tid i minuter som ska utvärdera resultaten över. |

Resursen schema ska beror på den sparade sökningen så att den har skapats innan schemat.

> [!NOTE]
> Namn på schema måste vara unikt för en viss arbetsyta; två scheman kan inte ha samma ID, även om de är kopplade till olika sparade sökningar. Namn för alla sparade sökningar, scheman och åtgärder som skapats med API: et för Log Analytics måste också vara i gemener.


### <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer för att utföra som skickar ett e-post eller starta en runbook eller den kan definiera ett tröskelvärde som bestämmer när resultatet av en sökning som matchar vissa villkor.  Vissa åtgärder definiera både så att processerna som utförs när tröskelvärdet är uppfyllda.

Åtgärder kan definieras med hjälp av [grupp] eller åtgärd resurs.

> [!NOTE]
> Från den 14 maj 2018 startar alla aviseringar i en arbetsyta automatiskt utökas till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). För användare som utökar aviseringar till Azure, kontrolleras nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [grupp - Azure Resource Manager-mall](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).


Det finns två typer av åtgärden resursen som anges av den **typen** egenskapen.  Ett schema kräver en **avisering** åtgärd som definierar varningsregeln och vilka åtgärder som vidtas när en avisering skapas. Åtgärden resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

Aviseringsåtgärder har följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 


```
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
| Typ | Ja | Typ av åtgärd.  Detta är **avisering** för aviseringsåtgärder. |
| Namn | Ja | Visningsnamn för aviseringen.  Detta är det namn som visas i konsolen för regeln. |
| Beskrivning | Nej | Valfri beskrivning av aviseringen. |
| Severity | Ja | Allvarlighetsgrad för aviseringen posten från följande värden:<br><br> **kritiska**<br>**Varning**<br>**Information**


#### <a name="threshold"></a>Tröskelvärde
Det här avsnittet krävs.  Den definierar egenskaperna för tröskelvärde.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| Operator | Ja | Operator för jämförelse från följande värden:<br><br>**gt = större än<br>lt = mindre än** |
| Värde | Ja | Värde att jämföra resultatet. |

##### <a name="metricstrigger"></a>MetricsTrigger
Det här avsnittet är valfritt.  Inkludera det för ett mått mätning aviseringen.

> [!NOTE]
> Mått mätning aviseringar är för närvarande i förhandsversion. 

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| TriggerCondition | Ja | Anger om tröskelvärdet för totala antalet överträdelser eller på varandra följande överträdelser från följande värden:<br><br>**Totalt antal<br>i följd** |
| Operator | Ja | Operator för jämförelse från följande värden:<br><br>**gt = större än<br>lt = mindre än** |
| Värde | Ja | Antal gånger som villkoret måste uppfyllas för att utlösa en avisering. |


#### <a name="throttling"></a>Begränsning
Det här avsnittet är valfritt.  Inkludera det här avsnittet om du vill undertrycka aviseringar från samma regel för vissa tidsperiod när en avisering har skapats.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| DurationInMinutes | Ja om begränsning element som ingår | Antal minuter för att undertrycka aviseringar när en från samma varningsregeln har skapats. |


#### <a name="azure-action-group"></a>Azure grupp
Alla aviseringar i Azure, använda åtgärden gruppen standardmekanism för hantering av åtgärder. Med åtgärden grupp du ange dina åtgärder en gång och sedan associerar åtgärdsgruppen till flera aviseringar - i Azure. Utan att behöva, upprepade gånger deklarera samma åtgärder upprepade gånger. Åtgärdsgrupper har stöd för flera åtgärder – inklusive e-post, SMS, röst anropa, ITSM anslutning, Automation-Runbook, Webhook URI och mycket mer. 

För användare som har utökat sina aviseringar i Azure - bör ett schema nu ha grupp information skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. Postinformation om e-, Webhook URL: er, Runbook Automation-information och andra åtgärder måste definieras på sidan av en grupp innan du kan skapa en avisering; går att skapa [grupp från Azure-Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) på portalen eller Använd [grupp - Resource-mallen](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| AzNsNotification | Ja | Resurs-ID för åtgärdsgruppen Azure som ska associeras med aviseringen för att utföra nödvändiga åtgärder när aviseringsvillkoren uppfylls. |
| CustomEmailSubject | Nej | Anpassade ämnesraden för e-postmeddelandet som skickas till alla adresser som anges i grupp relaterade åtgärder. |
| CustomWebhookPayload | Nej | Anpassad nyttolast skickas till alla webhook-slutpunkter som definierats i gruppen relaterade åtgärder. Formatet beror på vad webhooken förväntar sig och ska vara en giltig serialiserade JSON. |


#### <a name="actions-for-oms-legacy"></a>Åtgärder för OMS (bakåtkompatibelt)

Varje schemat har en **avisering** åtgärd.  Detta definierar information om aviseringen och eventuellt meddelande- och reparationsloggarna åtgärder.  Ett meddelande skickas ett e-postmeddelande till en eller flera adresser.  En startar en runbook i Azure Automation för att försöka åtgärda identifierade problem.

> [!NOTE]
> Från den 14 maj 2018 startar alla aviseringar i en arbetsyta automatiskt utökas till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). För användare som utökar aviseringar till Azure, kontrolleras nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [grupp - Azure Resource Manager-mall](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Det här avsnittet är valfritt att inkludera det om du vill att aviseringen ska skicka e-post till en eller flera mottagare.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| Mottagare | Ja | Kommaavgränsad lista över e-postadresser för att skicka en avisering när en avisering skapas som i följande exempel.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Ämne | Ja | Ämnesrad i e-postmeddelandet. |
| Bifogad fil | Nej | Bifogade filer stöds inte för närvarande.  Om det här elementet finns det ska vara **ingen**. |


##### <a name="remediation"></a>Åtgärd
Det här avsnittet är valfritt att inkludera det om du vill att en runbook att starta som svar på aviseringen. |

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| RunbookName | Ja | Namnet på runbook att starta. |
| WebhookUri | Ja | URI för webhook för runbook. |
| Förfallodatum | Nej | Datum och tid då reparationen upphör att gälla. |

##### <a name="webhook-actions"></a>Webhook-åtgärder

Webhook-åtgärder kan du starta en process genom att anropa en URL och du kan också tillhandahålla en nyttolast som ska skickas. De liknar reparationsåtgärder förutom de är avsedda för webhooks som kan anropa processer än Azure Automation-runbooks. De ger också ytterligare alternativ för att tillhandahålla en nyttolast som ska levereras till fjärrprocessen.

Om aviseringen ska anropa en webhook, så den behöver en åtgärd resurs med en typ av **Webhook** förutom den **avisering** åtgärd resurs.  

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

Egenskaper för Webhook åtgärd resurser beskrivs i följande tabeller.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| typ | Ja | Typ av åtgärd.  Detta är **Webhook** för webhook-åtgärder. |
| namn | Ja | Visningsnamn för åtgärden.  Detta visas inte i konsolen. |
| wehookUri | Ja | URI för webhooken. |
| CustomPayload | Nej | Anpassad nyttolast skickas till webhooken. Formatet beror på vad webhooken förväntas. |


## <a name="sample"></a>Exempel

Följande är ett exempel på en lösning som omfattar som innehåller följande resurser:

- Sparad sökning
- Schema
- Åtgärdsgrupp

Används [standardlösningen parametrar]( monitoring-solutions-solution-file.md#parameters) variabler som ofta används i en lösning i stället för hardcoding värden i resursdefinitionerna.

```
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
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
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
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
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

Följande parameterfilen innehåller exempel värden för den här lösningen.
```
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
* [Lägga till vyer](monitoring-solutions-resources-views.md) att din lösning för hantering.
* [Lägg till Automation-runbooks och andra resurser](monitoring-solutions-resources-automation.md) att din lösning för hantering.

