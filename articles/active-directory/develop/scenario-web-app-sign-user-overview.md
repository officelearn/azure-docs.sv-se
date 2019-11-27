---
title: Webbapp som loggar in användare (översikt) – Microsoft Identity Platform
description: Lär dig hur du skapar en webbapp som loggar in användare (översikt)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316ab055a077b251e88421ab26997f8556a6e31f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482475"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: webb program som loggar in användare

Lär dig allt du behöver för att bygga en webbapp som använder Microsoft Identity Platform för att logga in användare.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Följ den här snabb starten om du vill skapa din första bärbara (ASP.NET Core)-webbapp som loggar in användare:

> [!div class="nextstepaction"]
> [Snabb start: ASP.NET Core webbapp som loggar in användare](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Om du vill veta hur du lägger till inloggning i ett befintligt ASP.NET-webbprogram, prova följande snabb start:

> [!div class="nextstepaction"]
> [Snabb start: ASP.NET-webbapp som loggar in användare](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Om du är en Java-utvecklare kan du prova följande snabb start:

> [!div class="nextstepaction"]
> [Snabb start: lägga till inloggning med Microsoft i en Java-webbapp](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Om du utvecklar med python kan du prova följande snabb start:

> [!div class="nextstepaction"]
> [Snabb start: lägga till inloggning med Microsoft i en python-webbapp](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Översikt

Du lägger till autentisering till din webbapp så att den kan logga in användare. Genom att lägga till autentisering kan din webbapp komma åt begränsad profil information för att anpassa användar upplevelsen. 

Web Apps autentiserar en användare i en webbläsare. I det här scenariot dirigerar webbappen användarens webbläsare för att logga in till Azure Active Directory (Azure AD). Azure AD returnerar ett inloggnings svar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Inloggnings användare drar nytta av [Open ID Connect](./v2-protocols-oidc.md) standard-protokollet, vilket är förenklat genom användning av mellanprogram [bibliotek](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps).

![Webbapp loggar in användare](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Som en andra fas kan du aktivera ditt program så att det anropar webb-API: er åt den inloggade användaren. Nästa fas är ett annat scenario, som du hittar i [webb program som anropar webb-API: er](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Att lägga till inloggning i en webbapp är att skydda webbappen och verifiera en användartoken, vilket är vad **mellan** -program varu bibliotek gör. I fallet med .NET kräver det här scenariot ännu inte Microsoft Authentication Library (MSAL), som handlar om att förvärva en token för att anropa skyddade API: er. Autentiseringsscheman kommer att införas i uppföljnings scenariot när webbappen behöver anropa webb-API: er.

## <a name="specifics"></a>Information

- Under program registreringen måste du ange en eller flera (om du distribuerar appen till flera platser) svars-URI: er. I vissa fall (ASP.NET och ASP.NET Core) måste du aktivera ID-token. Slutligen vill du skapa en utloggnings-URI så att ditt program reagerar på användare som loggar ut.
- I koden för ditt program måste du ange den myndighet som webbappen delegerar inloggning för. Du kanske vill anpassa verifiering av token (särskilt i partner scenarier).
- Webb program stöder alla typer av konton. Mer information finns i [konto typer som stöds](v2-supported-account-types.md).

## <a name="next-steps"></a>Nästa steg

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Appregistrering](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
