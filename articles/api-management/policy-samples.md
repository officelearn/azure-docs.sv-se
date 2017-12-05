---
title: Azure API Management princip-exempel | Microsoft Docs
description: "Läs mer om principerna som är tillgängliga för användning i Azure API Management."
services: api-management
documentationcenter: 
author: Juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: apimpm
ms.openlocfilehash: 0e8089cbcc5e38504d6b4c7ced372781f9a5e6d8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="api-management-policy-samples"></a>Exempel för API Management-princip

[Principer för](api-management-howto-policies.md) är en kraftfull funktion för systemet som gör att utgivaren för att ändra funktionssättet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Följande tabell innehåller länkar till exempel och ger en kort beskrivning av varje prov.

|||
|---|---|
|**Inkommande principer**||
|[Lägga till ett sidhuvud vidarebefordrade för att tillåta serverdelen API för att skapa rätt URL: er](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |Visar hur du lägger till en vidarebefordrade rubrik i en inkommande begäran att serverdelen API för att skapa rätt URL: er.|
|[Lägga till ett sidhuvud som innehåller en Korrelations-id](./policies/add-correlation-id.md?toc=api-management/toc.json) |Visar hur du lägger till en rubrik som innehåller en Korrelations-ID till en inkommande begäran.|
|[Lägga till funktioner i en serverdelstjänst och cachelagrar svaret](./policies/cache-response.md?toc=api-management/toc.json) |Visar hur du lägger till funktioner till en backend-tjänst. Godkänn exempelvis ett namn på plats i stället för latitud och longitud i en väder prognos API.|
|[Auktorisera åtkomst baserat på JWT anspråk](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Visar hur du bevilja åtkomst till specifika HTTP-metoder på en API som baseras på JWT-anspråk.|
|[Auktorisera åtkomst med hjälp av Google OAuth-token](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Visar hur du bevilja åtkomst till dina slutpunkter med Google som en OAuth-token-provider.|
|[Generera en signatur för delad åtkomst och vidarebefordra begäran till Azure-lagring](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Visar hur du skapar [signatur för delad åtkomst](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) med uttryck och vidarebefordrar begäran till Azure-lagring med omarbetning-uri-principer. |
|[Hämta OAuth2-token från AAD och vidarebefordra den till serverdelen](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Ger exempel på hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Den visar hur du hämtar en åtkomst-token från AAD och vidarebefordra dem till serverdelen.|
|[Hämta X-CSRF token från SAP-gateway med hjälp av skicka begäran princip](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Visar hur du implementerar X CSRF mönster som används av många API: er. Det här exemplet är specifika för SAP-Gateway. |
|[Vidarebefordra begäran baserat på storleken på innehållet](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Visar hur du dirigera förfrågningar baserat på storleken på deras organ.|
|[Skicka information om åtkomstbegäran kontext till serverdelstjänsten](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |Visar hur du skickar vissa sammanhangsinformation till backend-tjänsten för loggning eller bearbetning.|
|[Ange varaktighet för svar på cache](./policies/set-cache-duration.md?toc=api-management/toc.json) |Visar hur du ställer in varaktighet för svar på cache med hjälp av maxAge värdet i Cache-Control-huvudet som skickas av serverdelen.|
|**Utgående principer**||
|[Filtrera svar innehåll](./policies/filter-response-content.md?toc=api-management/toc.json) | Visar hur du filtrerar dataelement från svar nyttolasten baserat på produkten som är kopplad till begäran.|
|**Vid fel-principer**||
|[Logga fel i Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning.|
