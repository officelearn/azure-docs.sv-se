---
title: Java Script – översikt över program scenario med en sida – Microsoft Identity Platform
description: Lär dig hur du skapar ett program med en enda sida (scenario översikt) som integrerar Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852523"
---
# <a name="scenario-single-page-application"></a>Scenario: Enkelsidig app

Lär dig allt du behöver för att bygga ett enda webb program (SPA).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Du kan skapa ditt första program genom att följa Java Script SPA-snabb starten:

> [!div class="nextstepaction"]
> [Snabbstart: Program med en sida](./quickstart-v2-javascript.md)

## <a name="overview"></a>Översikt

Många moderna webb program är skapade för program på klient sidan som skrivits med hjälp av Java Script eller ett SPA-ramverk, till exempel vinkel, Vue. js och reagerar. js. Dessa program körs i en webbläsare och har olika egenskaper för autentisering än traditionella webb program på Server sidan. Microsoft Identity Platform gör det möjligt för program på en sida att logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er med det [implicita flödet i OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Med det implicita flödet kan programmet Hämta ID-token som representerar den autentiserade användaren och även få åtkomst till tokens som krävs för att anropa skyddade API: er.

![Program med en enda sida](./media/scenarios/spa-app.svg)

Det här autentiseringsschemat omfattar inte program scenarier som använder plattforms oberoende JavaScript-ramverk som Electron, reagerar-Native och så vidare. eftersom de kräver ytterligare funktioner för interaktion med de ursprungliga plattformarna.

## <a name="specifics"></a>Information

Följande aspekter krävs för att aktivera det här scenariot för ditt program:

* Program registrering med Azure AD innebär att aktivera det implicita flödet och ange en omdirigerings-URI som tokens returneras till.
* Program konfiguration med de registrerade program egenskaperna, till exempel program-ID.
* Använd MSAL-bibliotek för att utföra auth-flödet för att logga in och hämta tokens.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-spa-app-registration.md)
