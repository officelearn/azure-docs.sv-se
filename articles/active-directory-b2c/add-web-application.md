---
title: Lägg till ett webb-API-program – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till ett webb-API-program till din Active Directory B2C-klient.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190185"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Lägga till ett webb-API-program till din Azure Active Directory B2C-klient

 Registrera webb-API-resurser i klient organisationen så att de kan acceptera och svara på begär Anden från klient program som presenterar en åtkomsttoken. Den här artikeln visar hur du registrerar ett webb-API i Azure Active Directory B2C (Azure AD B2C).

Om du vill registrera ett program i din Azure AD B2C klient kan du använda den aktuella **program** upplevelsen eller vår nya enhetliga **Appregistreringar (förhands granskning)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I produktions programmet kan du ange svars-URL till ett värde som `https://localhost:44332`. I test syfte anger du svars-URL till `https://jwt.ms`.
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du det program-ID som du kommer att använda när du konfigurerar webbappen.

#### <a name="app-registrations-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar (för hands version)** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapi1*.
1. Under **omdirigerings-URI**väljer du **webb**och anger sedan en slut punkt där Azure AD B2C ska returnera de token som programmet begär. I ett produktions program kan du ange omdirigerings-URI: n till en slut punkt som `https://localhost:5000`. Under utveckling eller testning kan du ange att den ska `https://jwt.ms`, ett Microsoft-ägda webb program som visar det avkodade innehållet i en token (innehållet i token aldrig lämnar webbläsaren). Du kan när som helst lägga till och ändra omdirigerings-URI: er i dina registrerade program.
1. Välj **Registrera**.
1. Registrera **program-ID: t (Client)** som ska användas i din webb-API-kod.

Om du har ett program som implementerar det implicita tilldelnings flödet, till exempel ett JavaScript-baserat program med en enda sida (SPA), kan du aktivera flödet genom att följa dessa steg:

1. Under **Hantera**väljer du **autentisering**.
1. Välj **testa den nya upplevelsen** (om den visas).
1. Under **implicit beviljande**väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.

* * *

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. I den här självstudiekursen använder du omfång för att definiera läs- och skrivrättigheter för webb-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:et. I [Självstudier: registrera ett program i Azure Active Directory B2C](tutorial-register-applications.md)registreras till exempel ett webb program med namnet *webapp1* i Azure AD B2C. Du kan använda det här programmet för att anropa webb-API: et.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Programmet har registrerats för att anropa det skyddade webb-API:et. En användare autentiserar med Azure AD B2C för att använda programmet. Programmet får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:et.
