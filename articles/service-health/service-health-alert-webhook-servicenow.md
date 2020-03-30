---
title: Skicka hälsovarningar för Azure-tjänsten med ServiceNow
description: Få anpassade aviseringar om tjänsthälsohändelser till din ServiceNow-instans.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 3daae05aabff571010d043cf5602847e95ea29f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654111"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Skicka hälsovarningar för Azure-tjänsten med ServiceNow med webhooks

Den här artikeln visar hur du integrerar Azure-tjänsthälsovarningar med ServiceNow med hjälp av en webhook. När du har konfigurerat webhook-integrering med din ServiceNow-instans får du aviseringar via din befintliga meddelandeinfrastruktur när Azure-tjänstproblem påverkar dig. Varje gång en Azure Service Health-avisering utlöses anropas en webhook via ServiceNow's Scripted REST API.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Skapa ett skriptat REST-API i ServiceNow

1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [ServiceNow-konto.](https://www.servicenow.com/)

1.  Navigera till avsnittet **Systemwebbtjänster** i ServiceNow och välj **SKRIPT-REST-API:er**.

    ![Avsnittet "Skriptad webbtjänst" i ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Välj **Ny** om du vill skapa en ny skriptad REST-tjänst.
 
    ![Knappen "Nytt skript-REST API" i ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Lägg till ett **namn** i REST API:et och ange **API-ID:n** till `azureservicehealth`.

1.  Välj **Skicka**.

    !["REST API-inställningar" i ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Välj rest-API:et som du skapade och välj **Nytt**under fliken **Resurser** .

    ![Fliken "Resurs" i ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Ge** den `event` nya resursen ett `POST`namn och ändra **HTTP-metoden** till .

1.  Lägg till följande JavaScript-kod i avsnittet **Skript:**

    >[!NOTE]
    >Du måste uppdatera `<secret>``<group>`, `<email>` och värdet i skriptet nedan.
    >* `<secret>`bör vara en slumpmässig sträng, som en GUID
    >* `<group>`bör vara den ServiceNow-grupp som du vill tilldela incidenten till
    >* `<email>`bör vara den specifika person som du vill tilldela incidenten till (valfritt)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Avmarkera **Kräver autentisering** på fliken Säkerhet och väljer **Skicka**. Du `<secret>` anger skyddar det här API:et i stället.

    ![Kryssrutan "Kräver autentisering" i ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Tillbaka på avsnittet Skriptad REST-API:er bör du hitta **bas-API-sökvägen** för det nya REST API:et:

     !["Grundläggande API-sökväg" i ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Hela integrations-URL:en ser ut:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Skapa en avisering med ServiceNow i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny åtgärdsgrupp:
1. Följ steg 1 till och med 8 i [den här artikeln](../azure-monitor/platform/alerts-activity-log-service-notifications.md) för att skapa en avisering med en ny åtgärdsgrupp.

1. Definiera i listan över **åtgärder:**

    a. **Åtgärdstyp:** *Webhook*

    b. **Detaljer:** Den **ServiceNow-integrations-URL** som du tidigare sparat.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **Spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärdsgrupp:
1. Välj **Övervaka**i [Azure-portalen](https://portal.azure.com/).

1. Välj **Åtgärdsgrupper** **Action groups**i avsnittet Inställningar .

1. Sök efter och markera den åtgärdsgrupp som du vill redigera.

1. Lägg till i listan över **åtgärder:**

    a. **Åtgärdstyp:** *Webhook*

    b. **Detaljer:** Den **ServiceNow-integrations-URL** som du tidigare sparat.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **Spara** när du vill uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa servicehälsonyttolasten som du vill skicka. Du hittar en exempeltjänsthälsa webhook nyttolast på [Webhooks för Azure aktivitetsloggvarningar](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör `200 OK` få ett svar med meddelandet "Incident skapad".

1. Gå till [ServiceNow](https://www.servicenow.com/) för att bekräfta att integreringen har konfigurerats.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problemhanteringssystem](service-health-alert-webhook-guide.md).
- Granska [webbkroksschemat för aktivitetsloggen.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Läs mer om [hälsomeddelanden](../azure-monitor/platform/service-notifications.md)för tjänster .
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).