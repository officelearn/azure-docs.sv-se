---
title: 'Skapa en webbapp som anropar webb-API: er - Microsoft identity platform | Azure'
description: Lär dig hur du skapar en webbapp som anropar webb-API:er (översikt)
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
ms.openlocfilehash: 14f513306b3f0bc0c06a4143e5174c3ecddaef62
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617170"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: En webbapp som anropar webb-API:er

Lär dig hur du skapar en webbapp som loggar in användare på Microsofts identitetsplattform och anropar sedan webb-API:er för den inloggade användarens räkning.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Det här scenariot förutsätter att du redan har gått igenom följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Översikt

Du lägger till autentisering i webbappen så att den kan logga in användare och anropa ett webb-API för den inloggade användarens räkning.

![Webbapp som anropar webb-API:er](./media/scenario-webapp/web-app.svg)

Webbappar som anropar webb-API:er är konfidentiella klientprogram.
Det är därför de registrerar en hemlighet (ett programlösenord eller certifikat) med Azure Active Directory (Azure AD). Den här hemligheten skickas in under anropet till Azure AD för att hämta en token.

## <a name="specifics"></a>Detaljerna

> [!NOTE]
> Att lägga till inloggning i en webbapp handlar om att skydda själva webbappen. Detta skydd uppnås genom att använda *middleware-bibliotek,* inte Microsoft Authentication Library (MSAL). Föregående scenario, [webbapp som loggar in användare](scenario-web-app-sign-user-overview.md), täckte ämnet.
>
> Det här scenariot beskriver hur du anropar webb-API:er från en webbapp. Du måste få åtkomsttoken för webb-API:erna. Du kan använda MSAL-bibliotek för att hämta dessa token.

Utveckling för det här scenariot omfattar dessa specifika uppgifter:

- Under [programregistreringen](scenario-web-app-call-api-app-registration.md)måste du ange ett svar URI, hemlighet eller certifikat som ska delas med Azure AD. Om du distribuerar appen på flera platser anger du den här informationen för varje plats.
- [Programkonfigurationen](scenario-web-app-call-api-app-configuration.md) måste ange klientautentiseringsuppgifterna som delades med Azure AD under programregistreringen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [En webbapp som anropar webb-API:er: Appregistrering](scenario-web-app-call-api-app-registration.md)
