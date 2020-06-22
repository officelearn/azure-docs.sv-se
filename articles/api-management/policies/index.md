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
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b8f97bb27b3eb45c731b20ad51e35fde9b6a394a
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126916"
---
# <a name="api-management-policy-samples"></a>API Management-principexempel

[Principer](../api-management-howto-policies.md) är en kraftfull funktion i systemet som gör att utgivaren kan konfigurera om API:ets beteende. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Följande tabell innehåller länkar till exempel och ger en kort beskrivning av varje exempel.

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Inkommande principer**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [Lägg till en vidarebefordrad rubrik för att tillåta serverdels-API:et att skapa rätt URL:er](./set-header-to-enable-backend-to-construct-urls.md) | Visar hur du lägger till en vidarebefordrad rubrik i en inkommande begäran för att tillåta serverdels-API:en att skapa rätt URL:er.                                                                                                        |
| [Lägga till en rubrik som innehåller ett korrelations-id](./add-correlation-id.md)                                                             | Visar hur du lägger till en rubrik som innehåller ett korrelations-ID till en inkommande begäran.                                                                                                                                        |
| [Lägga till funktioner i en serverdelstjänst och cachelagra svaret](./cache-response.md)                                             | Visar hur du lägger till funktioner till en serverdelstjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API.                                                                    |
| [Auktorisera åtkomstbaserat på JWT-anspråk](./authorize-request-based-on-jwt-claims.md)                                              | Visar hur du beviljar åtkomst till specifika HTTP-metoder på en API som baseras på JWT-anspråk.                                                                                                                                       |
| [Auktorisera begäran med extern auktoriserare](./authorize-request-using-external-authorizer.md)                                                   | Visar hur du använder en extern auktoriserare för att skydda API-åtkomst.                                                                                                                                                               |
| [Auktorisera åtkomst med hjälp av Google OAuth-token](./use-google-as-oauth-token-provider.md)                                            | Visar hur du beviljar åtkomst till dina slutpunkter med Google som en OAuth-tokenprovider.                                                                                                                                    |
| [Filtrera IP-adresser när du använder en Application Gateway](./filter-ip-addresses-when-using-appgw.md) | Visar hur du filtrerar IP-adresser i principer när API Management-instansen nås via en Application Gateway
| [Generera en signatur för delad åtkomst och vidarebefordra begäran till Azure-lagring](./generate-shared-access-signature.md)                  | Visar hur du skapar [Signatur för delad åtkomst](../../storage/storage-dotnet-shared-access-signature-part-1.md) med uttryck och vidarebefordrar begäran till Azure-lagring med principer för omarbetnings-uri. |
| [Hämta OAuth2-token från AAD och vidarebefordra den till serverdelen](./use-oauth2-for-authorization.md)                             | Ger exempel på hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen.                                                    |
| [Hämta X-CSRF-token från SAP-gateway med hjälp av sändarbegärandeprincip](./get-x-csrf-token-from-sap-gateway.md)                           | Visar hur du implementerar X CSRF-mönster som används av många API:er. Det här exemplet är specifikt för SAP-gateway.                                                                                                                           |
| [Vidarebefordra begäran baserat på storleken på innehållet](./route-requests-based-on-size.md)                                            | Visar hur du dirigerar förfrågningar baserat på storleken på deras innehåll.                                                                                                                                                       |
| [Skicka begäran om sammanhangsinformation till serverdelstjänsten](./send-request-context-info-to-backend-service.md)                    | Visar hur du skickar viss sammanhangsinformation till serverdelstjänsten för loggning eller bearbetning.                                                                                                                                |
| [Ange varaktighet för svarscache](./set-cache-duration.md)                                                                          | Visar hur du ställer in varaktighet för svarscache med hjälp av maxAge-värdet i Cache-Control-huvudet som skickas av serverdelen.                                                                                                             |
| **Utgående principer**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Filtrera svarsinnehåll](./filter-response-content.md)                                                                         | Visar hur du filtrerar dataelement från svarets nyttolast baserat på produkten som är kopplad till begäran.                                                                                                        |
| **Principer vid fel**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Logga fel i Stackify](./log-errors-to-stackify.md)                                                                           | Visar hur du lägger till en felloggningsprincip om du vill skicka fel till Stackify för loggning.                                                                                                                                            |
