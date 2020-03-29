---
title: Utlösa komplexa åtgärder med Azure Monitor-aviseringar
description: Lär dig hur du skapar en logikappåtgärd för att bearbeta Azure Monitor-aviseringar.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: 655a3acc44a1418778b37fbef85e5df75d042317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206244"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Utlösa komplexa åtgärder med Azure Monitor-aviseringar

I den här artikeln visas hur du konfigurerar och utlöser en logikapp för att skapa en konversation i Microsoft Teams när en avisering utlöses.

## <a name="overview"></a>Översikt

När en Azure Monitor-avisering utlöses anropas en [åtgärdsgrupp](../../azure-monitor/platform/action-groups.md). Med åtgärdsgrupper kan du utlösa en eller flera åtgärder för att meddela andra om en avisering och även åtgärda den.

Den allmänna processen är:

-   Skapa logikappen för respektive aviseringstyp.

-   Importera en exempelnyttolast för respektive aviseringstyp till logikappen.

-   Definiera logikappens beteende.

-   Kopiera HTTP-slutpunkten för logikappen till en Azure-åtgärdsgrupp.

Processen är liknande om du vill att logikappen ska utföra en annan åtgärd.

## <a name="create-an-activity-log-alert-administrative"></a>Skapa en aktivitetsloggavisering: Administrativ

1.  I Azure-portalen väljer du **Skapa en resurs** i det övre vänstra hörnet.

2.  Sök efter och välj **Logic App**och välj sedan **Skapa**.

3.  Ge logikappen ett **namn,** välj en **resursgrupp**och så vidare.

    ![Skapa en logikapp](media/action-groups-logic-app/create-logic-app-dialog.png "Skapa en logikapp")

4.  Välj **Skapa** om du vill skapa logikappen. Ett popup-meddelande anger att logikappen har skapats. Välj **Starta resurs** för att öppna Logic Apps **Designer**.

5.  Välj utlösaren: **När en HTTP-begäran tas emot**.

    ![Utlösare för logikappar](media/action-groups-logic-app/logic-app-triggers.png "Utlösare för logikappar")

6.  Välj **Redigera** om du vill ändra HTTP-begärandens utlösare.

    ![HTTP-begäran triggers](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-begäran triggers")

7.  Välj **Generera schemat genom att använda en exempelnyttolast**.

    ![Använda en nyttolast för exempel](media/action-groups-logic-app/use-sample-payload-button.png "Använda en nyttolast för exempel")

8.  Kopiera och klistra in följande exempelnyttolaster i dialogrutan:

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

9. **Logic App Designer** visar ett popup-fönster för att påminna dig om att begäran som skickas till logikappen måste ange **content-type-huvudet** till **program/json**. Stäng popup-fönstret. Azure Monitor-aviseringen anger huvudet.

    ![Ange innehållstypsrubrik](media/action-groups-logic-app/content-type-header.png "Ange innehållstypsrubrik")

10. Välj **+** **Nytt steg** och välj sedan Lägg till en **åtgärd**.

    ![Lägga till en åtgärd](media/action-groups-logic-app/add-action.png "Lägga till en åtgärd")

11. Sök efter och välj Microsoft Teams-kopplingen. Välj åtgärden **Microsoft Teams - Skicka meddelande.**

    ![Åtgärder i Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Åtgärder i Microsoft Teams")

12. Konfigurera åtgärden Microsoft Teams. **Logic Apps Designer** ber dig att autentisera till ditt Office 365-konto. Välj **team-ID** och **Kanal-ID** att skicka meddelandet till.

13. Konfigurera meddelandet med hjälp av en kombination av \<\> statisk text och referenser till fälten i det dynamiska innehållet. Kopiera och klistra in följande text i fältet **Meddelande:**

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Sök sedan efter \<och\> ersätt fälten med dynamiska innehållstaggar med samma namn.

    > [!NOTE]
    > Det finns två dynamiska fält med namnet **status**. Lägg till båda dessa fält i meddelandet. Använd fältet som finns i egenskapspåsen **activityLog** och ta bort det andra fältet. Håll markören över **statusfältet** för att se den fullständigt kvalificerade fältreferensen, som visas i följande skärmbild:

    ![Microsoft Teams åtgärd: Skicka ett meddelande](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams åtgärd: Skicka ett meddelande")

14. Högst upp i **Logic Apps Designer**väljer du **Spara** för att spara logikappen.

15. Öppna din befintliga åtgärdsgrupp och lägg till en åtgärd som refererar till logikappen. Om du inte har en befintlig åtgärdsgrupp läser du [Skapa och hantera åtgärdsgrupper i Azure-portalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) för att skapa en. Glöm inte att spara dina ändringar.

    ![Uppdatera åtgärdsgruppen](media/action-groups-logic-app/update-action-group.png "Uppdatera åtgärdsgruppen")

Nästa gång en avisering anropar din åtgärdsgrupp anropas logikappen.

## <a name="create-a-service-health-alert"></a>Skapa en hälsovarning för tjänsten

Azure Service Health-poster är en del av aktivitetsloggen. Processen för att skapa aviseringen liknar att [skapa en aktivitetsloggavisering,](#create-an-activity-log-alert-administrative)men med några ändringar:

- Steg 1 till 7 är desamma.
- För steg 8 använder du följande exempelnyttolast för HTTP-begäranden:

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
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Steg 9 och 10 är desamma.
-  För steg 11 till och med 14 använder du följande process:

   1. Välj **+** **Nytt steg** och välj sedan Lägg till ett **villkor**. Ange följande villkor så att logikappen endast körs när indata matchar värdena nedan.  När du anger versionsvärdet i textrutan placerar du citattecken runt det ("0.1.1") för att se till att det utvärderas som en sträng och inte en numerisk typ.  Citaten visas inte om du går tillbaka till sidan, men den underliggande koden behåller fortfarande strängtypen.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health nyttolast skick"](media/action-groups-logic-app/service-health-payload-condition.png "Service Health nyttolast skick")

   1. I **tillståndet om det är sant** följer du instruktionerna i steg 11 till och med 13 i Skapa en [aktivitetsloggavisering för](#create-an-activity-log-alert-administrative) att lägga till åtgärden Microsoft Teams.

   1. Definiera meddelandet med hjälp av en kombination av HTML och dynamiskt innehåll. Kopiera och klistra in följande innehåll i fältet **Meddelande.** Ersätt `[incidentType]`taggarna `[title]`, `[communication]` `[trackingID]`, och med dynamiska innehållstaggar med samma namn:

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

       !["Service Health sant tillstånd efter åtgärd"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Åtgärden Service Health true condition post")

   1. Ange ett användbart meddelande för **falskt** tillstånd:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health falskt tillstånd efter åtgärd"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Åtgärd för falskt tillstånd efter tjänsthälsa")

- Steg 15 är detsamma. Följ instruktionerna för att spara logikappen och uppdatera din åtgärdsgrupp.

## <a name="create-a-metric-alert"></a>Skapa en måttavisering

Processen för att skapa en måttavisering liknar att [skapa en aktivitetsloggavisering,](#create-an-activity-log-alert-administrative)men med några ändringar:

- Steg 1 till 7 är desamma.
- För steg 8 använder du följande exempelnyttolast för HTTP-begäranden:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
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
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Steg 9 och 10 är desamma.
- För steg 11 till och med 14 använder du följande process:

  1. Välj **+** **Nytt steg** och välj sedan Lägg till ett **villkor**. Ange följande villkor så att logikappen endast körs när indata matchar dessa värden nedan. När du anger versionsvärdet i textrutan placerar du citattecken runt det ("2.0") för att se till att det utvärderas som en sträng och inte en numerisk typ.  Citaten visas inte om du går tillbaka till sidan, men den underliggande koden behåller fortfarande strängtypen. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Villkor för metrisk varningsnyttolast"](media/action-groups-logic-app/metric-alert-payload-condition.png "Nyttolastvillkor för måttvarning")

  1. I tillståndet **om sant** lägger du till en **för varje** loop och microsoft teams-åtgärden. Definiera meddelandet med hjälp av en kombination av HTML och dynamiskt innehåll.

      !["Åtgärd för true condition-villkoret"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Åtgärden True condition post-åtgärd för emetriska aviseringar")

  1. I tillståndet **Om falskt** definierar du en Microsoft Teams-åtgärd för att meddela att måttaviseringen inte matchar logikappens förväntningar. Inkludera JSON-nyttolasten. Lägg märke till `triggerBody` hur du `json()` refererar till det dynamiska innehållet i uttrycket.

      !["Åtgärd för falsk tillståndsvarning"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Åtgärd för falsk tillståndsvarning för metriskt tillstånd")

- Steg 15 är detsamma. Följ instruktionerna för att spara logikappen och uppdatera din åtgärdsgrupp.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Anropa andra program än Microsoft Teams
Logic Apps har ett antal olika kopplingar som gör att du kan utlösa åtgärder i en mängd olika program och databaser. Slack, SQL Server, Oracle, Salesforce, är bara några exempel. Mer information om kopplingar finns i [Logic App-kopplingar](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Nästa steg
* Få en [översikt över Azure-aktivitetsloggaviseringar](../../azure-monitor/platform/alerts-overview.md) och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett Azure Service Health-meddelande har publicerats](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

