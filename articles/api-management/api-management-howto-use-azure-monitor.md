---
title: "Övervaka publicerade API:er i Azure API Management | Microsoft Docs"
description: "Följ stegen i den här kursen lär dig hur du övervakar din API i Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: db1ed08c4d4c9e9abd525ec13f5511da82ee1fe4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
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

## <a name="diagnostic-logs"></a>Visa aktivitetsloggar

Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina API Management-tjänster. Med aktivitetsloggar kan du bestämma ”vad, vem och när” för skrivåtgärder (PUT, POST, DELETE) som ska vidtas för dina API Management-tjänster. 

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET) eller åtgärder som utförs i den klassiska Publisher-portalen eller via ursprungliga Management-API:er.

Du kan få åtkomst till aktivitetsloggar i API Management-tjänsten eller få åtkomst till loggar för alla dina Azure-resurser i Azure Monitor. 

Så här visar du aktivitetsloggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Aktivitetslogg**.

## <a name="view-diagnostic-logs"></a>Visa diagnostikloggar

Diagnostikloggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning, samt i felsökningssyfte. Diagnostikloggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina Azure-resurser. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit på egen hand.

Så här få du åtkomst till diagnostikloggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Diagnostiklogg**.

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
