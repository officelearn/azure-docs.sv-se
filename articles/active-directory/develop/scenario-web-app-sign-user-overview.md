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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b898a93b87811fa5139e148a3273d7051af851b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056280"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: Webbapp som loggar in användare

Lär dig allt du behöver för att bygga en webbapp som loggar in användare med Microsoft Identity Platform.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Följ den här snabb starten om du vill skapa dina första bärbara (ASP.NET Core)-webbappar som loggar in användare:

> [!div class="nextstepaction"]
> [Snabbstart: ASP.NET Core-webbapp som loggar in användare](quickstart-v2-aspnet-core-webapp.md)

Om du vill fortsätta med ASP.NET kan du prova följande självstudie:

> [!div class="nextstepaction"]
> [Snabbstart: ASP.NET-webbapp som loggar in användare](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Översikt

Du lägger till autentisering i din webbapp så att den kan logga in användare. Genom att lägga till autentisering kan din webbapp få åtkomst till begränsad profil information, och till exempel anpassa den upplevelse som du erbjuder till sina användare. Web Apps autentiserar en användare i en webbläsare. I det här scenariot dirigerar webb programmet användarens webbläsare för att logga in på Azure AD. Azure AD returnerar ett inloggnings svar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Inloggnings användare använder sig av det [Öppna ID: t Connect-](./v2-protocols-oidc.md) standardprotokollet som är förenklat genom användning av mellanprogram [bibliotek](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Webb program loggar – användare](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Som en andra fas kan du också aktivera ditt program så att det anropar webb-API: er åt den inloggade användaren. Nästa fas är ett annat scenario, som du hittar i webb- [API: er för webb program](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Att lägga till inloggning i en webbapp är att skydda webbappen och verifiera en användartoken, vilket är vad **mellan** program varu bibliotek gör. Det här scenariot kräver inte Microsoft Authentication libraries (MSAL), som håller på att förvärva en token för att anropa skyddade API: er. Autentiseringsinställningarna kommer bara att införas i uppföljnings scenariot när webbappen behöver anropa webb-API: er.

## <a name="specifics"></a>Information

- Under program registreringen måste du ange en eller flera (om du distribuerar appen till flera platser) svars-URI: er. I vissa fall (ASP.NET/ASP.NET Core) måste du aktivera ID-token. Slutligen vill du skapa en utloggnings-URI så att ditt program reagerar på användare som loggar ut.
- I koden för ditt program måste du ange den myndighet som du vill att webbappen delegerar inloggning för. Du kanske vill anpassa verifiering av token (särskilt i ISV-scenarier).
- Webb program stöder alla typer av konton. Mer information finns i [konto typer som stöds](v2-supported-account-types.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-web-app-sign-user-app-registration.md)
