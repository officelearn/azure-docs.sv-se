---
title: Webbapp som loggar in användare (översikt) - Microsoft identity-plattformen
description: Lär dig att skapa en webbapp som loggar in användare (översikt)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46df5e4ec8352b47f744a507fad702c37aa5fba
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075107"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: Webbapp som loggar in användare

Lär dig allt du behöver skapa en webbapp som loggar in användare med Microsoft identity-plattformen.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Om du vill skapa din första bärbar (ASP.NET Core)-webbappar som kan logga in användare Följ den här snabbstarten:

> [!div class="nextstepaction"]
> [Snabbstart: ASP.NET Core webbapp som loggar in användare](quickstart-v2-aspnet-core-webapp.md)

Om du vill att hålla dig med ASP.NET kan prova att använda följande självstudie:

> [!div class="nextstepaction"]
> [Snabbstart: ASP.NET Core webbapp som loggar in användare](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Översikt

Du lägger till autentisering till din webbapp så att den kan logga in användare. Att lägga till autentisering gör det möjligt för din webbapp att komma åt profilinformation och exempelvis anpassa upplevelsen du erbjuda användarna. Webbappar autentisera en användare i en webbläsare. I det här scenariot uppmanar webbprogrammet användarens webbläsare att logga in dem till Azure AD. Azure AD returnerar ett svar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Logga in användare utnyttjar den [öppna ID Connect](./v2-protocols-oidc.md) standardprotokollet själva förenklad med hjälp av mellanprogram [bibliotek](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Web app loggar in användare](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Du kan också aktivera programmet att anropa webb-API: er åt den inloggade användaren som en andra fasen. Den här nästa fas är ett annat scenario där du hittar i [Web App anropar webb-API: er](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Lägga till inloggning till ett webb-app är om hur du skyddar webbappen och validerar en användartoken, vilket är vad **mellanprogram** bibliotek gör. Det här scenariot kräver inte ännu Microsoft Authentication Libraries (MSAL) som håller på att skaffa en token som kan anropa skyddade API: er. Authentication Library kommer endast att presenteras i Uppföljnings scenariot när webbappen behöver anropa webb-API: er.

## <a name="specifics"></a>Närmare information

- Under registreringen programmet måste du ange en eller flera (om du distribuerar din app till flera platser) svars-URI: er. I vissa fall (ASP.NET/ASP.NET kärnor) måste du aktivera IDToken. Slutligen bör du ställa in en utloggning URI så att ditt program som reagerar på användare loggar ut.
- I koden för ditt program måste du ange utfärdaren som web app delegerar logga in. Du kanske vill anpassa tokenvalidering (särskilt i ISV-scenarier).
- Webbprogram stöd för alla kontotyper. Mer information finns i [stöds kontotyper](v2-supported-account-types.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-web-app-sign-user-app-registration.md)
