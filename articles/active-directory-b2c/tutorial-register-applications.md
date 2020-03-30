---
title: 'Självstudiekurs: Registrera ett program'
titleSuffix: Azure AD B2C
description: Lär dig hur du registrerar ett webbprogram i Azure Active Directory B2C med Azure-portalen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183099"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Självstudiekurs: Registrera ett program i Azure Active Directory B2C

Innan dina [program](application-types.md) kan interagera med Azure Active Directory B2C (Azure AD B2C) måste de vara registrerade i en klient som du hanterar. Den här självstudien visar hur du registrerar ett webbprogram med Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klienthemlighet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du inte redan har skapat din egen [Azure AD B2C-klient skapar](tutorial-create-tenant.md)du en nu. Du kan använda en befintlig Azure AD B2C-klientorganisation.

## <a name="register-a-web-application"></a>Registrera ett webbprogram

Om du vill registrera ett program i din Azure AD B2C-klient kan du använda den aktuella **programupplevelsen** eller vår nya upplevelse av enhetliga **appregistreringar (förhandsversion).** [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Sök efter och välj **Azure AD B2C**i Azure-portalen .
1. Välj **Program**och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapp1*.
1. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ställa in `https://localhost:44316`den så att den lyssnar lokalt på . Om du ännu inte känner till portnumret kan du ange ett platshållarvärde och ändra det senare.

    För teständamål som den här självstudien kan du ställa in den `https://jwt.ms` som visar innehållet i en token för inspektion. För den här självstudien `https://jwt.ms`anger du **svars-URL:en** till .

    Följande begränsningar gäller för svarsadresser:

    * Svars-URL:en måste `https`börja med schemat .
    * Svars-URL:en är skiftlägeskänslig. Ärendet måste matcha fallet med URL-sökvägen för det program som körs. Om programmet till exempel ingår som `.../abc/response-oidc`en del `.../ABC/response-oidc` av sökvägen ska du inte ange i svars-URL:en. Eftersom webbläsaren behandlar sökvägar som skiftlägeskänsliga `.../abc/response-oidc` kan cookies som är associerade med uteslutas om de omdirigeras till webbadressen för ärendeomatchade. `.../ABC/response-oidc`

1. Välj **Skapa** för att slutföra programregistreringen.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Sök efter och välj **Azure AD B2C**i Azure-portalen .
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapp1*.
1. Välj **Konton i en organisationskatalog eller någon identitetsprovider**.
1. Under **Omdirigera URI**väljer du `https://jwt.ms` **Webb**och anger sedan i textrutan URL.

    Omdirigerings-URI är den slutpunkt som användaren skickas till av auktoriseringsservern (Azure AD B2C, i det här fallet) efter att ha slutfört sin interaktion med användaren och till vilken en åtkomsttoken eller auktoriseringskod skickas vid lyckad auktorisering. I ett produktionsprogram är det vanligtvis en allmänt tillgänglig slutpunkt `https://contoso.com/auth-response`där appen körs, till exempel . För teständamål som den här självstudien kan du ställa in den på `https://jwt.ms`, ett Microsoft-ägt webbprogram som visar det avkodade innehållet i en token (innehållet i token lämnar aldrig din webbläsare). Under apputvecklingen kan du lägga till slutpunkten där ditt `https://localhost:5000`program lyssnar lokalt, till exempel . Du kan när som helst lägga till och ändra omdirigerings-URI:er i dina registrerade program.

    Följande begränsningar gäller för omdirigerande URI:er:

    * Svars-URL:en måste `https`börja med schemat .
    * Svars-URL:en är skiftlägeskänslig. Ärendet måste matcha fallet med URL-sökvägen för det program som körs. Om programmet till exempel ingår som `.../abc/response-oidc`en del `.../ABC/response-oidc` av sökvägen ska du inte ange i svars-URL:en. Eftersom webbläsaren behandlar sökvägar som skiftlägeskänsliga `.../abc/response-oidc` kan cookies som är associerade med uteslutas om de omdirigeras till webbadressen för ärendeomatchade. `.../ABC/response-oidc`

1. Markera kryssrutan Bevilja *administratörsgodkännande för att öppna och offline_access behörigheter* under **Behörigheter.**
1. Välj **Registrera**.

När programregistreringen är klar aktiverar du det implicita bidragsflödet:

1. Välj **Autentisering**under **Hantera**.
1. Välj **Prova den nya upplevelsen** (om den visas).
1. Markera **kryssrutorna Access-token** och **ID-token** under **Implicit tilldelning.**
1. Välj **Spara**.

* * *

## <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Om ditt program byter en auktoriseringskod för en åtkomsttoken måste du skapa en programhemlighet.

#### <a name="applications"></a>[Program](#tab/applications/)

1. På sidan **Azure AD B2C - Applications** väljer du det program som du skapade, till exempel *webapp1*.
1. Välj **Nycklar** och välj sedan **Generera nyckel**.
1. Välj **Spara** om du vill visa nyckeln. Anteckna **appnyckel**-värdet. Du använder det här värdet som programhemlighet i programmets kod.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. På sidan **Azure AD B2C - Appregistreringar (Förhandsversion)** väljer du det program som du skapade, till exempel *webapp1*.
1. Under **Hantera**väljer du **Certifikat & hemligheter**.
1. Välj **Ny klienthemlighet**.
1. Ange en beskrivning av klienthemligheten i rutan **Beskrivning.** Till exempel *clientsecret1*.
1. Under **Upphör att gälla**väljer du en varaktighet som hemligheten är giltig för och väljer sedan Lägg **till**.
1. Spela in hemlighetens **värde**. Du använder det här värdet som programhemlighet i programmets kod.

* * *

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klienthemlighet

Lär dig sedan hur du skapar användarflöden så att användarna kan registrera sig, logga in och hantera sina profiler.

> [!div class="nextstepaction"]
> [Skapa användarflöden i Azure Active Directory B2C >](tutorial-create-user-flows.md)
