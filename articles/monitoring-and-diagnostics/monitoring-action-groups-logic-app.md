---
title: Hur du utlöser komplexa åtgärder med Azure övervaka aviseringar och åtgärdsgrupper
description: Lär dig hur du skapar en logik app åtgärd för att bearbeta Azure övervaka aviseringar.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 14e562234152d2f1f2f2d2b57b34cd5724d3dd14
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753101"
---
# <a name="create-a-logic-app-action"></a>Skapa en app logik-åtgärd

Den här artikeln visar hur du ställer in och utlösa en logikapp för att skapa en konversation i Microsoft Teams när en avisering utlöses.

## <a name="overview"></a>Översikt
När en Azure-Monitor alert utlöser anropar en [grupp](monitoring-action-groups.md). Åtgärdsgrupper kan du aktivera en eller flera åtgärder för att meddela andra om en avisering och åtgärdas också.

Den allmänna processen är:

-   Skapa logikappen för respektive typ av avisering.

-   Importera schemat för respektive aviseringstypen till logikappen.

-   Definiera logic Apps beteende.

-   Kopiera HTTP-slutpunkten för logikappen till en Azure grupp.

Processen påminner om du vill logikappen att utföra olika åtgärder.

## <a name="create-an-activity-log-alert-administrative"></a>Skapa en aktivitet loggen avisering: administrativa

1.  Välj i Azure-portalen **skapar du en resurs** i det övre vänstra hörnet.

2.  Söka efter och välja **Logikapp**och välj **skapa**.

3.  Ge din logikapp en **namn**, Välj en **resursgruppen**och så vidare.

    ![Skapa en logikapp](media/monitoring-action-groups/create-logic-app-dialog.png "skapa en logikapp")

4.  Välj **skapa** att skapa logikappen. Ett popup-meddelande som anger att logikappen har skapats. Välj **starta resurs** att öppna den **Logic Apps Designer**.

5.  Välj utlösaren: **när en HTTP-begäran tas emot**.

    ![Logik app utlösare](media/monitoring-action-groups/logic-app-triggers.png "logik app utlösare")

6.  Välj **redigera** att ändra utlösaren för HTTP-begäran.

    ![HTTP-begäran utlösare](media/monitoring-action-groups/http-request-trigger-shape.png "utlösare för HTTP-begäran")

7.  Välj **Generera schemat genom att använda en exempelnyttolast**.

    ![Använd en exempel-nyttolast](media/monitoring-action-groups/use-sample-payload-button.png "använder en exempel-nyttolast")

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

9. Den **logik App Designer** visar ett popup-fönster för att påminna dig om att begäran som skickades till logikappen måste ange den **Content-Type** sidhuvud till **application/json**. Stäng popup-fönster. Azure övervakningsavisering anger huvudet.

    ![Ange Content-Type-huvudet](media/monitoring-action-groups/content-type-header.png "ange Content-Type-huvudet")

10. Välj **+** **nytt steg** och välj sedan **lägga till en åtgärd**.

    ![Lägg till en åtgärd](media/monitoring-action-groups/add-action.png "lägga till en åtgärd")

11. Sök efter och välj den Microsoft-Teams kopplingen. Välj den **Microsoft Teams – skicka meddelandet** åtgärd.

    ![Åtgärder för Microsoft-Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams åtgärder")

12. Konfigurera Microsoft Teams-åtgärd. Den **Logic Apps Designer** ombedd att autentisera till Office 365-konto. Välj den **Team Id** och **Id för överföringskanal** att skicka meddelande till.

13. Konfigurera meddelandet genom att använda en kombination av statisk text och referenser till den \<fält\> i dynamiskt innehåll. Kopiera och klistra in följande text i den **meddelandet** fält:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Sök sedan efter och ersätta den \<fält\> med dynamiska innehåll taggar med samma namn.

    > [!NOTE]
    > Det finns två dynamiska fält som heter **status**. Lägga till båda fälten i meddelandet. Använd fältet i den **activityLog** egenskapsuppsättning och ta bort andra fältet. Håller markören över den **status** fältet för att se den fullständiga fältreferensen som visas i följande skärmbild:

    ![Microsoft-Teams åtgärd: skicka ett meddelande](media/monitoring-action-groups/teams-action-post-message.png "Microsoft Teams åtgärd: skicka ett meddelande")

14. Längst upp i den **Logic Apps Designer**väljer **spara** att spara din logikapp.

15. Öppna din befintliga grupp och Lägg till en åtgärd för att referera till logikappen. Om du inte har en befintlig grupp, se [skapa och hantera åtgärdsgrupper i Azure portal](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) att skapa en. Glöm inte att spara ändringarna.

    ![Uppdatera åtgärdsgruppen](media/monitoring-action-groups/update-action-group.png "Uppdateringsgrupp åtgärd")

Nästa gång en avisering anropar din grupp, kallas logikappen.

## <a name="create-a-service-health-alert"></a>Skapa en health service

Azure tjänstens hälsa poster är en del av aktivitetsloggen. Processen för att skapa aviseringen liknar [skapar en avisering för aktiviteten loggen](#create-an-activity-log-alert-administrative), men med några ändringar:

- Steg 1 till 7 är samma.
- Använd följande exempelschema för HTTP-begäran utlösaren för steg 8:

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

-  Steg 9 och 10 är samma.
-  För steg 11 till 14, använder du följande process:

   1. Välj **+** **nytt steg** och välj sedan **Lägg till ett villkor**. Ange följande villkor för att säkerställa att logikappen körs bara när indata matchar dessa värden:
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == 0.1.1`

      ![”Tjänstens hälsa nyttolast villkoret”](media/monitoring-action-groups/service-health-payload-condition.png "tjänstens hälsa nyttolast villkor")

   1. I den **om värdet är true** villkor, följer du anvisningarna i steg 11 till 13 i [skapar en avisering för aktiviteten loggen](#create-an-activity-log-alert-administrative) att lägga till Microsoft-Teams-åtgärd.

   1. Definiera meddelandet genom att använda en kombination av HTML- och dynamiskt innehåll. Kopiera och klistra in följande innehåll i den **meddelandet** fältet. Ersätt den `[incidentType]`, `[trackingID]`, `[title]`, och `[communication]` fält med dynamiska innehåll taggar med samma namn:

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

       ![”Tjänstens hälsa sant villkor efter åtgärden”](media/monitoring-action-groups/service-health-true-condition-post-action.png "tjänstens hälsa sant villkor efter åtgärd")

   1. För den **om falskt** villkor, ange ett användbart meddelande:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       ![”Tjänstens hälsa falskt villkor efter åtgärden”](media/monitoring-action-groups/service-health-false-condition-post-action.png "tjänstens hälsa falskt villkor efter åtgärd")

- Steg 15 är samma. Följ instruktionerna för att spara din logikapp och uppdatera din grupp.

## <a name="create-a-metric-alert"></a>Skapa en mått

Processen för att skapa en avisering om mått liknar [skapar en avisering för aktiviteten loggen](#create-an-activity-log-alert-administrative), men med några ändringar:

- Steg 1 till 7 är samma.
- Använd följande exempelschema för HTTP-begäran utlösaren för steg 8:

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

- Steg 9 och 10 är samma.
- För steg 11 till 14, använder du följande process:

   1. Välj **+** **nytt steg** och välj sedan **Lägg till ett villkor**. Ange följande villkor för att säkerställa att logikappen körs bara när indata matchar dessa värden:
       - `schemaId == AzureMonitorMetricAlert`
       - `version == 2.0`

       ![”Mått avisering nyttolast villkoret”](media/monitoring-action-groups/metric-alert-payload-condition.png "mått avisering nyttolast villkor")

   1. I den **om värdet är true** villkor, lägga till en **för varje** loop och Microsoft-Teams-åtgärd. Definiera meddelandet genom att använda en kombination av HTML- och dynamiskt innehåll.

       ![”Mått true varningsvillkor efter åtgärden”](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "mått true varningsvillkor efter åtgärd")

   1. I den **om falskt** villkor, definierar en Microsoft-Teams åtgärder för att kommunicera att mått aviseringen inte matchar logikappen förväntningar. Inkludera JSON-nyttolast. Observera hur du refererar till den `triggerBody` dynamiskt innehåll i den `json()` uttryck.

       ![”Mått FALSKT varningsvillkor efter åtgärden”](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "mått FALSKT varningsvillkor efter åtgärd")

- Steg 15 är samma. Följ instruktionerna för att spara din logikapp och uppdatera din grupp.

## <a name="next-steps"></a>Nästa steg
* Hämta en [översikt över Azure log Aktivitetsaviseringar](monitoring-overview-alerts.md) och lära dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurera aviseringar när ett meddelande om Azure tjänstens hälsa är bokförd](monitoring-activity-log-alerts-on-service-notifications.md).
* Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md).