---
title: Lägg till ett webb program – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till ett webb program i Active Directory B2C-klienten.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 88ce3931d9f47b8c16251a45e54fa96b97f038e2
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71693276"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Lägga till ett webb-API-program till din Azure Active Directory B2C-klient

 Registrera webb-API-resurser i klient organisationen så att de kan acceptera och svara på begär Anden från klient program som presenterar en åtkomsttoken. Den här artikeln visar hur du registrerar program i Azure Active Directory B2C (Azure AD B2C).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I produktions programmet kan du ange svars-URL till ett värde som `https://localhost:44332`. I test syfte ställer du in svars- `https://jwt.ms`URL till.
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du program-ID:t du kommer att använda när du konfigurerar webbappen.

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. I den här självstudiekursen använder du omfång för att definiera läs- och skrivrättigheter för webb-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:t. Som exempel: I [Självstudie: Registrera ett program i Azure Active Directory B2C](tutorial-register-applications.md)skapas ett webb program i Azure AD B2C med namnet *webapp1*. Du kan använda det här programmet för att anropa webb-API: et.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Programmet har registrerats för att anropa det skyddade webb-API:et. En användare autentiserar med Azure AD B2C för att använda programmet. Programmet får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:et.
