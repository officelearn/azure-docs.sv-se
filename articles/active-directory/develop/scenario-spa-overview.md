---
title: Java Script – översikt över program scenario med en sida – Microsoft Identity Platform
description: Lär dig hur du skapar ett program med en enda sida (scenario översikt) med hjälp av Microsoft Identity Platform.
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
ms.openlocfilehash: 77f59e8c628d7ba37aaf258541664e40e1d4a2dc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764686"
---
# <a name="scenario-single-page-application"></a>Scenario: ett program med en sida

Lär dig allt du behöver för att bygga ett enda webb program (SPA).

## <a name="prerequisites"></a>Krav

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Du kan skapa ditt första program genom att följa Java Script SPA-snabb starten:

> [!div class="nextstepaction"]
> [Snabb start: ett program på en sida](./quickstart-v2-javascript.md)

## <a name="overview"></a>Översikt

Många moderna webb program skapas som program på en enda sida med klient sidan. Utvecklare skriver dem med hjälp av Java Script eller ett SPA-ramverk, till exempel vinkel, Vue. js och reagerar. js. Dessa program körs i en webbläsare och har olika egenskaper för autentisering än traditionella webb program på Server sidan. 

Microsoft Identity Platform gör det möjligt för program på en sida att logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er genom att använda det [implicita flödet i OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Med det implicita flödet kan programmet Hämta ID-token som representerar den autentiserade användaren och även få åtkomst till tokens som krävs för att anropa skyddade API: er.

![Program med en enda sida](./media/scenarios/spa-app.svg)

Det här autentiseringsschemat omfattar inte program scenarier som använder plattforms oberoende JavaScript-ramverk som Electron och reagerar-Native. De kräver ytterligare funktioner för interaktion med de ursprungliga plattformarna.

## <a name="specifics"></a>Information

Om du vill aktivera det här scenariot för ditt program behöver du:

* Program registrering med Azure Active Directory (Azure AD). Den här registreringen innebär att aktivera det implicita flödet och ange en omdirigerings-URI som tokens returneras till.
* Program konfiguration med de registrerade program egenskaperna, t. ex. program-ID.
* Använd Microsoft Authentication Library (MSAL) för att utföra autentiserings flödet för att logga in och hämta token.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-spa-app-registration.md)
