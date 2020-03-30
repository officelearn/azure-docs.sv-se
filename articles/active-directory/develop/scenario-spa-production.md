---
title: Flytta ensidig app till produktion – Microsoft identity platform | Azure
description: Lär dig hur du skapar ett ensidigt program (flytta till produktion)
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
ms.openlocfilehash: 01b923e0d013fab1815456e55eac6036ded772bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701849"
---
# <a name="single-page-application-move-to-production"></a>Ensidigt program: Flytta till produktion

Nu när du vet hur du skaffar en token för att anropa webb-API:er kan du läsa om hur du flyttar till produktion.

## <a name="improve-your-app"></a>Förbättra din app

[Aktivera loggning](msal-logging.md) för att göra appproduktionen klar.

## <a name="test-your-integration"></a>Testa din integration

Testa integreringen genom att följa checklistan för integrering av [Microsoft-identitetsplattform](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Nästa steg

Djupdykning av snabbstartsprovet, som förklarar koden för hur du loggar in användare och får en åtkomsttoken för att anropa Microsoft Graph API med msal.js:

> [!div class="nextstepaction"]
> [Självstudiekurs för JavaScript SPA](./tutorial-v2-javascript-spa.md)

Exempel som visar hur du hämtar token för ditt eget backend-webb-API med MSAL.js:

> [!div class="nextstepaction"]
> [SPA med en ASP.NET backend](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Exempel på hur du använder MSAL.js för att logga in användare i en app som är registrerad med Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA med Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
