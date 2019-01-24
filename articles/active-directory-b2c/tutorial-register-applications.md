---
title: Självstudie – registrera dina program i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du registrerar dina program i Azure Active Directory B2C med Azure portal.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 99ad1bbaa732b1207ead9da8da36f345d4978241
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856034"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>Självstudier: Registrera ditt program i Azure Active Directory B2C

Innan din [program](active-directory-b2c-apps.md) kan interagera med Azure Active Directory (Azure AD) B2C, måste de vara registrerade i en klient som du hanterar. Den här självstudien visar hur du registrerar program med hjälp av Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Registrera ett webb-API
> * Registrera ett mobilt eller inbyggt program

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat dina egna [Azure AD B2C-klient](tutorial-create-tenant.md), skapa en nu. Du kan använda en befintlig klient.

## <a name="register-a-web-application"></a>Registrera ett webbprogram

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.

    ![Växla till prenumerationskatalogen](./media/tutorial-register-applications/switch-directories.png)

2. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
3. Välj **program**, och välj sedan **Lägg till**.

    ![Lägga till ett program](./media/tutorial-register-applications/add-application.png)

4. Ange ett namn för programmet. Till exempel *webapp1*.
5. För **ta med webbapp / webb-API** och **Tillåt implicit flöde**väljer **Ja**.
6. För **svars-URL**, ange en slutpunkt där Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ange den för att lyssna lokalt på `https://localhost:44316` om du inte ännu vet portnumret, kan du ange ett platshållarvärde och ändra den senare. I testsyfte kan du ange den till `https://jwt.ms`, som visar innehållet i en token för granskning. Den här självstudien väljer `https://jwt.ms`. 

    Svars-URL: en måste börja med schemat `https`, och alla svars-URL-värden måste dela en enda DNS-domän. Exempel: om programmet har en svars-URL för `https://login.contoso.com`, du kan lägga till data som den här URL: en `https://login.contoso.com/new`. Eller du kan referera till DNS-underdomänen av `login.contoso.com`, till exempel `https://new.login.contoso.com`. Om du vill ha ett program med `login-east.contoso.com` och `login-west.contoso.com` som svars-URL: er, måste du lägga till dessa-URL: Svarswebbadresser i den här ordningen: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Du kan lägga till två senare eftersom de är underdomäner i den första svars-URL `contoso.com`.

7. Klicka på **Skapa**.

    ![Ange egenskaper för programmet](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Om du är programmet utbyter en kod för en token, måste du skapa en programhemlighet.

1. Välj **nycklar** och klicka sedan på **skapa nycklar**.

    ![Generera nycklar](./media/tutorial-register-applications/generate-keys.png)

2. Välj **spara** att visa nyckeln. Anteckna **appnyckel**-värdet. Du använder värdet som programhemlighet i programkoden.

    ![Spara nyckeln](./media/tutorial-register-applications/save-key.png)
    
3. Välj **API-åtkomst**, klickar du på **Lägg till**, och välj webb-API och scope (behörigheter).

    ![Konfigurera API-åtkomst](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Registrera ett webb-API

1. Välj **program**, och välj sedan **Lägg till**.
3. Ange ett namn för programmet. Till exempel *webapi1*.
4. För **ta med webbapp / webb-API** och **Tillåt implicit flöde**väljer **Ja**.
5. För **svars-URL**, ange en slutpunkt där Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ange den för att lyssna lokalt på `https://localhost:44316`. Om du inte ännu vet portnumret, kan du ange ett platshållarvärde och ändra den senare.
6. För **Appidentitets-URI**, ange identifieraren som används för ditt webb-API. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
7. Klicka på **Skapa**.
8. Välj den *webapi1* program som du skapade och välj sedan **publicerade områden** att lägga till fler omfång efter behov. Som standard den `user_impersonation` omfång definieras. Den `user_impersonation` omfång ger andra program möjlighet att komma åt den här API: et för den inloggade användaren räkning. Om du vill kan den `user_impersonation` omfång kan tas bort.

    ![Ange publicerade områden](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>Registrera ett mobilt eller inbyggt program

1. Välj **program**, och välj sedan **Lägg till**.
2. Ange ett namn för programmet. Till exempel *nativeapp1*.
3. För **ta med webbapp / webb-API**väljer **nr**.
4. För **ta med intern klient**väljer **Ja**.
5. För **omdirigerings-URI**, ange en giltig omdirigerings-URI med ett anpassat schema. Det finns två viktiga överväganden när du väljer en omdirigerings-URI:

    - **Unikt** -schemat för omdirigerings-URI måste vara unikt för varje program. I det här exemplet `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` är schemat. Det här mönstret ska följas. Om två program delar samma schema, ges användaren kan välja ett program. Om användaren gör ett felaktigt val, misslyckas inloggningen.
    - **Fullständig** -omdirigerings-URI måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//contoso/` fungerar och `//contoso` misslyckas. Kontrollera att omdirigeringen-URI inte innehåller specialtecken, till exempel understreck.

6. Klicka på **Skapa**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Registrera ett webb-API
> * Registrera ett mobilt eller inbyggt program

> [!div class="nextstepaction"]
> [Skapa användarflöden i Azure Active Directory B2C](tutorial-create-user-flows.md)