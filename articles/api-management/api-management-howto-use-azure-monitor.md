---
title: "Övervakaren publicerade API: er i Azure API Management | Microsoft Docs"
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
ms.openlocfilehash: bdca9d4968e9e68314f350787907f15e417821f7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="monitor-published-apis"></a>Övervaka publicerade API: er

Azure övervakaren är en Azure-tjänst som tillhandahåller en enda källa för övervakning av alla Azure-resurser. Med Azure-Monitor kan du visualisera, fråga, vidarebefordra, arkivera och vidta åtgärder på mått och loggar från Azure-resurser som API-hantering. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa diagnostikloggar
> * Visa mätvärden för ditt API 
> * Konfigurera en aviseringsregel när din API hämtar otillåtna anrop

Följande videoklipp visar hur du övervakar API Management med hjälp av Azure-Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Krav

+ Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Dessutom slutföra följande kursen: [Import och publicera din första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>Visa aktivitetsloggar

Aktiviteten loggarna ger information om åtgärder som utfördes på din API Management-tjänster. Med aktivitetsloggar kan du bestämma den ”vad som, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas för API Management-tjänster. 

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET) eller åtgärder utförs i den klassiska portalen utgivare eller med den ursprungliga Management-API: er.

Du kan komma åt aktivitetsloggar i API Management-tjänsten eller komma åt loggarna för alla dina Azure-resurser i Azure-Monitor. 

Visa aktivitetsloggar:

1. Från din **API Management** instans klickar du på **aktivitetsloggen**.

## <a name="view-diagnostic-logs"></a>Visa diagnostikloggar

Diagnostikloggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning, samt i felsökningssyfte. Diagnostik loggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikter om de åtgärder som utfördes på Azure-resurser. Diagnostik-loggarna ger information om åtgärder som din resurs utförde sig själv.

Öppna diagnostikloggar:

1. Från din **API Management** instans klickar du på **diagnostiska loggen**.

## <a name="view-metrics-of-your-apis"></a>Visa mätvärden för dina API: er

API Management avger mätvärden varje minut, vilket ger dig nära realtid insyn i tillstånd och hälsotillståndet för dina API: er. Nedan följer en sammanfattning av några tillgängliga mått:

* Kapacitet (förhandsversion): hjälper dig att fatta beslut om att uppgradera/nedgradera APIM-tjänster. Måttet har genererats per minut och visar gateway-kapacitet vid tidpunkten för rapporten. Måttet mellan 0 och 100 och beräknas baserat på gateway recourses, till exempel CPU-och minnesanvändningen.
* Totalt antal begäranden som Gateway: antal API begäranden under perioden. 
* Slutförda förfrågningar som Gateway: antalet API-begäranden som tas emot lyckade HTTP-svarskoder inklusive 304, 307 och mindre än 301 (till exempel 200). 
* Misslyckade begäranden som Gateway: antalet API-begäranden som tas emot felaktiga HTTP-svarskoder inklusive 400 och något som är större än 500.
* Obehörig Gateway begäranden: antalet API-begäranden som tas emot HTTP-svarskoder inklusive 401, 403 och 429. 
* Andra Gateway-begäranden: antalet API-begäranden som tas emot HTTP-svarskoder som inte tillhör någon av ovanstående kategorier (till exempel 418).

Öppna mått:

1. Välj **mått** på menyn längst ned på sidan.
2. Listrutan, välja mått som du är intresserad av (du kan lägga till flera mått). 
    
    Välj exempelvis **Gateway förfrågningarna** och **misslyckade begäranden för Gateway** från listan över tillgängliga mått.
3. Diagrammet visar det totala antalet API-anrop. Den visar även antalet API-anrop misslyckades. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Konfigurera en aviseringsregel för obehörig begäran

Du kan konfigurera för att ta emot varningar baserat på mått och aktivitet. Azure-Monitor kan du konfigurera en avisering när den utlöser gör du följande:

* Skicka ett e-postmeddelande
* anropa en webhook
* Anropa en Azure Logikapp

Konfigurera aviseringar:

1. Välj **Varna regler** på menyraden längst ned på sidan.
2. Välj **Lägg till mått avisering**.
3. Ange en **namn** för den här aviseringen.
4. Välj **obehöriga Gateway begäranden** som mått för att övervaka.
5. Välj **e-ägare, deltagare och läsare**.
6. Tryck på **OK**.
7. Försök att anropa vår konferens API utan en API-nyckel. Du får en e-postavisering som ägare till den här API Management-tjänsten. 

    > [!TIP]
    > Varningsregeln kan också anropa ett webb-Hook eller ett Azure-Logikapp när den utlöses.

    ![set-aviseringen](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa diagnostikloggar
> * Visa mätvärden för ditt API 
> * Konfigurera en aviseringsregel när din API hämtar otillåtna anrop

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Spårning av anrop](api-management-howto-api-inspector.md)