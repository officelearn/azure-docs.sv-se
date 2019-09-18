---
title: 'Daemon-app som anropar webb-API: er (app-registrering) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er – app-registrering'
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f590010a655fb01529a4a59b5540cc03068f2b8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056480"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon-app som anropar webb-API: er – app-registrering

För ett daemon-program måste du veta vad du behöver veta när du registrerar appen.

## <a name="supported-account-types"></a>Kontotyper som stöds

Med tanke på att daemon-program bara är begripliga i Azure AD-klienten, måste du välja följande när du skapar programmet:

- båda **kontona i den här organisations katalogen**. Det här alternativet är det vanligaste fallet eftersom daemon-program vanligt vis skrivs av verksamhetsspecifika utvecklare.
- eller **konton i en organisations katalog**. Du väljer själv om du är en ISV som tillhandahåller ett verktygs verktyg för dina kunder. Du behöver kundens klient organisations administratörer för att godkänna den.

## <a name="authentication---no-reply-uri-needed"></a>Autentisering – ingen svars-URI behövs

Om ditt konfidentiella klient program **bara** använder flödet för klientautentiseringsuppgifter, behöver svars-URL: en inte registreras. Det behövs varken för program konfigurationen/konstruktionen. Flödet för klientens autentiseringsuppgifter använder det inte.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-behörigheter – app-behörigheter och administratörs medgivande

Ett daemon-program kan bara begära program behörigheter till API: er (inte delegerade behörigheter). På sidan **API-behörighet** för program registreringen när du har valt **Lägg till en behörighet** och valt API-serien väljer du **program behörigheter**och väljer sedan behörigheter

![App-behörigheter och administratörs medgivande](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Daemon-program kräver ett godkännande av en innehavaradministratör för programmet som anropar webb-API: et. Detta medgivande finns på sidan samma **API-behörighet** , av en innehavaradministratör som väljer **bevilja administratörs medgivande till *vår organisation***

Om du är en ISV-byggnad av ett program för flera innehavare vill du kontrol lera om det finns ett stycke med [distribution av daemon-appar för flera innehavare](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) .

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon App – app Code Configuration](./scenario-daemon-app-configuration.md)
