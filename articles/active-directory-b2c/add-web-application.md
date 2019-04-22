---
title: Add a web application - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till ett webbprogram till din Active Directory B2C-klient.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: c1c640fa2c03f584aa9be62ae6ad123377ef53cd
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680765"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Lägg till ett webb-API-program till din Azure Active Directory B2C-klient

 Registrera webb-API-resurser i din klient så att de kan godkänna och svara på förfrågningar av klientprogram som presenterar en åtkomst-token. Den här artikeln visar hur du registrerar program i Azure Active Directory (Azure AD) B2C.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I ditt produktionsprogram, ställer du in svars-URL till ett värde som `https://localhost:44332`. I testsyfte kan du ange svars-URL `https://jwt.ms`.
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du program-ID:t du kommer att använda när du konfigurerar webbappen.

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. I den här självstudiekursen använder du omfång för att definiera läs- och skrivrättigheter för webb-API.

1. Välj **Program** och därefter *webapi1*.
2. Välj **Publicerade omfång**.
3. Som **omfång** anger du `Read` och som beskrivning anger du `Read access to the application`.
4. Som **omfång** anger du `Write` och som beskrivning anger du `Write access to the application`.
5. Klicka på **Spara**.

De publicerade omfången kan användas för att ge ett klientprogram behörighet till webb-API:et.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:t. Som exempel: I [Självstudie: Registrera ett program i Azure Active Directory B2C](tutorial-register-applications.md), ett webbprogram har skapats i Azure AD B2C med namnet *webapp1*. Du kan använda det här programmet för att anropa webb-API.

1. Välj **program**, och välj sedan ditt webbprogram.
2. Välj **API-åtkomst** och sedan **Lägg till**.
3. I listrutan **Välj API** väljer du *webapi1*.
4. I den **Välj områden** listrutan väljer du den **Läs** och **skriva** omfång som du tidigare har definierat.
5. Klicka på **OK**.

Programmet har registrerats för att anropa det skyddade webb-API:et. En användare autentiserar med Azure AD B2C för att använda programmet. Programmet får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:et.
