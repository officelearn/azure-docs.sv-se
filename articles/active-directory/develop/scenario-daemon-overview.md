---
title: 'Skapa en demonapp som anropar webb-API: er - Microsoft identity platform | Azure'
description: 'Lär dig hur du skapar en daemon app som anropar webb-API: er'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df06c4c55941f4424d6b90d2846af17bf055b2e4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885471"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: Daemon program som anropar webb-API: er

Lär dig allt du behöver för att bygga en demon program som anropar webb-API: er.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Översikt

Ditt program kan hämta en token för att anropa ett webb-API för sig själv (inte för en användares räkning). Det här scenariot är användbart för demonprogram. Den använder standard OAuth 2.0 [klient autentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) bevilja.

![Daemon-appar](./media/scenario-daemon-app/daemon-app.svg)

Här är några exempel på användningsfall för daemonappar:

- Webbprogram som används för att etablera eller administrera användare eller utföra batchprocesser i en katalog
- Skrivbordsprogram (som Windows-tjänster i Windows eller demonprocesser på Linux) som utför batchjobb eller en operativsystemtjänst som körs i bakgrunden
- Webb-API:er som behöver manipulera kataloger, inte specifika användare

Det finns ett annat vanligt fall där icke-demon program använder klientuppgifter: även när de agerar på uppdrag av användare, de måste komma åt en webb-API eller en resurs under sin egen identitet av tekniska skäl. Ett exempel är åtkomst till hemligheter i Azure Key Vault eller en Azure SQL-databas för en cache.

Program som hämtar en token för sina egna identiteter:

- Är konfidentiella klientprogram. Dessa appar, med tanke på att de får åtkomst till resurser oberoende av användare, måste bevisa sin identitet. De är också ganska känsliga appar. De måste godkännas av Azure Active Directory (Azure AD) klientadministratörer.
- Har registrerat en hemlighet (programlösenord eller certifikat) med Azure AD. Den här hemligheten skickas in under anropet till Azure AD för att hämta en token.

## <a name="specifics"></a>Detaljerna

> [!IMPORTANT]
>
> - Användare kan inte interagera med ett daemonprogram. En demon ansökan kräver sin egen identitet. Den här typen av program begär en åtkomsttoken med hjälp av dess programidentitet och presentera dess program-ID, autentiseringsuppgifter (lösenord eller certifikat) och program-ID URI till Azure AD. Efter lyckad autentisering får demonen en åtkomsttoken (och en uppdateringstoken) från slutpunkten för Microsoft identity-plattformen. Den här token används sedan för att anropa webb-API:et (och uppdateras efter behov).
> - Eftersom användare inte kan interagera med daemonprogram är inkrementellt medgivande inte möjligt. Alla nödvändiga API-behörigheter måste konfigureras vid programregistrering. Koden för programmet begär bara statiskt definierade behörigheter. Detta innebär också att daemon-program inte stöder inkrementellt samtycke.

För utvecklare har slutupplevelsen för det här scenariot följande aspekter:

- Daemon-program kan bara fungera i Azure AD-klienter. Det skulle inte vara meningsfullt att bygga en demon program som försöker manipulera Microsoft personliga konton. Om du är en LOB-apputvecklare (line-of-business) skapar du appen Daemon i din klientorganisation. Om du är en ISV, kanske du vill skapa en multitenant demon program. Varje klientadministratör måste ge sitt samtycke.
- Under [programregistreringen](./scenario-daemon-app-registration.md)behövs inte svars-URI. Du måste dela hemligheter eller certifikat eller signerade påståenden med Azure AD. Du måste också begära programbehörigheter och ge administratören tillstånd att använda dessa appbehörigheter.
- [Programkonfigurationen](./scenario-daemon-app-configuration.md) måste ange klientautentiseringsuppgifter som delade med Azure AD under programregistreringen.
- [Scopet](scenario-daemon-acquire-token.md#scopes-to-request) som används för att hämta en token med klientautentiseringsflödet måste vara ett statiskt scope.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon app - app registrering](./scenario-daemon-app-registration.md)
