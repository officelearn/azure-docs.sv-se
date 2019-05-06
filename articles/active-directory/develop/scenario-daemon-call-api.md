---
title: 'Daemon för app anropande webb API: er (anropande webb API: er) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar en daemon-app att anrop webb-API: er (anropande webb API: er)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076277"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon för appen att anrop webb-API: er – anropa ett webb-API från appen

En daemon-app kan anropa ett webb-API från en .NET-daemon-program eller anropa flera förhandsgodkända webb-API: er.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Anropa ett webb-API från en .NET-daemon-program

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Anropa flera API: er

Webb-API: er som du anropar måste godkännas före för daemon-appar. Det finns inte någon inkrementella medgivande med daemon-appar (det finns inga användaråtgärder). Administratör måste godkänna före programmet och API-behörigheter. Om du vill anropa flera API: er du behöver hämta en token för varje resurs, varje gång anropa `AcquireTokenForClient`. MSAL använder programmet tokens cacheminne för att undvika onödiga tjänstanrop.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon för app – flytta till produktion](./scenario-daemon-production.md)