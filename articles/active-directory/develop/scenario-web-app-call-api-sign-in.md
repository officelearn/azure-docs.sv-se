---
title: Ta bort konton från token cache vid utloggning – Microsoft Identity Platform | Azure
description: Lär dig hur du tar bort ett konto från token-cache vid utloggning
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: be8208c7b2b67610f8d579b1bc0094bf09b3033e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026296"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>En webbapp som anropar webb-API: ta bort konton från token-cachen vid global utloggning

Du har lärt dig hur du lägger till inloggning till din webbapp i [en webbapp som loggar in användare: inloggning och utloggning](scenario-web-app-sign-user-sign-in.md).

Utloggning är annorlunda för en webbapp som anropar webb-API: er. När användaren loggar ut från ditt program, eller från ett program, måste du ta bort de token som är associerade med användaren från token-cachen.

## <a name="intercept-the-callback-after-single-sign-out"></a>Fånga upp återanropet efter enkel utloggning

För att ta bort den token-cachepost som är kopplad till det konto som loggade in kan programmet fånga efter- `logout` händelsen. Web Apps lagrar åtkomsttoken för varje användare i en token-cache. Genom att fånga efter `logout` återanrop kan ditt webb program ta bort användaren från cachen.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web tar hand om att implementera utloggningen åt dig. Mer information finns i [kod för Microsoft. identitet. webb adress](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET-exemplet tar inte bort konton från cachen vid global utloggning.

# <a name="java"></a>[Java](#tab/java)

Java-exemplet tar inte bort konton från cachen vid global utloggning.

# <a name="python"></a>[Python](#tab/python)

Python-exemplet tar inte bort konton från cachen vid global utloggning.

---

## <a name="next-steps"></a>Nästa steg

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Hämta en token för webb programmet](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
