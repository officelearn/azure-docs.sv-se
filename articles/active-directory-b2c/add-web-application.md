---
title: Lägga till ett webb-API-program - Azure Active Directory B2C | Microsoft-dokument
description: Lär dig hur du lägger till ett webb-API-program i Active Directory B2C-klienten.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e6dbf3d6fd5a43ab2d075c193c5bc589dc3566a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190185"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Lägga till ett webb-API-program i din Azure Active Directory B2C-klient

 Registrera webb-API-resurser i din klientorganisation så att de kan acceptera och svara på begäranden av klientprogram som presenterar en åtkomsttoken. Den här artikeln visar hur du registrerar ett webb-API i Azure Active Directory B2C (Azure AD B2C).

Om du vill registrera ett program i din Azure AD B2C-klient kan du använda den aktuella **programupplevelsen** eller vår nya upplevelse av enhetliga **appregistreringar (förhandsversion).** [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Program**och välj sedan **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I produktionsprogrammet kan du ange svars-URL:en till ett värde, `https://localhost:44332`till exempel . För testningsändamål anger du `https://jwt.ms`svars-URL:en till .
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du program-ID:t du kommer att använda när du konfigurerar webbappen.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapi1*.
1. Under **Omdirigera URI**väljer du **Webb**och anger sedan en slutpunkt där Azure AD B2C ska returnera alla token som ditt program begär. I ett produktionsprogram kan du ange omdirigerings-URI en slutpunkt som `https://localhost:5000`. Under utveckling eller testning kan `https://jwt.ms`du ställa in den till , ett Microsoft-ägt webbprogram som visar det avkodade innehållet i en token (innehållet i token lämnar aldrig din webbläsare). Du kan när som helst lägga till och ändra omdirigerings-URI:er i dina registrerade program.
1. Välj **Registrera**.
1. Registrera **program-ID:t (klient)** för användning i webb-API:s kod.

Om du har ett program som implementerar det implicita bidragsflödet, till exempel ett JavaScript-baserat ensidigt program (SPA), kan du aktivera flödet genom att följa dessa steg:

1. Välj **Autentisering**under **Hantera**.
1. Välj **Prova den nya upplevelsen** (om den visas).
1. Markera **kryssrutorna Access-token** och **ID-token** under **Implicit tilldelning.**
1. Välj **Spara**.

* * *

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. I den här självstudiekursen använder du omfång för att definiera läs- och skrivrättigheter för webb-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:t. I [självstudiekurs: Registrera ett program i Azure Active Directory B2C](tutorial-register-applications.md)registreras ett webbprogram med namnet *webapp1* i Azure AD B2C. Du kan använda det här programmet för att anropa webb-API:et.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Programmet har registrerats för att anropa det skyddade webb-API:et. En användare autentiserar med Azure AD B2C för att använda programmet. Programmet får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:et.
