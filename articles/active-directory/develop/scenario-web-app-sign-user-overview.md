---
title: Logga in användare från en webbapp – Microsoft identity platform | Azure
description: Läs om hur du skapar en webbapp som loggar in användare (översikt)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 65d254cec5735c54e19f5adfde57fb6aed776a2c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881493"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: Webbapp som loggar in användare

Lär dig allt du behöver för att skapa en webbapp som använder Microsofts identitetsplattform för att logga in användare.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Om du vill skapa din första bärbara (ASP.NET Core) webbapp som loggar in användare följer du den här snabbstarten:

> [!div class="nextstepaction"]
> [Snabbstart: ASP.NET Core-webbapp som loggar in användare](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Om du vill förstå hur du lägger till inloggning i ett befintligt ASP.NET webbprogram provar du följande snabbstart:

> [!div class="nextstepaction"]
> [Snabbstart: ASP.NET webbapp som loggar in användare](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Om du är Java-utvecklare provar du följande snabbstart:

> [!div class="nextstepaction"]
> [Snabbstart: Lägga till inloggning med Microsoft i en Java-webbapp](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Om du utvecklar med Python kan du prova följande snabbstart:

> [!div class="nextstepaction"]
> [Snabbstart: Lägga till inloggning med Microsoft i en Python-webbapp](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Översikt

Du lägger till autentisering i webbappen så att den kan logga in användare. Genom att lägga till autentisering kan webbappen komma åt begränsad profilinformation för att anpassa upplevelsen för användarna. 

Webbappar autentiserar en användare i en webbläsare. I det här fallet styr webbappen användarens webbläsare för att logga in dem i Azure Active Directory (Azure AD). Azure AD returnerar ett inloggningssvar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. När du loggar in användare kan du dra nytta av standardprotokollet [Open ID Connect,](./v2-protocols-oidc.md) förenklat med hjälp av middleware-bibliotek . [libraries](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)

![Webbappskyltar hos användare](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Som en andra fas kan du aktivera ditt program för att anropa webb-API:er för den inloggade användarens räkning. Nästa fas är ett annat scenario, som du hittar i [webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Att lägga till inloggning i en webbapp handlar om att skydda webbappen och validera en användartoken, vilket är vad **middleware-bibliotek** gör. När det gäller .NET kräver det här scenariot ännu inte Microsoft Authentication Library (MSAL), som handlar om att hämta en token för att anropa skyddade API:er. Autentiseringsbibliotek introduceras i uppföljningsscenariot när webbappen behöver anropa webb-API:er.

## <a name="specifics"></a>Detaljerna

- Under programregistreringen måste du ange en eller flera (om du distribuerar appen till flera platser) svara URI:er. I vissa fall (ASP.NET och ASP.NET Core) måste du aktivera ID-token. Slutligen vill du konfigurera en utskrivnings-URI så att programmet reagerar på användare som loggar ut.
- I koden för ditt program måste du ange den behörighet som webbappen delegerar logga in på. Du kanske vill anpassa tokenverifiering (i synnerhet i partnerscenarier).
- Webbprogram stöder alla kontotyper. Mer information finns i [Kontotyper som stöds](v2-supported-account-types.md).

## <a name="next-steps"></a>Nästa steg

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
