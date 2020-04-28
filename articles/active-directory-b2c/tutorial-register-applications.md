---
title: 'Självstudie: registrera ett program'
titleSuffix: Azure AD B2C
description: Lär dig hur du registrerar ett webb program i Azure Active Directory B2C med hjälp av Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183099"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Självstudie: registrera ett program i Azure Active Directory B2C

Innan dina [program](application-types.md) kan interagera med Azure Active Directory B2C (Azure AD B2C) måste de registreras i en klient som du hanterar. Den här självstudien visar hur du registrerar ett webb program med hjälp av Azure Portal.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klient hemlighet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du inte redan har skapat din egen [Azure AD B2C-klient](tutorial-create-tenant.md)skapar du en nu. Du kan använda en befintlig Azure AD B2C klient.

## <a name="register-a-web-application"></a>Registrera ett webbprogram

Om du vill registrera ett program i din Azure AD B2C klient kan du använda den aktuella **program** upplevelsen eller vår nya enhetliga **Appregistreringar (förhands granskning)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **program**och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapp1*.
1. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ange att den ska lyssna lokalt på `https://localhost:44316`. Om du inte känner till port numret än kan du ange ett värde för plats hållare och ändra det senare.

    För testnings ändamål som den här själv studie kursen kan `https://jwt.ms` du ange det som visar innehållet i en token för att kontrol lera. I den här självstudien anger du **svars-URL** till `https://jwt.ms`.

    Följande begränsningar gäller för svars-URL: er:

    * Svars-URL: en måste börja `https`med schemat.
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av `.../abc/response-oidc`sökvägen ska du inte `.../ABC/response-oidc` ange i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, `.../abc/response-oidc` kan cookies som är kopplade till uteslutas om de omdirigeras till den Skift `.../ABC/response-oidc` läges fel matchnings bara URL: en.

1. Välj **skapa** för att slutföra program registreringen.

#### <a name="app-registrations-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **Appregistreringar (för hands version)** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapp1*.
1. Välj **konton i en organisations katalog eller en identitets leverantör**.
1. Under **omdirigerings-URI**väljer du **webb**och `https://jwt.ms` anger sedan i text rutan URL.

    Omdirigerings-URI: n är den slut punkt som användaren skickas till av auktoriseringsservern (Azure AD B2C i det här fallet) när dess interaktion med användaren har slutförts och till vilken en åtkomsttoken eller auktoriseringskod skickas vid lyckad auktorisering. I ett produktions program är det vanligt vis en offentligt tillgänglig slut punkt där appen körs, t. `https://contoso.com/auth-response`ex.. För testnings ändamål som den här självstudien kan du `https://jwt.ms`ställa in den till, ett Microsoft-ägda webb program som visar det avkodade innehållet i en token (innehållet i token aldrig lämnar webbläsaren). Under utveckling av appar kan du lägga till slut punkten där ditt program lyssnar lokalt, t `https://localhost:5000`. ex.. Du kan när som helst lägga till och ändra omdirigerings-URI: er i dina registrerade program.

    Följande begränsningar gäller för omdirigerings-URI: er:

    * Svars-URL: en måste börja `https`med schemat.
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av `.../abc/response-oidc`sökvägen ska du inte `.../ABC/response-oidc` ange i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, `.../abc/response-oidc` kan cookies som är kopplade till uteslutas om de omdirigeras till den Skift `.../ABC/response-oidc` läges fel matchnings bara URL: en.

1. Under **behörigheter**markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Registrera**.

När program registreringen är klar aktiverar du det implicita tilldelnings flödet:

1. Under **Hantera**väljer du **autentisering**.
1. Välj **testa den nya upplevelsen** (om den visas).
1. Under **implicit beviljande**väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.

* * *

## <a name="create-a-client-secret"></a>Skapa en klient hemlighet

Om programmet utbyter en auktoriseringskod för en åtkomsttoken måste du skapa en program hemlighet.

#### <a name="applications"></a>[Program](#tab/applications/)

1. På sidan **Azure AD B2C-program** väljer du det program som du skapade, till exempel *webapp1*.
1. Välj **nycklar** och välj sedan **generera nyckel**.
1. Välj **Spara** för att Visa nyckeln. Anteckna **appnyckel**-värdet. Du använder det här värdet som program hemlighet i programmets kod.

#### <a name="app-registrations-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

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
