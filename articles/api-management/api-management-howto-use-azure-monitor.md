---
title: Övervaka publicerade API:er i Azure API Management | Microsoft Docs
description: Följ stegen i den här kursen lär dig hur du övervakar din API i Azure API Management.
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 7f6c7a651e133122dab86d6ed81572f239718b43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243247"
---
# <a name="monitor-published-apis"></a>Övervaka publicerade API:er

Med Azure Monitor kan du visualisera, fråga, vidarebefordra, aktivera och vidta åtgärder för mått eller loggar från resurser i Azure.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa resurs loggar
> * Visa mått för din API 
> * Konfigurera en varningsregel när din API hämtar obehöriga anrop

Följande video visar hur du övervakar API Management med Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Krav

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Visa mått för dina API:er

API Management sänder ut mätvärden varje minut, vilket ger dig en insyn i realtid i API:ernas tillstånd och hälsa. Nedan visas de två oftast använda måtten. En lista över alla tillgängliga mått finns i [mått som stöds](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice).

* Kapacitet: hjälper dig att fatta beslut om att uppgradera/nedgradera dina APIM-tjänster. Måttet genereras per minut och återspeglar gatewaykapaciteten vid tidpunkten för rapporten. Måtten sträcker sig från 0 till 100 och beräknas utifrån gatewayens resurser som CPU och minnesanvändning.
* Begär Anden: hjälper dig att analysera API-trafik som går via dina APIM-tjänster. Måttet genereras per minut och rapporterar antalet Gateway-begäranden med dimensioner, inklusive svars koder, plats, värdnamn och fel. 

> [!IMPORTANT]
> Följande mått är föråldrade från maj 2019 och kommer att dras tillbaka i augusti 2023: totalt antal Gateway-begäranden, lyckade Gateway-begäranden, obehöriga Gateway-begäranden, misslyckade Gateway-begäranden, andra gateway-begäranden. Migrera till begär ande måttet som ger motsvarande funktionalitet.

![måttdiagram](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Så här får du åtkomst till mått:

1. Välj **Mått** på menyn långt ned på sidan.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Från listrutan väljer du mått som du är intresserad av. Till exempel **begär Anden**. 
3. Diagrammet visar det totala antalet API-anrop.
4. Diagrammet kan filtreras med dimensionerna för **begär** ande mått. Klicka till exempel på **Lägg till filter**, Välj **Server del svars kod**, ange 500 som värde. Nu visar diagrammet antalet begär Anden som misslyckats i API-backend-servern.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Konfigurera en aviseringsregel för obehörig begäran

Du kan konfigurera för att ta emot varningar baserat på mått och aktivitetsloggar. Med Azure Monitor kan du konfigurera att en avisering ska göra följande när den utlöses:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Så här konfigurerar du varningar:

1. Välj **aviseringar** från meny raden nästan längst ned på sidan.

    ![aviseringar](./media/api-management-azure-monitor/alert-menu-item.png)

2. Klicka på en **ny aviserings regel** för den här aviseringen.
3. Klicka på **Lägg till villkor**.
4. Välj **mått** i list rutan signal typ.
5. Välj **obehörig Gateway-begäran** som den signal som ska övervakas.

    ![aviseringar](./media/api-management-azure-monitor/signal-type.png)

6. I vyn **Konfigurera signal logik** anger du ett tröskelvärde efter vilken aviseringen ska utlösas och klickar på **Slutför**.

    ![aviseringar](./media/api-management-azure-monitor/threshold.png)

7. Välj en befintlig åtgärds grupp eller skapa en ny. I exemplet nedan skickas ett e-postmeddelande till administratörerna. 

    ![aviseringar](./media/api-management-azure-monitor/action-details.png)

8. Ange ett namn, en beskrivning av varnings regeln och välj allvarlighets grad. 
9. Tryck på **skapa aviserings regel**.
10. Försök nu att anropa konferens-API utan API-nyckel. Aviseringen aktive ras och e-postmeddelandet skickas till administratörerna. 

## <a name="activity-logs"></a>Aktivitetsloggar

Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina API Management-tjänster. Med aktivitetsloggar kan du bestämma ”vad, vem och när” för skrivåtgärder (PUT, POST, DELETE) som ska vidtas för dina API Management-tjänster.

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET), åtgärder som utförs i Azure-portalen eller via ursprungliga hanterings-API:er.

Du kan få åtkomst till aktivitetsloggar i API Management-tjänsten eller få åtkomst till loggar för alla dina Azure-resurser i Azure Monitor. 

![aktivitetsloggar](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Så här visar du aktivitetsloggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Aktivitetslogg**.

    ![aktivitetslogg](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Välj önskat filtreringsomfång och klicka på **Använd**.

## <a name="resource-logs"></a>Resurs loggar

Resurs loggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning samt fel söknings syfte. Resurs loggar skiljer sig från aktivitets loggar. Aktivitets loggarna ger insikter om de åtgärder som utfördes på dina Azure-resurser. Resurs loggar ger insikt om åtgärder som din resurs utfört.

Så här konfigurerar du resurs loggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Diagnostikinställningar**.

    ![resurs loggar](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Klicka på **Slå på diagnostik**. Du kan arkivera resurs loggar tillsammans med mått till ett lagrings konto, strömma dem till en Händelsehubben eller skicka dem till Azure Monitor loggar. 

API Management tillhandahåller för närvarande resurs loggar (grupperade per timme) om individuell API-begäran med varje post med följande schema:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Egenskap  | Typ | Beskrivning |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | True om HTTP-begäran slutfördes med svarsstatuskod inom intervallet 2xx eller 3xx |
| time | date-time | Tidsstämpeln för när gatewayen startar bearbetningen av begäran |
| operationName | sträng | Konstantvärde ”Microsoft.ApiManagement/GatewayLogs” |
| category | sträng | Konstantvärde ”GatewayLogs” |
| durationMs | heltal | Antalet millisekunder från den tidpunkt då gatewayen tog emot begäran tills det svar som har skickats fullständigt. Den innehåller clienTime, cacheTime och backendTime. |
| callerIpAddress | sträng | IP-adress för anropare av omedelbar gateway (kan vara en mellanhand) |
| correlationId | sträng | Unik http-begäranidentifierare tilldelad av API Management |
| location | sträng | Namn på Azure-regionen där gatewayen som behandlade begäran hittades |
| httpStatusCodeCategory | sträng | Kategori för http-svarsstatuskod: Lyckades (301 eller lägre eller 304 eller 307), Ej auktoriserad (401, 403, 429), Felaktig (400, mellan 500 och 600), Annat |
| resourceId | sträng | ID för API Management resurs/SUBSCRIPTIONS/ \<subscription> /RESOURCEGROUPS/ \<resource-group> /providers/Microsoft. API MANAGEMENT/SERVICE/\<name> |
| properties | objekt | Egenskaper för aktuell begäran |
| metod | sträng | HTTP-metod för inkommande begäran |
| url | sträng | URL för inkommande begäran |
| clientProtocol | sträng | HTTP-protokollversion för inkommande begäran |
| responseCode | heltal | Statuskod för HTTP-svar som skickas till klient |
| backendMethod | sträng | HTTP-metod för begäran som skickats till en serverdel |
| backendUrl | sträng | URL för begäran som skickats till en serverdel |
| backendResponseCode | heltal | Kod för HTTP-svaret togs emot av en serverdel |
| backendProtocol | sträng | HTTP-protokollversion för begäran som skickats till en serverdel | 
| requestSize | heltal | Antalet byte som tagits emot från en klient under bearbetning av begäran | 
| responseSize | heltal | Antalet byte som har skickats till en klient under bearbetning av begäran | 
| cache | sträng | Status för API Management cachemedverkan i behandling av begäran (t.ex. träff, miss, ingen) | 
| cacheTime | heltal | Antalet millisekunder som ägnats åt övergripande API Management cache-IO (ansluta, skicka och ta emot byte) | 
| backendTime | heltal | Antalet millisekunder som ägnats åt övergripande serverdels-IO (ansluta, skicka och ta emot byte) | 
| clientTime | heltal | Antalet millisekunder som ägnats åt övergripande klient-IO (ansluta, skicka och ta emot byte) | 
| apiId | sträng | API-entitetsidentifierare för aktuell begäran | 
| operationId | sträng | Åtgärdsentitetsidentifierare för aktuell begäran | 
| productId | sträng | Produktentitetsidentifierare för aktuell begäran | 
| userId | sträng | Användarentitetsidentifierare för aktuell begäran | 
| apimSubscriptionId | sträng | Prenumerationsentitetsidentifierare för aktuell begäran | 
| backendId | sträng | Serverdelentitetsidentifierare för aktuell begäran | 
| LastError | objekt | Bearbetningsfel för senaste begäran | 
| elapsed | heltal | Antalet millisekunder som förflutit mellan när gatewayen tog emot begäran och tidpunkten då felet inträffade | 
| källa | sträng | Namn på principen eller behandling av intern hanterare som orsakade felet | 
| omfång | sträng | Omfattningen för det dokument som innehåller principen som orsakade felet | 
| avsnitt | sträng | Avsnittet för det dokument som innehåller principen som orsakade felet | 
| orsak | sträng | Felorsak | 
| meddelande | sträng | Felmeddelande | 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa resurs loggar
> * Visa mått för din API
> * Konfigurera en varningsregel när din API hämtar obehöriga anrop

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Spåra anrop](api-management-howto-api-inspector.md)
