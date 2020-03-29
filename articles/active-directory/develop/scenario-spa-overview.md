---
title: Scenario med ensidig app i JavaScript – Microsoft identity platform | Azure
description: Lär dig hur du skapar ett ensidigt program (scenarioöversikt) med hjälp av Microsofts identitetsplattform.
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
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701883"
---
# <a name="scenario-single-page-application"></a>Scenario: Ensidig applikation

Lär dig allt du behöver för att bygga ett ensidigt program (SPA).

## <a name="prerequisites"></a>Krav

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Du kan skapa ditt första program genom att följa snabbstarten för JavaScript SPA:

> [!div class="nextstepaction"]
> [Snabbstart: Ensidig applikation](./quickstart-v2-javascript.md)

## <a name="overview"></a>Översikt

Många moderna webbapplikationer är byggda som ensidiga program på klientsidan. Utvecklare skriver dem med javascript eller ett SPA-ramverk som Angular, Vue.js och React.js. Dessa program körs i en webbläsare och har andra autentiseringsegenskaper än traditionella webbprogram på serversidan. 

Microsofts identitetsplattform gör det möjligt för ensidiga program att logga in användare och få token för att komma åt backend-tjänster eller webb-API:er med hjälp av [det implicita flödet OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Det implicita flödet gör att programmet kan hämta ID-token för att representera den autentiserade användaren och även komma åt token som behövs för att anropa skyddade API:er.

![Ensidiga program](./media/scenarios/spa-app.svg)

Det här autentiseringsflödet innehåller inte programscenarier som använder JavaScript-ramverk över flera plattformar, till exempel Elektron och React-Native. De kräver ytterligare funktioner för interaktion med de inbyggda plattformarna.

## <a name="specifics"></a>Detaljerna

Om du vill aktivera det här scenariot för ditt program behöver du:

* Programregistrering med Azure Active Directory (Azure AD). Den här registreringen innebär att det implicita flödet aktiveras och en omdirigerings-URI som token returneras till.
* Programkonfiguration med registrerade programegenskaper, till exempel program-ID.
* Använda Microsoft Authentication Library (MSAL) för att göra autentiseringsflödet för att logga in och hämta token.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-spa-app-registration.md)
