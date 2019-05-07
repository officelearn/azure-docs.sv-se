---
title: Ensidesapplikation (flytta till produktion) - Microsoft identity-plattformen
description: Lär dig hur du skapar ett enkelsidigt program (flytta till produktion)
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
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075017"
---
# <a name="single-page-application---move-to-production"></a>Ensidesprogram – flytta till produktion

Nu när du vet hur du skaffa en token för att anropa webb-API: er lär du dig hur du flyttar till produktion.

## <a name="improve-your-app"></a>Förbättra din app

Följ stegen som behövs för att förbereda din app i produktionsmiljön.

- [Aktivera loggning](msal-logging.md) i ditt program.

## <a name="test-your-integration"></a>Testa din integrering

- Testa din integrering genom att följa den [Microsoft identity-plattformen integration checklista](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Nästa steg

Här följer några andra exempel/Självstudier:

- Exempel som förklarar koden att logga in användare och få en åtkomsttoken att anropa MS Graph API med hjälp av MSAL.js för en djupdykning i Snabbstart

    > [!div class="nextstepaction"]
    > [JavaScript SPA-självstudien](./tutorial-v2-javascript-spa.md)

- Exemplet visar hur du hämtar token för din egen backend webb-API med hjälp av MSAL.js

     > [!div class="nextstepaction"]
     > [SPA med en ASP.NET-serverdel](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Exemplet för att visa hur du använder MSAL.js för att logga in användare i en app som har registrerats med Azure AD B2C

    > [!div class="nextstepaction"]
    > [SPA med Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
