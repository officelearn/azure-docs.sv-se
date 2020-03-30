---
title: Azure AD-ramverk för samtycke
titleSuffix: Microsoft identity platform
description: Lär dig mer om ramverket för medgivande i Azure Active Directory och hur det gör det enkelt att utveckla webb- och inbyggda klientprogram för flera innehavare.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: cb9441e6ce19094ff72e902cdeea151041ceb963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161158"
---
# <a name="azure-active-directory-consent-framework"></a>Azure Active Directory-medgivanderamverk

Azure Active Directory (Azure AD) medgivanderamverk gör det enkelt att utveckla webb- och inbyggda klientprogram med flera innehavare. Dessa program tillåter inloggning av användarkonton från en Azure AD-klient som skiljer sig från den där programmet är registrerat. De kan också behöva komma åt webb-API:er som Microsoft Graph API (för att komma åt Azure AD, Intune och tjänster i Office 365) och andra Microsoft-tjänsters API:er, utöver dina egna webb-API:er.

Ramverket är baserat på en användare eller en administratör som ger samtycke till ett program som ber att bli registrerad i sin katalog, vilket kan innebära åtkomst till katalogdata. Om ett webbklientprogram till exempel behöver läsa kalenderinformation om användaren från Office 365 måste användaren först godkänna klientprogrammet. När samtycke har getts kan klientprogrammet anropa Microsoft Graph API för användarens räkning och använda kalenderinformationen efter behov. [Microsoft Graph API](https://developer.microsoft.com/graph) ger åtkomst till data i Office 365 (till exempel kalendrar och meddelanden från Exchange, webbplatser och listor från SharePoint, dokument från OneDrive, anteckningsböcker från OneNote, uppgifter från Planner och arbetsböcker från Excel) samt användare och grupper från Azure AD och andra dataobjekt från fler Microsoft-molntjänster.

Medgivanderamverket bygger på OAuth 2.0 och dess olika flöden, till exempel auktoriseringskodsbidrag och klientautentiseringsbidrag, med hjälp av offentliga eller konfidentiella klienter. Genom att använda OAuth 2.0 gör Azure AD det möjligt att skapa många olika typer av klientprogram – till exempel på en telefon, surfplatta, server eller ett webbprogram – och få åtkomst till de resurser som krävs.

Mer information om hur du använder ramverket för medgivande med OAuth2.0-auktoriseringsbidrag finns i [Auktorisera åtkomst till webbprogram med OAuth 2.0 och Azure AD](v2-oauth2-auth-code-flow.md) och [autentiseringsscenarier för Azure AD](authentication-scenarios.md). Mer information om hur du får auktoriserad åtkomst till Office 365 via Microsoft Graph finns i [Appautentisering med Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Samtyckeserfarenhet - ett exempel

Följande steg visar hur samtyckesupplevelsen fungerar för både programutvecklaren och användaren.

1. Anta att du har ett webbklientprogram som måste begära specifika behörigheter för att komma åt en resurs/API. Du får lära dig hur du gör den här konfigurationen i nästa avsnitt, men i huvudsak Azure-portalen används för att deklarera behörighetsbegäranden vid konfigurationstid. De blir en del av programmets Azure AD-registrering som andra konfigurationsinställningar:

    ![Behörigheter till andra program](./media/consent-framework/permissions.png)

1. Tänk på att programmets behörigheter har uppdaterats, programmet körs och en användare är på väg att använda det för första gången. Först måste programmet hämta en auktoriseringskod från Azure AD:s `/authorize` slutpunkt. Auktoriseringskoden kan sedan användas för att få en ny åtkomst och uppdatera token.

1. Om användaren inte redan är autentiserat `/authorize` uppmanas användaren att logga in i Azure AD.

    ![Användare eller administratör loggar in på Azure AD](./media/consent-framework/usersignin.png)

1. När användaren har loggat in avgör Azure AD om användaren behöver visas på en medgivandesida. Det här fastställandet baseras på om användaren (eller organisationens administratör) redan har gett medgivande för programmet. Om medgivande inte redan har beviljats uppmanar Azure AD användaren om medgivande och visar de behörigheter som krävs för att fungera. Den uppsättning behörigheter som visas i dialogrutan medgivande matchar de som valts i de **delegerade behörigheterna** i Azure-portalen.

    ![Visar ett exempel på behörigheter som visas i dialogrutan medgivande](./media/consent-framework/consent.png)

1. När användaren har gett sitt samtycke returneras en auktoriseringskod till ditt program, som löses in för att hämta en åtkomsttoken och uppdateringstoken. Mer information om det här flödet finns i [OAuth 2.0 auktoriseringskodflöde](v2-oauth2-auth-code-flow.md).

1. Som administratör kan du även samtycka till ett programs delegerade behörigheter för samtliga användare i klienten. Administrativt medgivande förhindrar att dialogrutan samtycke visas för alla användare i klienten och kan göras i [Azure-portalen](https://portal.azure.com) av användare med administratörsrollen. Information om vilka administratörsroller som kan medgivande till delegerade behörigheter finns [i Administratörsrollbehörigheter i Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Så här samtycker du till en apps delegerade behörigheter**

   1. Gå till **api-behörighetssidan** för ditt program
   1. Klicka på knappen **Bevilja administratörssamgivande.**

      ![Bevilja behörigheter för uttryckligt administratörsgodkännande](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Det krävs för närvarande att ge uttryckligt medgivande med hjälp av knappen **Bevilja behörigheter** för ensidig användning (SPA) som använder ADAL.js. Annars misslyckas programmet när åtkomsttoken begärs.

## <a name="next-steps"></a>Nästa steg

* Se [hur du konverterar en app till att vara flera innehavare](howto-convert-app-to-be-multi-tenant.md)
* Mer djup kan du läsa [om hur medgivande stöds på OAuth 2.0-protokolllagret under tilldelningsflödet för auktoriseringskod.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
