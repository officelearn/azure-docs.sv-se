---
title: "Lägga till åtgärder i en API i Azure API Management | Microsoft Docs"
description: "Lär dig hur du lägger till åtgärder i en API i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 1158a023-1913-4e9c-93de-9164b672f9b3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 8b047c0826590d1cb6a79a2f14ca07764dc2b409
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Lägga till åtgärder i en API i Azure API Management
Innan en API i API Management kan användas, måste du lägga till åtgärder. Den här guiden visar hur du lägger till och konfigurera olika typer av åtgärder till en API i API-hantering.

## <a name="add-operation"></a>Lägga till en åtgärd
Åtgärder läggs till och konfigurerats för att en API i portalen utgivare. För att komma åt publisher-portalen klickar du på **Publisher portal** i Azure Portal för API Management-tjänsten.

![Utgivarportalen][api-management-management-console]

> Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][Create an API Management service instance] i självstudiekursen [Komma igång med Azure API Management][Get started with Azure API Management].
> 
> 

Välj önskad API i portalen för utgivaren och välj sedan den **Operations** fliken. 

![Åtgärder][api-management-operations]

Klicka på **lägga till åtgärden** att lägga till en ny operation. Den **nya åtgärden** visas och **signatur** fliken väljs som standard.

![Lägg till åtgärd][api-management-add-operation]

Ange den **HTTP-verbet** genom att välja från den nedrullningsbara listan.

![HTTP-metoden][api-management-http-method]

<a name="url-template"></a>

Ange URL: en mall genom att skriva in ett URL-fragment som består av en eller flera URL-sökvägssegment och noll eller flera sträng Frågeparametrar. Mallen URL läggs till bas-URL för API: et, identifierar en enskild HTTP-åtgärd. Det kan innehålla en eller flera namngivna variabeln delar som identifieras av klammerparenteser. Dessa rörliga delar kallas mallparametrar och tilldelas dynamiskt värden som extraheras från den begärda URL: en när begäran bearbetas av API Management-plattformen.

> URL: en mall kan innehålla mönster med jokertecken. Ange till exempel `/*` vidarebefordra alla begäranden för HTTP-metoden längst bak avslutas tjänsten.

![URL: en mall][api-management-url-template]

<a name="rewrite-url-template"></a>

Om du vill ange den **skriva om URL: en mall**. På så sätt kan du använda den URL-standardmallen för bearbetning av inkommande begäranden på den frontend när anropa backend via en konverterade URL enligt mallen omarbetning. Mallparametrar från URL-mallen ska användas i mallen omarbetning. I följande exempel visas hur content-type-kodade som sökvägssegmentet i webbtjänsten från föregående exempel kan anges som en frågeparameter i API som publicerats via API Management-plattform med hjälp av URL-mallar.

![URL: en mall för omarbetning][api-management-url-template-rewrite]

Anropare till åtgärden kommer att använda formatet `/customers?customerid=ALFKI` och kommer att mappas till `/Customers('ALFKI')` när backend-tjänst har anropats.

**Visa** namn och **beskrivning** ge en beskrivning av åtgärden och används för att tillhandahålla dokumentation för utvecklare som använder detta API i developer-portalen.

![Beskrivning][api-management-description]

Åtgärden beskrivning kan anges som oformaterad text eller HTML i den **beskrivning** textruta.

## <a name="operation-caching"></a>Åtgärden cachelagring
Svaret cachelagring minskar svarstiden uppfattas av API-konsumenter, driftmiljön bandbreddsanvändning och minskar belastningen på webben HTTP-tjänsten implementerar API: et. 

Välj för att snabbt och enkelt aktivera cachelagring för åtgärden på **cachelagring** och kontrollerar de **aktivera** kryssrutan.

![Cachelagring][api-management-caching-tab]

**Varaktighet** anger den tidsperiod under vilken åtgärden svaret finns kvar i cacheminnet. Standardvärdet är 3 600 sekunder eller 1 timme.

Cache-nycklar används för att skilja mellan svar så att svaret som motsvarar varje olika cachenyckel får sitt eget separat cachelagrade värde. Du kan också ange särskilda frågeparametrar för sträng och/eller HTTP-huvuden som ska användas i computing cache nyckelvärdena i den **variera inom strängen frågeparametrar** och **variera av huvuden** textrutor respektive. När ingen är angivna, fullständig URL: en och följande värden i HTTP-huvudet som används i cache-nyckelgenerering: **acceptera** och **Accept-Charset**.

> Mer information om cachelagring och cachelagring principer finns [så att cachelagra åtgärden resulterar i Azure API Management][How to cache operation results in Azure API Management].
> 
> 

## <a name="request-parameters"></a>Parametrar för begäran
Åtgärdsparametrar hanteras på fliken Parametrar. Parametrar som anges i den **URL mallen** på den **signatur** läggs till automatiskt och kan ändras endast genom att redigera mallen URL. Ytterligare parametrar kan anges manuellt.

Om du vill lägga till en ny frågeparameter, klickar du på **lägga till frågeparameter** och ange följande information:

* **Namnet** -parameternamnet.
* **Beskrivning** -en kort beskrivning av parametern (valfritt).
* **Typen** -parametertypen som valts i listrutan ned.
* **Värden** -värden som kan tilldelas till den här parametern. Ett av värdena kan vara markerad som standard (valfritt).
* **Krävs** -gör parametern obligatorisk genom att markera kryssrutan. 

![Parametrar för begäran][api-management-request-parameters]

## <a name="request-body"></a>Begärandetext
Om åtgärden tillåter (t.ex. PUT, POST) och kräver en text som du kan ge ett exempel på det till alla stöds representation format (t.ex. json, XML). 

> Begärandetexten används endast för dokumentation och har inte verifierats.
> 
> 

Om du vill ange en begärantext växla till den **brödtext** fliken.

Klicka på **lägga till Representation**, börja skriva önskad content-type-namn (t.ex. program/json), väljer du den i listrutan och klistra in önskad begäran brödtext exemplet i det markerade formatet i textrutan. 

![Begärandetexten][api-management-request-body]

I ytterligare att garantier, kan du också ange en valfri beskrivning i den **beskrivning** textruta.

## <a name="responses"></a>Svar
Det är en bra idé att ge exempel på svar för alla statuskoder som kan generera igen. Varje statuskod kan ha flera svar brödtext exempelvis ett för varje innehållstyperna som stöds. 

Lägg till ett svar, klicka på **Lägg till** och börja skriva önskad statuskoden. I det här exemplet statuskoden är **200 OK**. När koden som visas i listrutan, markerar du det och svarskoden skapas och läggs till igen.

![Svarskod][api-management-response-code]

Klicka på **lägga till Representation**, skriver du in önskad content-type-namnet (t.ex. program/json) och markerar den i nedrullningsbara ned.

![Brödtext innehållstyp][api-management-response-body-content-type]

Klistra in exemplet brödtext för svar i det markerade formatet i textrutan. 

![Svarstexten][api-management-response-body]

Om du vill lägga till en valfri beskrivning i den **beskrivning** textruta.

När åtgärden har konfigurerats klickar du på **spara**.

## <a name="next-steps"> </a>Nästa steg
När åtgärderna har lagts till i en API, är nästa steg att associera API: et med en produkt och publicera den så att utvecklare kan anropa åtgärderna.

* [Hur du skapar och publicerar en produkt][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
