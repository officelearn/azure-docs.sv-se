---
title: 'Självstudie: registrera ett program'
titleSuffix: Azure AD B2C
description: Lär dig hur du registrerar ett webb program i Azure Active Directory B2C med hjälp av Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76d38e4b35111cd2c09685653fb4948f98387132
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950790"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Självstudie: registrera ett program i Azure Active Directory B2C

Innan dina [program](active-directory-b2c-apps.md) kan interagera med Azure Active Directory B2C (Azure AD B2C) måste de registreras i en klient som du hanterar. Den här självstudien visar hur du registrerar ett webb program med hjälp av Azure Portal.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klient hemlighet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du inte redan har skapat din egen [Azure AD B2C-klient](tutorial-create-tenant.md)skapar du en nu. Du kan använda en befintlig Azure AD B2C klient.

## <a name="register-a-web-application"></a>Registrera ett webbprogram

Om du vill registrera ett program i din Azure AD B2C klient kan du använda den aktuella **program** upplevelsen eller vår nya enhetliga **Appregistreringar (förhands granskning)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapp1*.
1. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ange att den ska lyssna lokalt på `https://localhost:44316`. Om du inte känner till port numret än kan du ange ett värde för plats hållare och ändra det senare.

    För testnings ändamål som den här själv studie kursen kan du ställa in den till `https://jwt.ms` som visar innehållet i en token för granskning. I den här självstudien anger du **svars-URL** till `https://jwt.ms`.

    Följande begränsningar gäller för svars-URL: er:

    * Svars-URL: en måste börja med schemat `https`.
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program till exempel innehåller som en del av sökvägen `.../abc/response-oidc`ska du inte ange `.../ABC/response-oidc` i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga kan cookies som är kopplade till `.../abc/response-oidc` uteslutas om de omdirigeras till den Skift läges fel matchning `.../ABC/response-oidc` URL.

1. Slutför program registreringen genom att klicka på **skapa** .

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar (för hands version)** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapp1*.
1. Välj **konton i en organisations katalog eller en identitets leverantör**.
1. Under **omdirigerings-URI**väljer du **webb**och anger sedan `https://jwt.ms` i text rutan URL.

    Omdirigerings-URI: n är den slut punkt som användaren skickas till av auktoriseringsservern (Azure AD B2C i det här fallet) när dess interaktion med användaren har slutförts och till vilken en åtkomsttoken eller auktoriseringskod skickas vid lyckad auktorisering. I ett produktions program är det vanligt vis en offentligt tillgänglig slut punkt där appen körs, t. ex. `https://contoso.com/auth-response`. I test syfte som i den här självstudien kan du ställa in det på `https://jwt.ms`, ett Microsoft-ägda webb program som visar det avkodade innehållet i en token (innehållet i token aldrig lämnar webbläsaren). Under utveckling av appar kan du lägga till slut punkten där ditt program lyssnar lokalt, som `https://localhost:5000`. Du kan när som helst lägga till och ändra omdirigerings-URI: er i dina registrerade program.

    Följande begränsningar gäller för omdirigerings-URI: er:

    * Svars-URL: en måste börja med schemat `https`.
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program till exempel innehåller som en del av sökvägen `.../abc/response-oidc`ska du inte ange `.../ABC/response-oidc` i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga kan cookies som är kopplade till `.../abc/response-oidc` uteslutas om de omdirigeras till den Skift läges fel matchning `.../ABC/response-oidc` URL.

1. Under **behörigheter**markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Registrera**.

När program registreringen är klar aktiverar du det implicita tilldelnings flödet:

1. Under **Hantera**väljer du **autentisering**.
1. Välj **testa den nya upplevelsen** (om den visas).
1. Under **implicit beviljande**väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.

* * *

## <a name="create-a-client-secret"></a>Skapa en klient hemlighet

Om programmet utbyter en kod för en token måste du skapa en program hemlighet.

#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

1. På sidan **Azure AD B2C-program** väljer du det program som du skapade, till exempel *webapp1*.
1. Välj **nycklar** och välj sedan **generera nyckel**.
1. Välj **Spara** för att Visa nyckeln. Anteckna **appnyckel**-värdet. Du använder det här värdet som program hemlighet i programmets kod.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. På sidan **Azure AD B2C-Appregistreringar (förhands granskning)** väljer du det program som du skapade, till exempel *webapp1*.
1. Under **Hantera**väljer du **certifikat & hemligheter**.
1. Välj **Ny klienthemlighet**.
1. Ange en beskrivning av klient hemligheten i rutan **Beskrivning** . Till exempel *clientsecret1*.
1. Under **upphör ande**väljer du en varaktighet för vilken hemligheten är giltig och väljer sedan **Lägg till**.
1. Registrera hemlighetens **värde**. Du använder det här värdet som program hemlighet i programmets kod.

* * *

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klient hemlighet

Nu ska du lära dig hur du skapar användar flöden för att göra det möjligt för användarna att registrera sig, logga in och hantera sina profiler.

> [!div class="nextstepaction"]
> [Skapa användar flöden i Azure Active Directory B2C >](tutorial-create-user-flows.md)
