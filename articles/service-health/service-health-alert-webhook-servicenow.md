---
title: Konfigurera aviseringar för Azure-tjänstens hälsotillstånd med ServiceNow | Microsoft Docs
description: Hämta anpassade meddelanden om tjänstens hälsa händelser till ServiceNow-instans.
author: shawntabrizi
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 867a8c0b478df9d2b7690b8b914ded7c42558583
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178876"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Konfigurera tjänsten health-aviseringar med ServiceNow

Den här artikeln visar hur du integrerar hälsovarningar för Azure-tjänsten med ServiceNow med hjälp av en webhook. När du har skapat webhooken integrering med din ServiceNow-instans, får du aviseringar via din befintliga infrastruktur för meddelanden när problem med Azure-tjänsten påverkar dig. Varje gång en Azure-tjänstens hälsa avisering utlöses anropar en webhook via Servicenows skripta REST API.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Skapa ett skript REST-API i ServiceNow
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [ServiceNow](https://www.servicenow.com/) konto.

2.  Navigera till den **System webbtjänster** i avsnittet ServiceNow och välj **skripta REST API: er**.

    ![Avsnittet ”Skriptfönster webbtjänsten” i ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

3.  Välj **ny** att skapa en ny skripta REST-tjänst.
 
    ![Knappen ”Ny skripta REST-API” i ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

4.  Lägg till en **namn** till REST-API och ange den **API-ID** till `azureservicehealth`.

5.  Välj **skicka**.

    ![”REST API inställningarna” i ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  Välj REST-API som du skapade och under den **resurser** väljer **ny**.

    ![”Fliken resurs” i ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

7.  **Namnet** ny resurs `event` och ändra den **HTTP-metoden** till `POST`.

8.  I den **skriptet** lägger du till följande JavaScript-kod:

    >[!NOTE]
    >Du måste uppdatera de `<secret>`,`<group>`, och `<email>` värdet i skriptet nedan.
    >* `<secret>` måste vara en slumpmässig sträng som ett GUID
    >* `<group>` bör vara den ServiceNow-grupp som du vill tilldela incident till
    >* `<email>` bör vara specifika personer som du vill tilldela incident till (valfritt)
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
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
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

9.  På säkerhetsfliken avmarkera **kräver autentisering** och välj **skicka**. Den `<secret>` du ange skyddar denna API i stället.

    ![Kryssrutan ”kräver autentisering” i ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

10.  Tillbaka på avsnittet skripta REST API: er, bör du hitta på **Base API sökvägen** för ditt nya REST-API:

     ![Den ”basera API sökvägen” i ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

11.  Det ser ut så fullständig integrering URL: en:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Skapa en avisering med hjälp av ServiceNow i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny grupp:
1. Följ steg 1 till 8 i [i den här artikeln](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) att skapa en avisering med en ny grupp.

2. Definiera i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhooken*

    b. **Information:** på ServiceNow **integrering URL** du sparat tidigare.

    c. **Namn:** Webhook's, alias eller identifierare.

3. Välj **spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig grupp:
1. I den [Azure-portalen](https://portal.azure.com/)väljer **övervakaren**.

2. I den **inställningar** väljer **åtgärdsgrupper**.

3. Hitta och välj den grupp du vill redigera.

4. Lägga till listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhooken*

    b. **Information:** på ServiceNow **integrering URL** du sparat tidigare.

    c. **Namn:** Webhook's, alias eller identifierare.

5. Välj **spara** när du är klar för att uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa service hälsa nyttolast som du vill skicka. Du hittar en exempel-tjänsten hälsa webhook nyttolast på [Webhooks för Azure aktiviteten Logga varningar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Du bör få ett `200 OK` svar med meddelandet ”Incident skapad”.

4. Gå till [ServiceNow](https://www.servicenow.com/) att bekräfta att din integrering har ställts in.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-meddelanden för befintliga problem system](service-health-alert-webhook-guide.md).
- Granska de [avisering webhook för aktivitetslogg](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Lär dig mer om [tjänsten meddelanden om hälsostatus](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Lär dig mer om [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md).