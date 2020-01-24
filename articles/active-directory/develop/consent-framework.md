---
title: Azure AD medgivande Framework
titleSuffix: Microsoft identity platform
description: Lär dig mer om medgivande ramverket i Azure Active Directory och hur det gör det enkelt att utveckla webb-och interna klient program med flera innehavare.
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
ms.openlocfilehash: 3f95a0743ca6fadff0c7a26a796ef20659adfb80
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697752"
---
# <a name="azure-active-directory-consent-framework"></a>Ramverk för Azure Active Directory medgivande

Den Azure Active Directory (Azure AD) medgivande ramverket gör det enkelt att utveckla webb-och interna klient program med flera innehavare. Dessa program tillåter inloggning av användar konton från en Azure AD-klient som skiljer sig från den plats där programmet har registrerats. De kan också behöva åtkomst till webb-API: er som Microsoft Graph-API (för att få åtkomst till Azure AD, Intune och tjänster i Office 365) och andra API: er för Microsoft-tjänster, förutom dina egna webb-API: er.

Ramverket är baserat på en användare eller en administratör som ger tillåtelse till ett program som ber att registreras i deras katalog, vilket kan innebära åtkomst till katalog data. Om ett webb klient program till exempel behöver läsa kalender information om användaren från Office 365, måste användaren först godkänna klient programmet. Efter medgivande kan klient programmet anropa Microsoft Graph-API: et för användarens räkning och använda Kalender informationen efter behov. [Microsoft Graph API](https://developer.microsoft.com/graph) ger åtkomst till data i Office 365 (t. ex. kalendrar och meddelanden från Exchange, webbplatser och listor från SharePoint, dokument från OneDrive, antecknings böcker från OneNote, uppgifter från Planner och arbets böcker från Excel) samt användare och grupper från Azure AD och andra data objekt från fler Microsoft-molntjänster.

Godkännande ramverket bygger på OAuth 2,0 och de olika flödena, till exempel auktoriseringskod och beviljande av klientautentiseringsuppgifter, med hjälp av offentliga eller konfidentiella klienter. Med hjälp av OAuth 2,0 gör Azure AD det möjligt att bygga många olika typer av klient program, till exempel på en telefon, surfplatta, server eller ett webb program, och få till gång till de resurser som krävs.

Mer information om hur du använder medgivande ramverket med auktoriserings bidrag för OAuth 2.0 finns i [bevilja åtkomst till webb program med hjälp av oauth 2,0 och Azure AD](v1-protocols-oauth-code.md) och [autentiserings scenarier för Azure AD](authentication-scenarios.md). Information om hur du får behörighet för att få åtkomst till Office 365 via Microsoft Graph finns i [app-autentisering med Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Godkännande upplevelse – ett exempel

Följande steg visar hur medgivande upplevelsen fungerar för både programutvecklaren och användaren.

1. Anta att du har ett webb klient program som behöver begära specifika behörigheter för att få åtkomst till en resurs/API. Du lär dig hur du gör den här konfigurationen i nästa avsnitt, men i princip används Azure Portal för att deklarera behörighets begär Anden vid konfigurations tillfället. De blir en del av programmets Azure AD-registrering som andra konfigurationsinställningar:

    ![Behörigheter till andra program](./media/consent-framework/permissions.png)

1. Tänk på att programmets behörigheter har uppdaterats, att programmet körs och att en användare kommer att använda det för första gången. Först måste programmet hämta en auktoriseringskod från Azure ADs `/authorize`-slutpunkt. Auktoriseringskoden kan sedan användas för att få en ny åtkomst och uppdatera token.

1. Om användaren inte redan är autentiserad kommer Azure ADs `/authorize`-slutpunkt att begära att användaren loggar in.

    ![Användare eller administratör logga in på Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. När användaren har loggat in avgör Azure AD om användaren måste visas en godkännande sida. Det här fastställandet baseras på om användaren (eller organisationens administratör) redan har gett medgivande för programmet. Om medgivande inte redan har beviljats uppmanar Azure AD användaren att godkänna och visar de nödvändiga behörigheter som de behöver för att fungera. De behörigheter som visas i dialog rutan för medgivande matchar de som har marker ATS i de **delegerade behörigheterna** i Azure Portal.

    ![Visar ett exempel på behörigheter som visas i dialog rutan medgivande](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. När användaren har gett sitt medgivande returneras en auktoriseringskod till ditt program, som har lösts för att erhålla en åtkomsttoken och en uppdaterad token. Mer information om det här flödet finns i [webb-API-app-typ](web-api.md).

1. Som administratör kan du även samtycka till ett programs delegerade behörigheter för samtliga användare i klienten. Administrativt medgivande förhindrar att dialog rutan för medgivande visas för alla användare i klient organisationen och kan göras i [Azure Portal](https://portal.azure.com) av användare med rollen administratör. Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Godkänna till en Apps delegerade behörigheter**

   1. Gå till sidan **API-behörigheter** för ditt program
   1. Klicka på knappen **bevilja administratörs medgivande** .

      ![Bevilja behörighet för uttryckligt administratörs medgivande](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Att bevilja explicit medgivande med hjälp av knappen **bevilja behörigheter** krävs för närvarande för program med en enda sida (Spa) som använder ADAL. js. Annars misslyckas programmet när åtkomsttoken begärs.

## <a name="next-steps"></a>Nästa steg

* Se så [här konverterar du en app till flera innehavare](howto-convert-app-to-be-multi-tenant.md)
* För mer djupgående, lär du dig [hur medgivande stöds på protokoll skiktet OAuth 2,0 under auktoriseringskod-flödet.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
