---
title: Hur du utlöser komplexa åtgärder med Azure Monitor-aviseringar
description: Lär dig mer om att skapa en logic app-åtgärd för att bearbeta Azure Monitor-aviseringar.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 0ab496b884506b013c6a4abb2dff1e1feff4fc72
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342994"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Hur du utlöser komplexa åtgärder med Azure Monitor-aviseringar

Den här artikeln visar hur du konfigurerar och utlösa en logikapp för att skapa en konversation i Microsoft Teams när en avisering utlöses.

## <a name="overview"></a>Översikt
När en Azure Monitor-avisering utlöses, anropas en [åtgärdsgrupp](../../azure-monitor/platform/action-groups.md). Åtgärdsgrupper kan du utlösa en eller flera åtgärder för att meddela andra om en avisering och åtgärdas också.

Den allmänna processen är:

-   Skapa logikapp för respektive typ av avisering.

-   Importera schemat för respektive aviseringstypen till logikappen.

-   Definiera beteendet för logic app.

-   Kopiera HTTP-slutpunkten av logic app till en Azure åtgärdsgrupp.

Processen påminner om du vill att logikappen att utföra olika åtgärder.

## <a name="create-an-activity-log-alert-administrative"></a>Skapa en aktivitetsloggavisering: Administrativ

1.  I Azure-portalen väljer du **skapa en resurs** i det övre vänstra hörnet.

2.  Sök efter och välj **Logikapp**och välj sedan **skapa**.

3.  Ge din logikapp ett **namn**, Välj en **resursgrupp**och så vidare.

    ![Skapa en logikapp](media/action-groups-logic-app/create-logic-app-dialog.png "skapa en logikapp")

4.  Välj **skapa** att skapa logikappen. Ett popup-meddelande indikerar att logikappen har skapats. Välj **starta Resource** att öppna den **Logic Apps Designer**.

5.  Välj utlösaren: **När en HTTP-begäran tas emot**.

    ![Logikapputlösare](media/action-groups-logic-app/logic-app-triggers.png "utlösare för Logikappar")

6.  Välj **redigera** ändra HTTP-begäran-utlösaren.

    ![HTTP-begäran-utlösare](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-begäran-utlösare")

7.  Välj **Generera schemat genom att använda en exempelnyttolast**.

    ![Använda en exempelnyttolast](media/action-groups-logic-app/use-sample-payload-button.png "använda en exempelnyttolast")

8.  Kopiera och klistra in följande exempelschema i dialogrutan:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. Den **Logic App Designer** visas ett popup-fönster för att påminna dig om att begäran som skickats till logikappen måste ange den **Content-Type** sidhuvud till **application/json**. Stäng popup-fönstret. Azure Monitor-aviseringen anger att rubriken.

    ![Ange Content-Type-huvud](media/action-groups-logic-app/content-type-header.png "ange Content-Type-huvud")

10. Välj **+** **nytt steg** och välj sedan **Lägg till en åtgärd**.

    ![Lägg till en åtgärd](media/action-groups-logic-app/add-action.png "Lägg till en åtgärd")

11. Sök efter och välj Microsoft Teams-koppling. Välj den **Microsoft Teams – publicera meddelande** åtgärd.

    ![Microsoft Teams åtgärder](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams-åtgärder")

12. Konfigurera Microsoft Teams-åtgärd. Den **Logic Apps Designer** ber dig att autentisera till ditt Office 365-konto. Välj den **lag-ID** och **kanal-ID** att skicka meddelandet till.

13. Konfigurera meddelandet med hjälp av en kombination av statisk text och referenser till den \<fält\> i dynamiskt innehåll. Kopiera och klistra in följande text till den **meddelande** fält:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Sök sedan efter och ersätta den \<fält\> med dynamiskt innehåll taggar med samma namn.

    > [!NOTE]
    > Det finns två dynamisk fält som namnges **status**. Lägga till båda de här fälten i meddelandet. Använd fältet som finns i den **activityLog** egenskapsuppsättning och ta bort det andra fältet. Håll markören över de **status** fältet för att se den fullständigt kvalificerade fältreferensen enligt följande skärmbild:

    ![Microsoft Teams-åtgärd: Publicera ett meddelande](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams-åtgärd: Publicera ett meddelande")

14. Överst på den **Logic Apps Designer**väljer **spara** att spara din logikapp.

15. Öppna din befintliga åtgärdsgrupp och Lägg till en åtgärd för att referera till logikappen. Om du har en befintlig åtgärdsgrupp [skapa och hantera åtgärdsgrupper i Azure-portalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) att skapa en. Glöm inte att spara dina ändringar.

    ![Uppdatera åtgärdsgruppen](media/action-groups-logic-app/update-action-group.png "uppdatera åtgärdsgruppen")

Nästa gång en avisering anropar din åtgärdsgrupp kallas logikappen.

## <a name="create-a-service-health-alert"></a>Skapa en hälsoavisering för tjänst

Azure Service Health-poster är en del av aktivitetsloggen. Processen för att skapa aviseringen liknar [skapar en aktivitetsloggavisering](#create-an-activity-log-alert-administrative), men med några ändringar:

- Steg 1 till 7 är desamma.
- Steg 8, Använd följande exempelschema för HTTP-begäran-utlösare:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Steg 9 och 10 är desamma.
-  För steg 11 till 14, använder du följande process:

   1. Välj **+** **nytt steg** och välj sedan **Lägg till ett villkor**. Ange följande villkor så att logikappen körs bara när indata matchar värdena nedan.  När du anger värdet i textrutan, placera citattecknen runt den (”0.1.1”) för att se till att den utvärderas som en sträng och inte en numerisk typ.  Systemet visar inte citattecken om du går tillbaka till sidan, men den underliggande koden fortfarande underhåller strängtyp.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      ![”Service Health nyttolast villkoret”](media/action-groups-logic-app/service-health-payload-condition.png "Service Health nyttolast villkor")

   1. I den **om värdet är true** villkoret, följ instruktionerna i stegen 11 till 13 i [skapa en aktivitetsloggavisering](#create-an-activity-log-alert-administrative) att lägga till Microsoft Teams-åtgärd.

   1. Definiera meddelandet med hjälp av en kombination av HTML och dynamiskt innehåll. Kopiera och klistra in följande innehåll till den **meddelande** fält. Ersätt den `[incidentType]`, `[trackingID]`, `[title]`, och `[communication]` fält med dynamiskt innehåll taggar med samma namn:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       ![”Service Health-sant villkor åtgärd efter”](media/action-groups-logic-app/service-health-true-condition-post-action.png "åtgärd för Service Health sant villkor efter")

   1. För den **om falskt** tillstånd, kan du ange en användbar meddelande:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       ![”Service Health-falskt villkor åtgärd efter”](media/action-groups-logic-app/service-health-false-condition-post-action.png "åtgärd för Service Health falskt villkor efter")

- Steg 15 är samma. Följ anvisningarna för att spara logikappen och uppdatera din åtgärdsgruppen.

## <a name="create-a-metric-alert"></a>Skapa en måttavisering

Processen för att skapa en måttavisering liknar [skapar en aktivitetsloggavisering](#create-an-activity-log-alert-administrative), men med några ändringar:

- Steg 1 till 7 är desamma.
- Steg 8, Använd följande exempelschema för HTTP-begäran-utlösare:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

- Steg 9 och 10 är desamma.
- För steg 11 till 14, använder du följande process:

   1. Välj **+** **nytt steg** och välj sedan **Lägg till ett villkor**. Ange följande villkor så att logikappen körs bara när indata matchar värdena nedan. När du lägger till versionsvärdet i textrutan, placerar citattecknen runt den (”2.0”) till kontrollerar att utvärderas som en sträng och inte en numerisk typ.  Systemet visar inte citattecken om du går tillbaka till sidan, men den underliggande koden fortfarande underhåller strängtyp. 
       - `schemaId == AzureMonitorMetricAlert`
       - `version == "2.0"`
       
       ![”Metrisk varning nyttolast villkoret”](media/action-groups-logic-app/metric-alert-payload-condition.png "metrisk varning nyttolast villkor")

   1. I den **om värdet är true** villkoret, lägga till en **för varje** loop och Microsoft Teams-åtgärd. Definiera meddelandet med hjälp av en kombination av HTML och dynamiskt innehåll.

       ![”Åtgärd metrisk varning sant villkor efter”](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "åtgärd för metrisk varning sant villkor efter")

   1. I den **om falskt** villkoret, definiera en Microsoft Teams-åtgärd för att kommunicera att metrisk varning inte matchar kraven för logikappen. Innehåller JSON-nyttolast. Lägg märke till hur du refererar till den `triggerBody` dynamiskt innehåll i den `json()` uttryck.

       ![”Åtgärd metrisk varning falskt villkor efter”](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "åtgärd för metrisk varning falskt villkor efter")

- Steg 15 är samma. Följ anvisningarna för att spara logikappen och uppdatera din åtgärdsgruppen.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Anropa andra program än Microsoft Teams
Logic Apps har ett antal olika anslutningar som gör det möjligt att utlösaråtgärder i ett brett utbud av program och databaser. Slack, SQL Server, Oracle, Salesforce, är några exempel. Mer information om anslutningsappar finns i [Logikapp anslutningsappar](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Nästa steg
* Hämta en [översikt över Azure aktivitetsloggaviseringar](../../azure-monitor/platform/alerts-overview.md) och lär dig hur du får aviseringar.  
* Lär dig hur du [konfigurera aviseringar när ett meddelande om Azure Service Health publiceras](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).
