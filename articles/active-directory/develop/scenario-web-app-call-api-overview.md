---
title: 'Bygg en webbapp som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (översikt)'
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
ms.openlocfilehash: d121d6c198cb0d92cd098a40096e2f2300f65537
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758997"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: en webbapp som anropar webb-API: er

Lär dig hur du skapar en webbapp som loggar användare i Microsoft Identity Platform och anropar webb-API: er åt den inloggade användaren.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Det här scenariot förutsätter att du redan har gått igenom följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Översikt

Du lägger till autentisering till din webbapp så att den kan signera användare i och anropa ett webb-API för den inloggade användarens räkning.

![Webbapp som anropar webb-API:er](./media/scenario-webapp/web-app.svg)

Webb program som anropar webb-API: er är konfidentiella klient program.
Därför registrerar de en hemlighet (ett program lösen ord eller certifikat) med Azure Active Directory (Azure AD). Den här hemligheten skickas under anropet till Azure AD för att hämta en token.

## <a name="specifics"></a>Information

> [!NOTE]
> Att lägga till inloggning i en webbapp är att skydda själva webbappen. Skyddet uppnås med hjälp av *mellanprogram* bibliotek, inte Microsoft Authentication Library (MSAL). Föregående scenario, [webbapp som loggar in användare](scenario-web-app-sign-user-overview.md), och som omfattas av ämnet.
>
> Det här scenariot beskriver hur du anropar webb-API: er från en webbapp. Du måste hämta åtkomsttoken för dessa webb-API: er. Du kan hämta dessa token genom att använda MSAL-bibliotek för att hämta dessa tokens.

Utvecklingen av det här scenariot omfattar följande uppgifter:

- Under [program registrering](scenario-web-app-call-api-app-registration.md)måste du ange en svars-URI, en hemlighet eller ett certifikat som ska delas med Azure AD. Om du distribuerar din app till flera platser ger du den här informationen för varje plats.
- [Program konfigurationen](scenario-web-app-call-api-app-configuration.md) måste ange de klientautentiseringsuppgifter som delats med Azure AD under program registreringen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [En webbapp som anropar webb-API: app-registrering](scenario-web-app-call-api-app-registration.md)
