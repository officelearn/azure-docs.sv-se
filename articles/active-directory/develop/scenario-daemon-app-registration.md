---
title: Registrera daemon-appar som anropar webb-API:er – Microsofts identitetsplattform | Azure
description: 'Lär dig hur du skapar en daemon app som anropar webb-API: er - app registrering'
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773366"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon app som anropar webb-API: er - app registrering

För en demon ansökan, här är vad du behöver veta när du registrerar appen.

## <a name="supported-account-types"></a>Kontotyper som stöds

Daemon-program är bara meningsfulla i Azure AD-klienter. Så när du skapar programmet måste du välja något av följande alternativ:

- **Konton i den här organisationskatalogen bara**. Det här valet är det vanligaste eftersom daemonprogram vanligtvis skrivs av LOB-utvecklare (line-of-business).
- **Konton i alla organisationskataloger**. Du gör det här valet om du är en ISV som tillhandahåller ett verktyg till dina kunder. Du behöver dina kunders klientadministratörer för att godkänna det.

## <a name="authentication---no-reply-uri-needed"></a>Autentisering - inget svar URI behövs

Om ditt konfidentiella klientprogram *endast* använder klientautentiseringsflödet behöver svars-URI:n inte registreras. Det behövs inte för programkonfigurationen eller konstruktionen. Klientautentiseringsflödet använder det inte.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-behörigheter – appbehörigheter och administratörsmedgivande

Ett daemon-program kan endast begära programbehörigheter till API:er (inte delegerade behörigheter). Välj **Programbehörigheter**på sidan **API-behörigheter** för programregistreringen, när du har valt **Lägg till en behörighet** och valt API-familjen, och välj sedan dina behörigheter.

![Appbehörigheter och administratörsmedgivande](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Webb-API:et som du vill anropa måste definiera *programbehörigheter (approller)*, inte delegerade behörigheter. Mer information om hur du exponerar ett sådant API finns i [Skyddat webb-API: Appregistrering - när ditt webb-API anropas av en daemonapp](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Daemon-program kräver att en klientadministratör samtycker till programmet som anropar webb-API:et. Klientadministratörer ger detta samtycke på samma **API-behörighetssida** genom att välja **Bevilja administratörsgodkännande till *vår organisation* **

Om du är en ISV bygga ett multitenant program, bör du läsa avsnittet [Distribution - fall av multitenant daemon apps](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon app - app kod konfiguration](./scenario-daemon-app-configuration.md)
