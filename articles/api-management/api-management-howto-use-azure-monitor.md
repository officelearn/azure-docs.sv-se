---
title: "Övervaka API-hantering med Azure-Monitor | Microsoft Docs"
description: "Lär dig hur du övervakar Azure API Management-tjänsten med hjälp av Azure-Monitor."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>Övervakare för API Management med Azure Övervakare
Azure övervakaren är en Azure-tjänst som tillhandahåller en enda källa för övervakning av alla Azure-resurser. Med Azure-Monitor kan du visualisera, fråga, vidarebefordra, arkivera och vidta åtgärder på mått och loggar från Azure-resurser som API-hantering. 

Följande videoklipp visar hur du övervakar API Management med hjälp av Azure-Monitor. Läs mer om Azure-Monitor [Kom igång med Azure-Monitor]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Mått
API-hantering för närvarande skickar fem mått och vi planerar att lägga till fler i framtiden. De här måtten släpps varje minut, vilket ger dig nära realtid insyn i tillstånd och hälsotillståndet för dina API: er. Nedan följer en sammanfattning av mätvärden:
* Totalt antal begäranden som Gateway: antal API begäranden under perioden. 
* Slutförda förfrågningar som Gateway: antalet API-begäranden som tas emot lyckade HTTP-svarskoder inklusive 304, 307 och mindre än 301 (till exempel 200). 
* Misslyckade begäranden som Gateway: antalet API-begäranden som tas emot felaktiga HTTP-svarskoder inklusive 400 och något som är större än 500.
* Obehörig Gateway begäranden: antalet API-begäranden som tas emot HTTP-svarskoder inklusive 401, 403 och 429. 
* Andra Gateway-begäranden: antalet API-begäranden som tas emot HTTP-svarskoder som inte tillhör någon av ovanstående kategorier (till exempel 418).

Du kan komma åt mått i API Management-tjänsten eller åtkomst mätvärden för alla dina Azure-resurser i Azure-Monitor. Visa mått i API Management-tjänsten:
1. Öppna Azure-portalen.
2. Gå till din API Management-tjänst.
3. Klicka på **mått**.

![Mått bladet][metrics-blade]

Mer information om hur du använder mått finns [översikt av mått].

## <a name="activity-logs"></a>Aktivitetsloggar
Aktiviteten loggarna ger information om åtgärder som utfördes på din API Management-tjänster. Det som tidigare kallades ”granskningsloggar” eller ”arbetsloggarna”. Med aktivitetsloggar kan du bestämma den ”vad som, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas för API Management-tjänster. 

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET) eller åtgärder utförs i den klassiska portalen utgivare eller med den ursprungliga Management-API: er.

Du kan komma åt aktivitetsloggar i API Management-tjänsten eller komma åt loggarna för alla dina Azure-resurser i Azure-Monitor. Visa aktiviteten loggar i API Management-tjänsten:
1. Öppna Azure-portalen.
2. Gå till din API Management-tjänst.
3. Klicka på **aktivitetsloggen**.

![Aktiviteten loggar bladet][activity-logs-blade]

Mer information om hur du använder mått finns [översikt aktivitetsloggar].

## <a name="alerts"></a>Aviseringar
Du kan konfigurera för att ta emot varningar baserat på mått och aktivitet. Azure-Monitor kan du konfigurera en avisering när den utlöser gör du följande:

* Skicka ett e-postmeddelande
* anropa en webhook
* Anropa en Azure Logikapp

Du kan konfigurera Varningsregler i API Management-tjänsten, eller i Azure-Monitor. Konfigurera dem i API-hantering: 
1. Öppna Azure-portalen.
2. Gå till din API Management-tjänst.
3. Klicka på **Varna regler**.

![Varningsregler bladet][alert-rules-blade]

Mer information om hur du använder aviseringar finns [översikt över aviseringar].

## <a name="diagnostic-logs"></a>Diagnostikloggar
Diagnostikloggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning, samt i felsökningssyfte. Diagnostik loggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikter om de åtgärder som utfördes på Azure-resurser. Diagnostik-loggarna ger information om åtgärder som din resurs utförde sig själv.

API-hantering för närvarande tillhandahåller diagnostik loggar (batchar per timme) för enskilda API begäran med varje post med följande struktur:

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

Du kan komma åt diagnostikloggar i API Management-tjänsten eller komma åt loggarna för alla dina Azure-resurser i Azure-Monitor. Visa diagnostikloggar i API Management-tjänsten:
1. Öppna Azure-portalen.
2. Gå till din API Management-tjänst.
3. Klicka på **diagnostiska loggen**.

![Bladet Diagnostikloggar][diagnostic-logs-blade]

Mer information om hur du använder mått finns [översikt av diagnostikloggar].

## <a name="next-step"></a>Nästa steg

* [Kom igång med Azure-Monitor]
* [översikt av mått]
* [översikt aktivitetsloggar]
* [översikt av diagnostikloggar]
* [översikt över aviseringar]

[Kom igång med Azure-Monitor]: ../monitoring-and-diagnostics/monitoring-get-started.md
[översikt av mått]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[översikt aktivitetsloggar]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[översikt av diagnostikloggar]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[översikt över aviseringar]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
