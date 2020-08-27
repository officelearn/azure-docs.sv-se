---
title: Flytta en app med en enda sida till produktion – Microsoft Identity Platform | Azure
description: Lär dig hur du skapar ett program med en sida (flytta till produktion)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 21ba0193c3f1e19ffc74452aaceee34759c7e606
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949022"
---
# <a name="single-page-application-move-to-production"></a>Program med en sida: flytta till produktion

Nu när du vet hur du kan hämta en token för att anropa webb-API: er, lär du dig hur du flyttar till produktion.

## <a name="improve-your-app"></a>Förbättra din app

[Aktivera loggning](msal-logging.md) för att göra din app-produktion klar.

## <a name="test-your-integration"></a>Testa din integrering

Testa din integrering genom att följa [Check lista för Microsoft Identity Platform-integration](identity-platform-integration-checklist.md).

## <a name="deploy-your-app"></a>Distribuera din app

Kolla in ett [distributions exempel](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) för att lära dig hur du distribuerar dina spa-och webb-API-projekt med Azure Storage-och Azure App-tjänster. 

## <a name="next-steps"></a>Nästa steg

Djupgående snabb starts exempel, som förklarar koden för att logga in användare och hämta en åtkomsttoken för att anropa Microsoft Graph-API: **et** genom att använda **MSAL.js**:

> [!div class="nextstepaction"]
> [Själv studie kurs för JavaScript-SPA](./tutorial-v2-javascript-spa.md)

Exempel som visar hur du hämtar token för din egen server dels webb-API (ASP.NET Core) genom att använda **MSAL.js**:

> [!div class="nextstepaction"]
> [SPA med en ASP.NET Server del](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Exempel som visar hur du verifierar åtkomsttoken för ditt Server dels webb-API (Node.js) med hjälp av **Passport-Azure-AD**.

> [!div class="nextstepaction"]
> [Node.js webb-API (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)

Exempel som visar hur du använder **MSAL.js** för att logga in användare i en app som har registrerats med **Azure Active Directory B2C** (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA med Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

Exempel som visar hur du använder **Passport-Azure-AD** för att verifiera åtkomsttoken för appar som registrerats med **Azure Active Directory B2C** (Azure AD B2C)

> [!div class="nextstepaction"]
> [Node.js webb-API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
