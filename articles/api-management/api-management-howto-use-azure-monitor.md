---
title: Övervaka publicerade API:er i Azure API Management | Microsoft Docs
description: Följ stegen i den här kursen lär dig hur du övervakar din API i Azure API Management.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 93cbcf91af4ecf9425ed43ade400a0c82cea72d8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-published-apis"></a>Övervaka publicerade API:er

Azure Monitor är Azure-tjänst som tillhandahåller en enda källa för övervakning av alla dina Azure-resurser. Med Azure Monitor kan du visualisera, fråga, vidarebefordra, aktivera och vidta åtgärder för mått och loggar från resurser i Azure, som API Management. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa diagnostikloggar
> * Visa mått för din API 
> * Konfigurera en varningsregel när din API hämtar obehöriga anrop

Följande video visar hur du övervakar API Management med Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="view-metrics-of-your-apis"></a>Visa mått för dina API:er

API Management sänder ut mätvärden varje minut, vilket ger dig en insyn i realtid i API:ernas tillstånd och hälsa. Nedan följer en sammanfattning av några tillgängliga mått:

* Kapacitet (förhandsversion):  hjälper dig att fatta beslut om att uppgradera/nedgradera din APIM-tjänster. Måttet genereras per minut och återspeglar gatewaykapaciteten vid tidpunkten för rapporten. Måtten sträcker sig från 0 till 100 och beräknas utifrån gatewayens resurser som CPU och minnesanvändning.
* Totalt antal gatewaybegäranden: antalet API-begäranden under perioden. 
* Slutförda gatewaybegäranden: antalet API-begäranden som tog emot HTTP-svarskoder inklusive 304, 307 och under 301 (till exempel 200). 
* Misslyckade gatewaybegäranden: antalet API-begäranden som tog emot felaktiga HTTP-svarskoder inklusive 400 och över 500.
* Ej auktoriserad begäran: antalet API-begäranden som tog emot HTTP-svarskoder som 401, 403 och 429. 
* Övriga gatewaybegäranden: antalet API-begäranden som tog emot HTTP-svarskoder som inte tillhör någon av ovanstående kategorier (til exempel 418).

Så här får du åtkomst till mått:

1. Välj **Mått** på menyn långt ned på sidan.
2. Från listrutan väljer du mått som du är intresserad av (du kan lägga till flera mått). 
    
    Välj till exempel **Totalt antal gatewaybegäranden** **Misslyckade gatewaybegäranden** på listan över tillgängliga mått.
3. Diagrammet visar det totala antalet API-anrop. Det visar även antalet API-anrop som misslyckades. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Konfigurera en aviseringsregel för obehörig begäran

Du kan konfigurera för att ta emot varningar baserat på mått och aktivitet. Med Azure Monitor kan du konfigurera att en avisering ska göra följande när den utlöses:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Så här konfigurerar du varningar:

1. Välj **Aviseringsregler** på menyraden långt ned på sidan.
2. Välj **Lägg till metrisk varning**.
3. Ange ett **namn** på varningen.
4. Välj **Ej auktoriserad begäran** som mått som ska övervakas.
5. Välj **E-postägare, deltagare och läsare**.
6. Tryck på **OK**.
7. Försök anropa vår konferens-API utan API-nyckel. Du får en e-postavisering som ägare till den här API Management-tjänsten. 

    > [!TIP]
    > Varningsregeln kan också anropa en webbhook eller en Azure Logic App när den utlöses.

    ![set-up-alert](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="activity-logs"></a>Aktivitetsloggar

Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina API Management-tjänster. Med aktivitetsloggar kan du bestämma ”vad, vem och när” för skrivåtgärder (PUT, POST, DELETE) som ska vidtas för dina API Management-tjänster. 

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET), åtgärder som utförs i Azure-portalen eller via ursprungliga hanterings-API:er.

Du kan få åtkomst till aktivitetsloggar i API Management-tjänsten eller få åtkomst till loggar för alla dina Azure-resurser i Azure Monitor. 

Så här visar du aktivitetsloggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Aktivitetslogg**.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Diagnostikloggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning, samt i felsökningssyfte. Diagnostikloggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina Azure-resurser. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit på egen hand.

Så här konfigurerar du diagnostikloggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Diagnostiklogg**.
3. Klicka på **Slå på diagnostik**. Du kan arkivera diagnostikloggar tillsammans med mått i ett lagringskonto, strömma dem till en Event Hub eller skicka dem till Log Analytics. 

API Management tillhandahåller för närvarande diagnostikloggar (i batchar varje timme) om enskilda API-begäran där varje inmatning har följande schema:

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
| isRequestSuccess | boolesk | True om HTTP-begäran slutfördes med svarsstatuskod inom intervallet 2xx eller 3xx |
| time | date-time | Tidstämpel för mottagande av HTTP-begäran av gatewayen |
| operationName | sträng | Konstantvärde ”Microsoft.ApiManagement/GatewayLogs” |
| category | sträng | Konstantvärde ”GatewayLogs” |
| durationMs | heltal | Antalet millisekunder från att gatewayen fick begäran till då svaret har skickats fullständigt |
| callerIpAddress | sträng | IP-adress för anropare av omedelbar gateway (kan vara en mellanhand) |
| correlationId | sträng | Unik http-begäranidentifierare tilldelad av API Management |
| location | sträng | Namn på Azure-regionen där gatewayen som behandlade begäran hittades |
| httpStatusCodeCategory | sträng | Kategori för http-svarsstatuskod: Lyckades (301 eller lägre eller 304 eller 307), Ej auktoriserad (401, 403, 429), Felaktig (400, mellan 500 och 600), Annat |
| resourceId | sträng | ”Id för API Management-resursen /SUBSCRIPTIONS/<subscription>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/<name> |
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
| elapsed | heltal | Antalet förflutna millisekunder sedan gatewayen tog emot begäran till den tidpunkt då felet inträffade | 
| källa | sträng | Namn på principen eller behandling av intern hanterare som orsakade felet | 
| omfång | sträng | Omfattningen för det dokument som innehåller principen som orsakade felet | 
| avsnitt | sträng | Avsnittet för det dokument som innehåller principen som orsakade felet | 
| orsak | sträng | Felorsak | 
| meddelande | sträng | Felmeddelande | 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa diagnostikloggar
> * Visa mått för din API 
> * Konfigurera en varningsregel när din API hämtar obehöriga anrop

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Spåra anrop](api-management-howto-api-inspector.md)
