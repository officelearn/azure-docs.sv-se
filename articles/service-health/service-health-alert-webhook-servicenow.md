---
title: Konfigurera Azure service health-aviseringar med ServiceNow | Microsoft Docs
description: Få personligt anpassade meddelanden om service health-händelser till din ServiceNow-instans.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 11/14/2017
ms.openlocfilehash: f17215a5695128bf2ea507efa0c12fdbba9467d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620955"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Konfigurera service health-aviseringar med ServiceNow

Den här artikeln visar hur du integrerar Azure service health-aviseringar med ServiceNow med en webhook. När du skapat webhook-integrering med din ServiceNow-instans kan få du aviseringar via din befintliga infrastruktur för meddelanden när problem med Azure-tjänsten påverkar dig. Varje gång en Azure Service Health-avisering utlöses, anropar en webhook via Servicenows skriptade REST-API.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Skapa ett skriptbaserade REST-API i ServiceNow
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [ServiceNow](https://www.servicenow.com/) konto.

1.  Navigera till den **System webbtjänster** i avsnittet ServiceNow och välj **skriptade REST API: er**.

    ![Avsnittet ”skriptade webbtjänsten” i ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Välj **New** att skapa en ny skriptade REST-tjänst.
 
    ![Knappen ”Ny skriptade REST-API” i ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Lägg till en **namn** för REST API och ange den **API-ID** till `azureservicehealth`.

1.  Välj **Skicka**.

    ![De ”REST API Settings” i ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Välj REST-API som du skapade och under den **resurser** fliken väljer **New**.

    ![”Resource fliken” i ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Namnet** din nya resurs `event` och ändra den **HTTP-metoden** till `POST`.

1.  I den **skriptet** lägger du till följande JavaScript-kod:

    >[!NOTE]
    >Du måste uppdatera den `<secret>`,`<group>`, och `<email>` värdet i skriptet nedan.
    >* `<secret>` ska vara en slumpmässig sträng, t.ex. ett GUID
    >* `<group>` bör vara den ServiceNow-grupp som du vill tilldela incidenten till
    >* `<email>` bör vara den specifika person som du vill tilldela incidenten till (valfritt)
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

1.  På säkerhetsfliken, avmarkera **kräver autentisering** och välj **skicka**. Den `<secret>` du set skyddar detta API i stället.

    ![Kryssrutan ”kräver autentisering” i ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Tillbaka på avsnittet skriptade REST API: er, bör du hitta den **Base API sökvägen** för nya REST-API:

     ![Den ”basera API sökvägen” i ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Din fullständig integrering URL-Adressen liknar:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Skapa en avisering med ServiceNow i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny åtgärdsgrupp:
1. Följ steg 1 till och med 8 i [i den här artikeln](../azure-monitor/platform/alerts-activity-log-service-notifications.md) att skapa en avisering med en ny åtgärdsgrupp.

1. Definiera i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhook*

    b. **Information:** ServiceNow **integrering URL** du sparat tidigare.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärdsgrupp:
1. I den [Azure-portalen](https://portal.azure.com/)väljer **övervakaren**.

1. I den **inställningar** väljer **åtgärdsgrupper**.

1. Hitta och välj åtgärdsgrupp som du vill redigera.

1. Lägg till i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhook*

    b. **Information:** ServiceNow **integrering URL** du sparat tidigare.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **spara** när du är klar för att uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa webhook-integrering via en HTTP POST-begäran
1. Skapa service health-nyttolasten som du vill skicka. Du hittar ett exempel service health webhook-nyttolasten på [Webhooks för Azure-aktivitetsloggar loggaviseringar](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran enligt följande:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör få ett `200 OK` svar med meddelandet ”Incident skapad”.

1. Gå till [ServiceNow](https://www.servicenow.com/) att bekräfta att din integrering har har ställts in.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-aviseringar för befintliga problem system](service-health-alert-webhook-guide.md).
- Granska den [avisering webhook för aktivitetslogg](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Lär dig mer om [service health meddelanden](../azure-monitor/platform/service-notifications.md).
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).