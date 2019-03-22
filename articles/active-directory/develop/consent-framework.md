---
title: Ramverket för medgivande för Azure Active Directory
description: Läs mer om ramverket för medgivande i Azure Active Directory och hur det gör det lätt att utveckla webbprogram för flera innehavare och interna klientprogram.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: celested
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0200aa917894f4b7f1a45a9d3bd15b128aaf27a2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124192"
---
# <a name="azure-active-directory-consent-framework"></a>Ramverket för medgivande för Azure Active Directory

Ramverket för medgivande Azure Active Directory (AD Azure) gör det enkelt att utveckla webbprogram för flera klienter och interna klientprogram. Dessa program kan logga in genom att användarkonton från en Azure AD-klient som skiljer sig från den där programmet har registrerats. De kan också behöva åtkomst till webb-API: er som Microsoft Graph API (för att få åtkomst till Azure AD, Intune och tjänster i Office 365) och andra Microsoft-tjänster API: er, förutom ditt eget webb-API: er.

Ramverket är baserad på en användare eller administratör ge ditt medgivande till ett program som frågar registreras i sin katalog, som kan ge åtkomst till katalogdata. Om ett webb-klientprogram behöver läsa kalenderinformation om användaren från Office 365, är till exempel att användaren måste godkänna klientprogrammet först. När tillstånd ges kommer klientprogrammet att kunna anropa Microsoft Graph API för användarens räkning och använda kalenderinformationen vid behov. Den [Microsoft Graph API](https://developer.microsoft.com/graph) ger åtkomst till data i Office 365 (till exempel kalendrar och meddelanden från Exchange, platser och listor från SharePoint, dokument från OneDrive, från OneNote-anteckningsböcker, aktiviteter från Planner och arbetsböcker från Excel-), samt användare och grupper från Azure AD och andra dataobjekt från flera Microsoft-molntjänster.

Ramverket för medgivande bygger på OAuth 2.0 och dess olika flöden, t.ex. bevilja kod stipendium och klientens autentiseringsuppgifter, med hjälp av offentliga eller konfidentiella klienter. Med hjälp av OAuth 2.0, går Azure AD det att skapa många olika typer av program – till exempel på en telefon, surfplatta, server eller ett webbprogram – och få åtkomst till resurserna som krävs.

Mer information om hur du använder ramverket för medgivande med OAuth2.0 auktoriseringsbeviljanden finns i [bevilja åtkomst till webbprogram med hjälp av OAuth 2.0 och Azure AD](v1-protocols-oauth-code.md) och [autentiseringsscenarier för Azure AD](authentication-scenarios.md). Information om hur du får auktoriserad åtkomst till Office 365 via Microsoft Graph finns i [appautentisering med Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Samtycke upplevelsen - exempel

Följande steg visar hur samtycke upplevelse fungerar för både programutvecklare och användaren.

1. Anta att du har ett webbprogram för klienten som behöver begära särskild behörighet för att få åtkomst till en resurs/API. Du får lära dig hur du gör denna konfiguration i nästa avsnitt, men i stort sett Azure-portalen för att deklarera behörighetsbegäranden vid konfigurationen. De blir en del av programmets Azure AD-registrering som andra konfigurationsinställningar:

    ![Behörigheter för andra program](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)

1. Överväg att behörigheter för ditt program har uppdaterats, programmet körs och en användare kommer att använda den för första gången. Programmet måste först, att hämta en auktoriseringskod från Azure Active Directorys `/authorize` slutpunkt. Auktoriseringskoden kan sedan användas för att få en ny åtkomst och uppdatera token.

1. Om användaren inte är autentiserad och Azure Active Directorys `/authorize` endpoint uppmanar användaren att logga in.

    [Användaren eller administratören logga in på Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. När användaren har loggat in avgör Azure AD om användaren behöver en samtyckessida visas. Det här fastställandet baseras på om användaren (eller organisationens administratör) redan har gett medgivande för programmet. Om medgivande inte redan har getts, Azure AD efterfrågar medgivande och visar de behörigheter som krävs ska fungera. Uppsättningen behörigheter som visas i dialogrutan för medgivande överensstämmer med de som valts i den **delegerade behörigheter** i Azure-portalen.

    ![Användarupplevelsen för medgivande](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. När användaren ger ditt medgivande, returneras en auktoriseringskod till programmet, som har löst in att hämta en åtkomsttoken och uppdatera token. Mer information om det här flödet finns i [webb-API-apptyp](web-api.md).

1. Som administratör kan du även samtycka till ett programs delegerade behörigheter för samtliga användare i klienten. Administratörs godkännande förhindrar att godkännande i dialogrutan visas för varje användare i klienten och kan göras i den [Azure-portalen](https://portal.azure.com) av användare med administratörsrollen. Läs vilken administratör roller kan godkänna delegerade behörigheter i [behörigheter för administratör i Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Om du vill godkänna en app delegerade behörigheter**

   1. Gå till den **inställningar** sidan för ditt program
   1. Välj **behörigheter som krävs för**.
   1. Klicka på den **bevilja** knappen.

      ![Bevilja behörigheter för explicit administratörens godkännande](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)

   > [!IMPORTANT]
   > Bevilja uttryckliga medgivande med hjälp av den **bevilja** knappen krävs för närvarande för enkelsidigt program (SPA) som använder ADAL.js. Annars misslyckas programmet när åtkomsttoken begärs.

## <a name="next-steps"></a>Nästa steg

* Se [så här konverterar du en app för att vara flera innehavare](howto-convert-app-to-be-multi-tenant.md)
* Mer information lär du dig [hur medgivande stöds i OAuth 2.0-protokollet lagret under auktoriseringskodsflödet till bevilja.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
