---
title: Scenarioöversikt för JavaScript - ensidesapplikation Microsoft identity-plattformen
description: Lär dig hur du skapar ett enkelsidigt program (scenarioöversikt) som integrerar Microsoft identity-plattformen.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076367"
---
# <a name="scenario-single-page-application"></a>Scenario: Enkelsidig app

Lär dig allt du behöver för att skapa en ensidesapplikation (SPA).

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Du kan skapa ditt första program genom att följa JavaScript SPA-Snabbstart:

> [!div class="nextstepaction"]
> [Snabbstart: Ensidesapplikation](./quickstart-v2-javascript.md)

## <a name="overview"></a>Översikt

Många moderna webbprogram skapas med klientsidan enkelsidigt program som skrivits med JavaScript- eller en SPA-ramverk som Angular och Vue.js React.js. Dessa program körs i en webbläsare och har olika egenskaper än traditionella serversidan webbprogram. Microsoft identity-plattformen gör det möjligt för enkelsidigt program att logga in användare och hämta token för åtkomst till backend-tjänster eller webb-API: er med hjälp av den [OAuth 2.0 implicit flöde](./v2-oauth2-implicit-grant-flow.md). Det implicita flödet kan programmet för att hämta ID-token som representerar den autentiserade användaren och även åtkomsttoken krävs för att anropa API: er för skyddade.

![Enkelsidigt program](./media/scenarios/spa-app.svg)

Det här autentiseringsflödet inkluderar inte Programscenarier med hjälp av JavaScript-ramverk för flera plattformar t.ex, React-intern och så vidare. eftersom de kräver ytterligare funktioner för interaktion med de inbyggda plattformarna.

## <a name="specifics"></a>Närmare information

Följande aspekter krävs för att aktivera det här scenariot för ditt program:

* Programregistrering med Azure AD innebär att aktivera det implicita flödet och ange en omdirigerings-URI som token returneras.
* Programkonfiguration med registrerade programmet egenskaper, till exempel program-ID.
* Med MSAL-biblioteket för att göra autentiseringsflöde att logga in och hämta token.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-spa-app-registration.md)
