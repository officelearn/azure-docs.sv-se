---
title: Självstudie – registrera ett program – Azure Active Directory B2C
description: Lär dig hur du registrerar ett webb program i Azure Active Directory B2C med hjälp av Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 651c15c8206f7956bb35520f9c5837cb0c9308f9
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980693"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Självstudier: Registrera ett program i Azure Active Directory B2C

Innan dina [program](active-directory-b2c-apps.md) kan interagera med Azure Active Directory-B2C (Azure AD) måste de registreras i en klient som du hanterar. Den här självstudien visar hur du registrerar ett webb program med hjälp av Azure Portal.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klient hemlighet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat din egen [Azure AD B2C-klient](tutorial-create-tenant.md)skapar du en nu. Du kan använda en befintlig Azure AD B2C klient.

## <a name="register-a-web-application"></a>Registrera ett webbprogram

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapp1*.
1. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ange att den ska lyssna lokalt på `https://localhost:44316`. Om du inte känner till port numret än kan du ange ett värde för plats hållare och ändra det senare.

    För testnings ändamål som den här själv studie kursen kan `https://jwt.ms` du ange det som visar innehållet i en token för att kontrol lera. I den här självstudien anger du svars- **URL** till `https://jwt.ms`.

    Följande begränsningar gäller för svars-URL: er:

    * Svars-URL: en måste börja `https`med schemat.
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av `.../abc/response-oidc`sökvägen ska du inte `.../ABC/response-oidc` ange i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, `.../abc/response-oidc` kan cookies som är kopplade till uteslutas om de omdirigeras till den Skift läges fel `.../ABC/response-oidc` matchnings bara URL: en.

1. Slutför program registreringen genom att klicka på **skapa** .

## <a name="create-a-client-secret"></a>Skapa en klient hemlighet

Om programmet utbyter en kod för en token måste du skapa en program hemlighet.

1. På sidan **Azure AD B2C-program** väljer du det program som du skapade, till exempel *webapp1*.
1. Välj **nycklar** och välj sedan **generera nyckel**.
1. Välj **Spara** för att Visa nyckeln. Anteckna **appnyckel**-värdet. Du använder det här värdet som program hemlighet i programmets kod.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klient hemlighet

Nu ska du lära dig hur du skapar användar flöden för att göra det möjligt för användarna att registrera sig, logga in och hantera sina profiler.

> [!div class="nextstepaction"]
> [Skapa användar flöden i Azure Active Directory B2C >](tutorial-create-user-flows.md)
