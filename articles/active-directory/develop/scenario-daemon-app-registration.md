---
title: 'Daemon för app anropande webb API: er (appregistrering) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er – appregistrering'
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
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076247"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon för appen att anrop webb-API: er – appregistrering

För en daemonprogram kan du här är vad du behöver veta när du registrerar appen.

## <a name="supported-account-types"></a>Kontotyper som stöds

Med hänsyn till att daemon program bara meningsfullt i Azure AD-klient när du skapar programmet måste du välja:

- antingen **konton i den här organisationens katalogen**. Det här alternativet är i de flesta fall, eftersom daemon-program skrivs vanligtvis av line-of-business (LOB)-utvecklare.
- eller **konton i alla organisationskatalog**. Om du är en oberoende Programvaruleverantör som tillhandahåller ett verktyg för verktyget till dina kunder ska du göra det här alternativet. Du måste kundens klienter administratörer att godkänna den.

## <a name="authentication---no-reply-uri-needed"></a>Autentisering - inga svars-URI krävs

I de fall där konfidentiell klientprogrammet använder **endast** flow med klientens autentiseringsuppgifter, svars-URL behöver inte registreras. Det behövs inte för programmet configuration/konstruktion. Flödet använda inte den.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Behörigheter för API - app-behörigheter och administratörens godkännande

En daemon-program kan bara begära programbehörigheter till API: er (inte delegerade behörigheter). I den **API behörighet** för programregistrering när du har valt **lägga till en behörighet** och valt i API-familjen, Välj **programbehörigheter**, Välj sedan dina behörigheter

![App-behörigheter och administratörens godkännande](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Daemon för program kräver har en klientadministratör förväg godkänna programmet anropa webb-API. Den här medgivande finns i samma **API behörighet** sidan av en administratör att klienten välja **bevilja administratörens godkännande till *vår organisation***

Om du är en oberoende Programvaruleverantör att skapa ett program med flera innehavare, skulle du vill kontrollera den [distribution – fallet med flera innehavare daemon-appar](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) punkt.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon för app - konfiguration av kod](./scenario-daemon-app-configuration.md)
