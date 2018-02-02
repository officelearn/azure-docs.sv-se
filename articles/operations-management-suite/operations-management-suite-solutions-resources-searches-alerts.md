---
title: "Sparade sökningar och aviseringar i OMS-lösningar | Microsoft Docs"
description: "Lösningar i OMS inkluderar vanligtvis sparade sökningar i logganalys att analysera data som samlas in av lösningen.  De kan också definiera varningar som meddelar användaren eller automatiskt utföra åtgärder som svar på ett allvarligt problem.  Den här artikeln beskriver hur du definierar logganalys sparade sökningar och aviseringar i en Resource Manager-mall så att de kan tas med i hanteringslösningar."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e25ad9b9be6d02550b4be9c09496021cd7fe2d2
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-oms-management-solution-preview"></a>Lägga till logganalys sparade sökningar och aviseringar till OMS-hanteringslösning (förhandsgranskning)

> [!NOTE]
> Den här är dokumentationen preliminär för att skapa lösningar för hantering i OMS som för närvarande finns i förhandsgranskningen. Ett schema som beskrivs nedan kan ändras.   


[Lösningar för hantering i OMS](operations-management-suite-solutions.md) inkluderar vanligtvis [sparade sökningar](../log-analytics/log-analytics-log-searches.md) i logganalys att analysera data som samlas in av lösningen.  De kan också definiera [aviseringar](../log-analytics/log-analytics-alerts.md) att meddela användaren eller automatiskt utföra åtgärder som svar på ett allvarligt problem.  Den här artikeln beskriver hur du definierar logganalys sparade sökningar och aviseringar i en [resurshantering mallen](../resource-manager-template-walkthrough.md) så att de kan tas med i [hanteringslösningar](operations-management-suite-solutions-creating.md).

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [och skapa lösningar för hantering i Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)  

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med [skapar en lösning för](operations-management-suite-solutions-creating.md) och strukturen för en [Resource Manager-mall](../resource-group-authoring-templates.md) och lösningsfilen.


## <a name="log-analytics-workspace"></a>Log Analytics Workspace
Alla resurser i logganalys finns i en [arbetsytan](../log-analytics/log-analytics-manage-access.md).  Enligt beskrivningen i [OMS arbetsytan och Automation-konto](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account), arbetsytan ingår inte i hanteringslösningen men det måste finnas innan lösningen är installerad.  Om den inte är tillgänglig misslyckas lösning installationen.

Namnet på arbetsytan är namnet på varje logganalys-resurs.  Detta görs i lösningen med den **arbetsytan** parameter som i följande exempel på en savedsearch resurs.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-version
Alla logganalys-resurser som definierats i en Resource Manager-mall har en egenskap **apiVersion** som definierar versionen av resursen ska använda API: et.  Den här versionen är olika för resurser som använder den [äldre och uppgraderade frågespråket](../log-analytics/log-analytics-log-search-upgrade.md).  

 I följande tabell anger Log Analytics API-versioner för sparade sökningar i äldre och uppgraderade arbetsytor: 

| Arbetsyteversion | API-version | Fråga |
|:---|:---|:---|
| V1 (äldre)   | 2015-11-01-preview | Äldre format.<br> Exempel: Skriv = händelse EventLevelName = fel  |
| v2 (uppgraderade) | 2015-11-01-preview | Äldre format.  Konvertera till uppgraderade format på installera.<br> Exempel: Skriv = händelse EventLevelName = fel<br>Konvertera till: händelsen &#124; där EventLevelName == ”Error”  |
| v2 (uppgraderade) | 2017-03-03-preview | Uppgradera format. <br>Exempel: Händelsen &#124; där EventLevelName == ”Error”  |



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

| Egenskap | Beskrivning |
|:--- |:--- |
| category | Kategorin för den sparade sökningen.  Alla sparade sökningar i samma lösning kommer ofta att dela en enda kategori så att de grupperas tillsammans i konsolen. |
| displayname | Namnet som visas för den sparade sökningen i portalen. |
| DocumentDB | Frågan ska köras. |

> [!NOTE]
> Du kan behöva använda escape-tecken i fråga om den innehåller tecken som kan tolkas som JSON.  Om din fråga var till exempel **typ: AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write”**, bör vara skriven i lösningsfilen som **typ: AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Aviseringar
[Logga Analytics varningar](../log-analytics/log-analytics-alerts.md) skapas av Varningsregler som kör en sparad sökning med regelbundna intervall.  Om resultatet av frågan matchar de angivna villkoren, skapas en avisering post och en eller flera åtgärder körs.  

Varningsregler i en hanteringslösning består av följande tre olika resurser.

- **Sparad sökning.**  Definierar loggen sökningen som körs.  Flera Varningsregler kan dela en sparad sökning.
- **Schema.**  Definierar hur ofta loggen sökningen ska köras.  Varje regel för varning har ett och endast ett schema.
- **Åtgärd för aviseringen.**  Varje regel för varning har en åtgärd resurs med en typ av **avisering** som definierar information om aviseringen, till exempel kriterier för när en avisering post har skapats och att beakta aviseringens allvarlighetsgrad.  Åtgärden resursen kommer du även definiera ett e-post och runbook-svar.
- **Webhook-åtgärd (valfritt).**  Om regeln anropar en webhook, så det krävs en ytterligare åtgärd resurs med en typ av **Webhook**.    

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


### <a name="actions"></a>Åtgärder
Det finns två typer av åtgärden resursen som anges av den **typen** egenskapen.  Ett schema kräver en **avisering** åtgärd som definierar varningsregeln och vilka åtgärder som vidtas när en avisering skapas.  Det kan också innehålla en **Webhook** åtgärd om en webhook ska anropas från aviseringen.  

Åtgärden resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

#### <a name="alert-actions"></a>Aviseringsåtgärder

Varje schemat har en **avisering** åtgärd.  Detta definierar information om aviseringen och eventuellt meddelande- och reparationsloggarna åtgärder.  Ett meddelande skickas ett e-postmeddelande till en eller flera adresser.  En startar en runbook i Azure Automation för att försöka åtgärda identifierade problem.

Aviseringsåtgärder har följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 



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
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

Egenskaper för Aviseringsåtgärd resurser beskrivs i följande tabeller.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| Typ | Ja | Typ av åtgärd.  Detta är **avisering** för aviseringsåtgärder. |
| Namn | Ja | Visningsnamn för aviseringen.  Detta är det namn som visas i konsolen för regeln. |
| Beskrivning | Nej | Valfri beskrivning av aviseringen. |
| Allvarsgrad | Ja | Allvarlighetsgrad för aviseringen posten från följande värden:<br><br> **Kritiska**<br>**Varning**<br>**Informational** |


##### <a name="threshold"></a>Tröskelvärde
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

##### <a name="throttling"></a>Begränsning
Det här avsnittet är valfritt.  Inkludera det här avsnittet om du vill undertrycka aviseringar från samma regel för vissa tidsperiod när en avisering har skapats.

| Elementnamn | Krävs | Beskrivning |
|:--|:--|:--|
| DurationInMinutes | Ja om begränsning element som ingår | Antal minuter för att undertrycka aviseringar när en från samma varningsregeln har skapats. |

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

#### <a name="webhook-actions"></a>Webhook-åtgärder

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
| customPayload | Nej | Anpassad nyttolast skickas till webhooken. Formatet beror på vad webhooken förväntas. |




## <a name="sample"></a>Exempel

Följande är ett exempel på en lösning som omfattar som innehåller följande resurser:

- Sparad sökning
- Schema
- Aviseringsåtgärden
- Webhook-åtgärd

Används [standardlösningen parametrar](operations-management-suite-solutions-solution-file.md#parameters) variabler som ofta används i en lösning i stället för hardcoding värden i resursdefinitionerna.

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
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
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
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


Följande parameterfilen innehåller exempel värden för den här lösningen.

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
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>Nästa steg
* [Lägga till vyer](operations-management-suite-solutions-resources-views.md) att din lösning för hantering.
* [Lägg till Automation-runbooks och andra resurser](operations-management-suite-solutions-resources-automation.md) att din lösning för hantering.

