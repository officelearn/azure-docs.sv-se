---
title: Add a web application - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till ett webbprogram till din Active Directory B2C-klient.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: c20f455a0a325dadd3eeeb77dea7026de4834c56
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757702"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Lägg till ett webb-API-program till din Azure Active Directory B2C-klient

Webb-API-resurserna måste vara registrerade i klientorganisationen innan de kan godkänna och svara på begäranden om skyddad resurs av klientprogram som presenterar en åtkomsttoken.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. Välj **program**, och välj sedan **Lägg till**.
5. Ange ett namn för programmet. Till exempel *webapi1*.
6. För **ta med webbapp / webb-API** och **Tillåt implicit flöde**väljer **Ja**.
7. För **svars-URL**, ange en slutpunkt där Azure AD B2C ska returnera de token som programmet begär. I de här självstudierna exemplet körs lokalt och lyssnar på `https://localhost:44332`.
8. För **Appidentitets-URI**, ange identifieraren som används för ditt webb-API. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. Anteckna program-ID som du använder när du konfigurerar webbprogrammet på egenskapssidan.

## <a name="configure-scopes"></a>Konfigurera omfång

Scope är ett sätt att styra åtkomst till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. I den här självstudiekursen använder du omfång för att definiera läs- och skrivrättigheter för webb-API.

1. Välj **program**, och välj sedan *webapi1*.
2. Välj **publicerade områden**.
3. För **omfång**, ange `Hello.Read`, och beskrivning, ange `Read access to hello`.
4. För **omfång**, ange `Hello.Write`, och beskrivning, ange `Write access to hello`.
5. Klicka på **Spara**.

De publicerade omfången kan användas för att ge en klient program behörighet till webb-API.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program, måste du bevilja program-API: et. I den obligatoriska självstudiekursen skapade du ett webbprogram i Azure AD B2C med namnet *webapp1*. Du kan använda det här programmet för att anropa webb-API.

1. Välj **program**, och välj sedan ditt webbprogram.
2. Välj **API-åtkomst**, och välj sedan **Lägg till**.
3. I den **Välj API** listrutan Välj *webapi1*.
4. I den **Välj områden** listrutan väljer du den **Hello.Read** och **Hello.Write** omfång som du tidigare har definierat.
5. Klicka på **OK**.

Programmet har registrerats för att anropa det skyddade webb-API. En användare autentiseras med Azure AD B2C för att använda programmet. Programmet hämtar ett auktoriseringsbeviljande från Azure AD B2C för att få åtkomst till skyddat webb-API.