---
title: 'Daemon för app anropande webb API: er (översikt) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er'
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
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075887"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: Daemon-program att anrop webb-API: er

Lär dig allt du behöver för att skapa en daemon-program som anropar webb-API: er.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Översikt

Programmet kan hämta en token för att anropa ett webb-API för själva (inte för en användare). Det här scenariot är användbart för daemon-program. Den använder standard OAuth 2.0 [klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) bevilja.

![Daemon-appar](./media/scenario-daemon-app/daemon-app.svg)

Här följer några exempel på användningsområden för daemon-appar:

- Webbprogram som används för att etablera eller administrera användare eller batchprocesser i en katalog
- Program, till exempel (windows-tjänster på Windows) eller daemon processer på Linux som utför batch-jobb eller en tjänst i operativsystemet som körs i bakgrunden
- Web API: er som behöver ändra kataloger, inte specifika användare

Det finns ett annat vanligt fall där icke-daemon program använder klientautentiseringsuppgifter: även om de agera åt användare de behöver för att komma åt ett webb-API eller en resurs med sin identitet för tekniska skäl. Ett exempel är åtkomst till hemligheter i KeyVault eller en Azure SQL-databas för cache.

Program som hämta en token för sina egna identiteter:

- är konfidentiell klientprogram. De här apparna måste tanke på att de har åtkomst till resurser oberoende av en användare bekräfta sin identitet. De är i stället känsliga appar som de behöver godkännas av innehavaradministratörer Azure Active Directory (AD Azure).
- Har registrerat en hemlighet (programlösenord eller certifikat) med Azure AD. Den här hemligheten är skickades i under anropet till Azure AD för att hämta en token.

## <a name="specifics"></a>Närmare information

> [!IMPORTANT]
>
> - Interaktion från användaren är inte möjligt med en daemon-program. En daemon-programmet kräver sin egen identitet. Den här typen av program begär en åtkomsttoken genom att använda dess Programidentitet och presentera dess program-ID, autentiseringsuppgifter (lösenord eller certifikat) och program till Azure AD-ID-URI. Efter en lyckad autentisering får daemon en åtkomsttoken (och en uppdateringstoken) från Microsoft identity-plattformen slutpunkten, som sedan används för att anropa webb-API (och uppdateras efter behov).
> - Inkrementell medgivande går inte eftersom användaren inte är möjligt. Alla API-behörigheter som krävs måste konfigureras på programregistrering och koden i programmet begär bara statiska definierade behörigheter. Det innebär också att daemon program inte stöder inkrementella medgivande.

För utvecklare har slutpunkt till slutpunkt-upplevelsen för det här scenariot följande aspekter:

- Daemon för program fungerar bara i Azure AD-klienter. Det skulle vara klokt att skapa en daemon-program som försöker ändra personliga Microsoft-konton. Om du är en apputvecklare för line-of-business (LOB), ska du skapa din daemon-app i din klient. Om du är en oberoende Programvaruleverantör, kanske du vill skapa ett daemonprogram för flera innehavare. Det måste vara godkänt genom att varje administratör.
- Under den [programregistrering](./scenario-daemon-app-registration.md), **svars-URI** behövs inte. Du behöver dela hemligheter eller certifikat med Azure AD och du måste begära behörighet för program och bevilja administratörens godkännande att använda dessa behörigheter.
- Den [programkonfiguration](./scenario-daemon-app-configuration.md) behövs för att tillhandahålla klientautentiseringsuppgifter som delad med Azure AD vid registrering för programmet.
- Den [omfång](scenario-daemon-acquire-token.md#scopes-to-request) används för att hämta en token med klientautentiseringsuppgifter flow måste vara ett statiskt omfång.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon för app - appregistrering](./scenario-daemon-app-registration.md)
