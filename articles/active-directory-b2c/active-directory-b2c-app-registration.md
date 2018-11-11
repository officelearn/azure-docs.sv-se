---
title: Registrera ett program i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om att registrera ditt program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8068c4a8f38cd33a1a0547f5db5079bc75c76ec1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013419"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Registrera ett program i Azure Active Directory B2C

Skapa en [program](active-directory-b2c-apps.md) som accepterar konsument registrering och inloggning, måste du först registrera programmet med en Azure AD B2C-klient. Den här artikeln hjälper dig att registrera ett program i en Azure Active Directory (Azure AD) B2C-klient på några få minuter. När du är färdig är programmet klart att användas i Azure AD B2C-klientorganisationen.

## <a name="prerequisites"></a>Förutsättningar

Skaffa en egen klient med hjälp av stegen i [skapa en Azure Active Directory B2C-klient](tutorial-create-tenant.md).

Välj nästa steg baserat på din programtyp:

- [Registrera ett webbprogram](#register-a-web-application)
- [Registrera ett webb-API](#register-a-web-api)
- [Registrera ett mobilt eller inbyggt program](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Registrera ett webbprogram

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
2. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
3. Välj **program**, och välj sedan **Lägg till**.
4. Ange ett namn för programmet. Till exempel *testapp1*.
5. För **ta med webbapp / webb-API** och **Tillåt implicit flöde**väljer **Ja**.
6. För **svars-URL**, ange slutpunkt där Azure AD B2C ska returnera de token som appen begär. Du kan till exempel ange den för att lyssna lokalt på `https://localhost:44316`. Om du inte ännu vet portnumret, kan du ange ett platshållarvärde och ändra den senare.
7. Klicka på **Skapa**.

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Om programmet anropar ett webb-API som skyddas av Azure AD B2C, måste du skapa en programhemlighet.

1. Välj **nycklar** och klicka sedan på **skapa nycklar**. 
2. Välj **spara** att visa nyckeln. Anteckna **appnyckel**-värdet. Du använder värdet som programhemlighet i programkoden.
3. Välj **API-åtkomst**, klickar du på **Lägg till**, och välj webb-API och scope (behörigheter).

## <a name="register-a-web-api"></a>Registrera ett webb-API

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
2. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
3. Välj **program**, och välj sedan **Lägg till**.
4. Ange ett namn för programmet. Till exempel *testapp2*.
5. För **ta med webbapp / webb-API** och **Tillåt implicit flöde**väljer **Ja**.
6. För **svars-URL**, ange slutpunkt där Azure AD B2C ska returnera de token som appen begär. Du kan till exempel ange den för att lyssna lokalt på `https://localhost:44316`. Om du inte ännu vet portnumret, kan du ange ett platshållarvärde och ändra den senare.
7. För **Appidentitets-URI**, ange identifieraren som används för ditt webb-API. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
8. Klicka på **Skapa**.
9. Välj **publicerade områden** att lägga till fler omfång efter behov. Som standard den `user_impersonation` omfång definieras. Den `user_impersonation` omfång ger andra program möjlighet att komma åt den här API: et för den inloggade användaren räkning. Om du vill kan den `user_impersonation` omfång kan tas bort.

## <a name="register-a-mobile-or-native-application"></a>Registrera ett mobilt eller inbyggt program

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
2. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
3. Välj **program**, och välj sedan **Lägg till**.
4. Ange ett namn för programmet. Till exempel *testapp3*.
5. För **ta med webbapp / webb-API**väljer **nr**.
6. För **ta med intern klient**väljer **Ja**.
7. För **omdirigerings-URI**, ange en [omdirigerings-URI med ett anpassat schema](active-directory-b2c-apps.md). Kontrollera att du väljer en bra omdirigerings-URI och inte innehåller specialtecken, till exempel understreck.
8. Klicka på **Skapa**.

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Om programmet anropar ett webb-API som skyddas av Azure AD B2C, måste du skapa en programhemlighet.

1. Välj **nycklar** och klicka sedan på **skapa nycklar**. 
2. Välj **spara** att visa nyckeln. Anteckna **appnyckel**-värdet. Du använder värdet som programhemlighet i programkoden.
3. Välj **API-åtkomst**, klickar du på **Lägg till**, och välj webb-API och scope (behörigheter).

## <a name="next-steps"></a>Nästa steg

Nu när du har registrerat ett program med Azure AD B2C kan du göra [en av snabbstartsguiderna](active-directory-b2c-overview.md) för att komma igång.

> [!div class="nextstepaction"]
> [Skapa en ASP.NET-webbapp med registrering, inloggning och lösenordsåterställning](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
