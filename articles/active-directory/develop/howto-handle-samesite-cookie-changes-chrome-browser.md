---
title: Så här hanterar du ändringar av SameSite-cookie i webbläsaren Chrome | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du hanterar ändringar av SameSite-cookies i webbläsaren Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534609"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Hantera SameSite-cookieändringar i webbläsaren Chrome

## <a name="what-is-samesite"></a>Vad är SameSite?

`SameSite`är en egenskap som kan ställas in i HTTP-cookies för att förhindra attacker mot cross site request forgery(CSRF) i webbprogram:

- När `SameSite` är inställt på **Lax**skickas cookien i förfrågningar på samma webbplats och i GET-förfrågningar från andra webbplatser. Det skickas inte i GET-begäranden som är korsdomäner.
- Värdet **Strict** säkerställer att cookien skickas i begäranden endast på samma webbplats.

Som standard `SameSite` anges inte värdet i webbläsare och det är därför det inte finns några begränsningar för cookies som skickas i begäranden. En ansökan skulle behöva välja att csrf-skyddet genom att ange **Lax** eller **Strict** enligt deras krav.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite ändringar och inverkan på autentisering

De senaste [uppdateringarna av standarderna på SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) föreslår att du skyddar appar genom att standardbeteendet `SameSite` för när inget värde är inställt på Lax. Denna begränsning innebär att cookies kommer att begränsas på HTTP-begäranden utom GET från andra webbplatser. Dessutom introduceras värdet **Ingen** för att ta bort begränsningar för cookies som skickas. Dessa uppdateringar kommer snart att släppas i en kommande version av webbläsaren Chrome.

När webbappar autentiseras med Microsoft Identity-plattformen med svarsläget "form_post" svarar inloggningsservern på programmet med hjälp av ett HTTP-POST för att skicka token eller autentiseringskod. Eftersom den här begäran är en `login.microsoftonline.com` begäran mellan domäner (från till exempel `https://contoso.com/auth`till din domän ) omfattas nu cookies som har angetts av din app av de nya reglerna i Chrome. De cookies som måste användas i scenarier över flera webbplatser är cookies som innehåller *värdena tillstånd* och *nonce,* som också skickas i inloggningsbegäran. Det finns andra cookies som har tagits bort av Azure AD för att hålla sessionen.

Om du inte uppdaterar webbapparna resulterar det här nya beteendet i autentiseringsfel.

## <a name="mitigation-and-samples"></a>Begränsning och prover

För att lösa autentiseringsfelen kan webbappar som `SameSite` autentiserar med Microsofts identitetsplattform ställa in egenskapen på `None` för cookies som används i scenarier mellan domäner när de körs i webbläsaren Chrome.
Andra webbläsare (se [här](https://www.chromium.org/updates/same-site/incompatible-clients) för en fullständig lista) följer det tidigare beteendet `SameSite` hos och kommer inte att inkludera cookies om `SameSite=None` de är inställda.
Det är därför, för att stödja autentisering på flera `SameSite` webbläsare `None` webbappar måste ställa in värdet till endast på Chrome och lämna värdet tomt på andra webbläsare.

Detta tillvägagångssätt visas i våra kodprover nedan.

# <a name="net"></a>[.NET](#tab/dotnet)

Tabellen nedan visar pull-begäranden som fungerade kring SameSite-ändringarna i våra ASP.NET- och ASP.NET Core-exempel.

| Exempel | Pull-begäran |
| ------ | ------------ |
|  [ASP.NET Core-självstudiekurs för webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Samma lösning av webbplatscookie #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET exempel på MVC-webbapp](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Samma korrigering av webbplatscookie #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Samma korrigering av webbplatscookie #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

för mer information om hur du hanterar SameSite cookies i ASP.NET och ASP.NET Core, se även:

- [Arbeta med SameSite cookies i ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET Blogg på SameSite fråga](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Exempel |
| ------ |
|  [ms-identitet-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Exempel | Pull-begäran |
| ------ | ------------ |
|  [ms-identitet-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Samma korrigering av webbplatscookie #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identitet-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Samma korrigering av webbplatscookie #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Nästa steg

Läs mer om SameSite och webbappscenariot:

> [!div class="nextstepaction"]
> [Vanliga frågor om Google Chrome på SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Krom SameSite sida](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scenario: Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)