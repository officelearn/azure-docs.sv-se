---
title: Azure API Management-principexempel | Microsoft Docs
description: Läs mer om principerna som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 550161ce39aa944d0e01bb349ba48acbf719a860
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859383"
---
# <a name="api-management-policy-samples"></a>API Management-principexempel

[Principer](api-management-howto-policies.md) är en kraftfull funktion i systemet som gör att utgivaren kan konfigurera om API:ets beteende. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Följande tabell innehåller länkar till exempel och ger en kort beskrivning av varje exempel.

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Inkommande principer**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [Lägg till en vidarebefordrad rubrik för att tillåta serverdels-API:et att skapa rätt URL:er](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) | Visar hur du lägger till en vidarebefordrad rubrik i en inkommande begäran för att tillåta serverdels-API:en att skapa rätt URL:er.                                                                                                        |
| [Lägga till en rubrik som innehåller ett korrelations-id](./policies/add-correlation-id.md?toc=api-management/toc.json)                                                             | Visar hur du lägger till en rubrik som innehåller ett korrelations-ID till en inkommande begäran.                                                                                                                                        |
| [Lägga till funktioner i en serverdelstjänst och cachelagra svaret](./policies/cache-response.md?toc=api-management/toc.json)                                             | Visar hur du lägger till funktioner till en serverdelstjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API.                                                                    |
| [Auktorisera åtkomstbaserat på JWT-anspråk](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json)                                              | Visar hur du beviljar åtkomst till specifika HTTP-metoder på en API som baseras på JWT-anspråk.                                                                                                                                       |
| [Auktorisera begäran med extern auktoriserare](./policies/authorize-request-using-external-authorizer.md)                                                   | Visar hur du använder en extern auktoriserare för att skydda API-åtkomst.                                                                                                                                                               |
| [Auktorisera åtkomst med hjälp av Google OAuth-token](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json)                                            | Visar hur du beviljar åtkomst till dina slutpunkter med Google som en OAuth-tokenprovider.                                                                                                                                    |
| [Generera en signatur för delad åtkomst och vidarebefordra begäran till Azure-lagring](./policies/generate-shared-access-signature.md?toc=api-management/toc.json)                  | Visar hur du skapar [Signatur för delad åtkomst](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) med uttryck och vidarebefordrar begäran till Azure-lagring med principer för omarbetnings-uri. |
| [Hämta OAuth2-token från AAD och vidarebefordra den till serverdelen](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json)                             | Ger exempel på hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen.                                                    |
| [Hämta X-CSRF-token från SAP-gateway med hjälp av sändarbegärandeprincip](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json)                           | Visar hur du implementerar X CSRF-mönster som används av många API:er. Det här exemplet är specifikt för SAP-gateway.                                                                                                                           |
| [Vidarebefordra begäran baserat på storleken på innehållet](./policies/route-requests-based-on-size.md?toc=api-management/toc.json)                                            | Visar hur du dirigerar förfrågningar baserat på storleken på deras innehåll.                                                                                                                                                       |
| [Skicka begäran om sammanhangsinformation till serverdelstjänsten](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json)                    | Visar hur du skickar viss sammanhangsinformation till serverdelstjänsten för loggning eller bearbetning.                                                                                                                                |
| [Ange varaktighet för svarscache](./policies/set-cache-duration.md?toc=api-management/toc.json)                                                                          | Visar hur du ställer in varaktighet för svarscache med hjälp av maxAge-värdet i Cache-Control-huvudet som skickas av serverdelen.                                                                                                             |
| **Utgående principer**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Filtrera svarsinnehåll](./policies/filter-response-content.md?toc=api-management/toc.json)                                                                         | Visar hur du filtrerar dataelement från svarets nyttolast baserat på produkten som är kopplad till begäran.                                                                                                        |
| **Principer vid fel**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Logga fel i Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json)                                                                           | Visar hur du lägger till en felloggningsprincip om du vill skicka fel till Stackify för loggning.                                                                                                                                            |
