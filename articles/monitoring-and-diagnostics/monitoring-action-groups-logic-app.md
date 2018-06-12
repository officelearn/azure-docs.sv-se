---
title: Hur du utlöser komplexa åtgärder med Azure övervaka aviseringar och åtgärder grupper
description: Lär dig hur du skapar en Logikapp åtgärd att bearbeta Azure övervaka aviseringar.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263892"
---
# <a name="create-a-logic-app-action"></a>Skapa en Logikapp-åtgärd
## <a name="overview"></a>Översikt ##
När en Azure-Monitor alert utlöser anropar en [grupp](monitoring-action-groups.md). Åtgärdsgrupper kan du utlösa en eller flera åtgärder för att meddela personer för aviseringen och även åtgärdas.

Den här artikeln visar hur du ställer in och utlösa en Logikapp för att skapa en konversation i Microsoft Teams när en avisering utlöses.

Den allmänna processen är:

-   Skapa Logikapp för respektive typ av avisering

-   Importera schemat för respektive aviseringstypen till Logikappen

-   Definiera beteendet för Logikapp

-   Kopiera den Logikapp HTTP-slutpunkt till en Azure grupp

Processen påminner om du vill Logikappen att utföra olika åtgärder.

## <a name="create-an-activity-log-alert--administrative"></a>Skapa en aktivitet loggen avisering – administrativa

1.  Klicka på knappen **Skapa en resurs** längst upp till vänster i Azure Portal.

2.  Sök efter och välj **Logikapp**. Klicka på knappen **Skapa**.

3.  Namnge din Logikapp, Välj en resursgrupp, osv.

    ![Skapa logik app dialogrutan](media/monitoring-action-groups/create-logic-app-dialog.png "skapa logik app dialogrutan")

4.  Klicka på Skapa om du vill skapa Logikappen. Ett popup-fönster visas när Logikappen har skapats. Klicka på Starta resurs för att öppna designern Logic Apps.

5.  Välj utlösaren **när en HTTP-begäran tas emot**.

    ![Logik App utlösare](media/monitoring-action-groups/logic-app-triggers.png "logik App utlösare")

6.  Välj **redigera** att ändra utlösaren för HTTP-begäran

    ![HTTP-begäran utlösaren formen](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP-begäran utlösaren form")

7.  Välj **Generera schemat genom att använda en exempelnyttolast**.

    ![Använda exemplet nyttolast knappen](media/monitoring-action-groups/use-sample-payload-button.png "Använd nyttolast OK")

8.  Kopiera och klistra in följande exempelschema i dialogrutan.

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

9. Logik App Designer ska visa en anteckning påminna dig om att begäran som skickades till Logikappen måste ange Content-Type-huvudet till application/json. Gå vidare och stänga dialogrutan. Azure övervakningsavisering ska göra detta på rätt sätt.

    ![Content-Type-huvud](media/monitoring-action-groups/content-type-header.png "Content-Type-huvud")

10. Välj **+ nytt steg** och välj sedan **lägga till en åtgärd**.

    ![Lägg till åtgärd](media/monitoring-action-groups/add-action.png "Lägg till åtgärd")

11. Sök efter och välj den Microsoft-Teams kopplingen. Välj den **Microsoft Teams – skicka meddelandet** åtgärd.

    ![Åtgärder för Microsoft-Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams åtgärder")

12. Konfigurera Microsoft Teams-åtgärd. Logic Apps Designer blir du ombedd att autentisera till ditt Office 365-konto. Välj den **Team Id** och **Id för överföringskanal** att skicka meddelande till.

13. Konfigurera den **meddelandet** med hjälp av en kombination av statisk text och referenser till den \<fält\> i dynamiskt innehåll. Klipp ut och klistra in följande text i fältet meddelande.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Sök sedan efter och ersätta den \<fält\> med dynamiska innehåll taggar med samma namn.

    **[OBS!]**  Det finns två dynamiska fält med namnet **status**. Lägg till båda status i meddelandet. Använda en i egenskapsuppsättningen activityLog och ta bort den andra. Om du håller muspekaren över den **status** fältet visas fullständigt kvalificerade fältreferensen som visas i skärmbilden

    ![Team åtgärd - skicka meddelandet](media/monitoring-action-groups/teams-action-post-message.png "team åtgärd - skicka meddelandet")

14. Spara din Logikapp genom att klicka på knappen Spara längst upp i Designer

15. Klicka på formen HTTP-begäran kan orsaka att utöka. Kopiera HTTP POST-URL.

    ![HTTP POST URL](media/monitoring-action-groups/http-post-url.png "HTTP POST-URL")

16. Öppna din befintliga grupp och Lägg till en åtgärd för att referera till Logikappen. Om du inte har en befintlig grupp finns <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> att skapa en. Glöm inte att spara ändringarna.

    ![Åtgärden Uppdateringsgrupp](media/monitoring-action-groups/update-action-group.png "åtgärd Uppdateringsgrupp")

Nästa gång en avisering anropar din grupp, kallas Logikappen.

## <a name="create-a-service-health-alert"></a>Skapa en avisering om tjänstens hälsa

Tjänsten hälsa poster är en del av aktivitetsloggen, så processen påminner om med följande ändringar

1.  Steg 1 till 7 är samma.
2.  Använd följande exempelschema för HTTP-utlösaren i steg 8.

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

3.  Steg 9 – 10 är samma.
4.  Använd anvisningarna nedan från steg 11 på.
5.  Klicka på den **+ nytt steg** knappen och välj **Lägg till ett villkor**. Ange följande villkor för att säkerställa att Logikappen endast körs när indata matchar dessa värden
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    ![”Tjänst nyttolast hälsostatus”](media/monitoring-action-groups/service-health-payload-condition.png "tjänsten hälsostatus för nyttolast")

6. I den **om värdet är true** villkor, lägga till Microsoft-Teams åtgärden med hjälp av stegen 11-13 från föregående exempel.

7. Definiera meddelandet med en kombination av HTML- och [dynamiskt innehåll]. Kopiera och klistra in innehållet nedan i fältet meddelande. Ersätt [incidentType], [trackingID], [Rubrik] och [kommunikation] fält med dynamiska innehåll taggar med samma namn

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    ![”Tjänst hälsa sant villkor efter åtgärden”](media/monitoring-action-groups/service-health-true-condition-post-action.png "tjänsten hälsa sant villkor efter åtgärd")

8. För den **om falskt** villkoret ge ett användbart meddelande

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    ![”Tjänstens hälsa falskt villkor efter åtgärden”](media/monitoring-action-groups/service-health-false-condition-post-action.png "tjänstens hälsa falskt villkor efter åtgärd")

9.  Följ steg 15 – 16 i föregående exempel spara din Logikapp och uppdatera din grupp

## <a name="metric-alert"></a>Metrisk varning

1.  Steg 1 till 7 är samma som det första exemplet
2.  Använd följande exempelschema för HTTP-utlösaren i steg 8.

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

3.  Steg 9 – 10 är samma.
4.  Använd anvisningarna nedan från steg 11 på.
5.  Klicka på den **+ nytt steg** knappen och välj **Lägg till ett villkor**. Ange följande villkor för att säkerställa att Logikappen endast körs när indata matchar dessa värden

    - schemaId == AzureMonitorMetricAlert
    - version 2.0 ==

    ![”Mått avisering nyttolast villkoret”](media/monitoring-action-groups/metric-alert-payload-condition.png "mått avisering nyttolast villkor")

6.  I den **om värdet är true** villkoret som vi lägger till en **för varje** form och åtgärden Microsoft Teams och definiera meddelandet med en kombination av HTML- och [dynamiskt innehåll]

    ![”Mått true varningsvillkor efter åtgärden”](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "mått true varningsvillkor efter åtgärd")

7.  I den **om falskt** form, definierar en Microsoft-Teams åtgärder som kommunicerar att mått-avisering inte matchar den Logikapp förväntningar och inkluderar JSON-nyttolast. Observera hur vi refererar triggerBody dynamiskt innehåll i json() uttrycket.

    ![”Mått FALSKT varningsvillkor efter åtgärden”](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "mått FALSKT varningsvillkor efter åtgärd")

8.  Följ steg 15 – 16 för det första exemplet spara din Logikapp och uppdatera din grupp

## <a name="next-steps"></a>Nästa steg ##
* Hämta en [översikt över aktivitet loggen aviseringar](monitoring-overview-alerts.md), och lära dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurera aviseringar när ett meddelande om tjänstens hälsa är bokförd](monitoring-activity-log-alerts-on-service-notifications.md).
* Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md)