---
title: Självstudie – registrera ett program – Azure Active Directory B2C
description: Lär dig mer om att registrera ett webbprogram i Azure Active Directory B2C med Azure portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5c46d3153bdc5768836bce198af115f82e8469f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056293"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Självstudier: Registrera ett program i Azure Active Directory B2C

Innan din [program](active-directory-b2c-apps.md) kan interagera med Azure Active Directory (Azure AD) B2C, måste de vara registrerade i en klient som du hanterar. Den här självstudien visar hur du registrerar ett webbprogram med Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klienthemlighet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte redan har skapat dina egna [Azure AD B2C-klient](tutorial-create-tenant.md), skapa en nu. Du kan använda en befintlig Azure AD B2C-klient.

## <a name="register-a-web-application"></a>Registrera ett webbprogram

1. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
2. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
3. Välj **Program** och därefter **Lägg till**.
4. Ange ett namn på programmet. Till exempel *webapp1*.
5. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
6. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ange den att lyssna lokalt på `https://localhost:44316`. Om du inte ännu vet portnumret, kan du ange ett platshållarvärde och ändra den senare.

    I testsyfte som den här självstudien kan du ange den till `https://jwt.ms` som visar innehållet i en token för granskning. För den här självstudiekursen ställer du in den **svars-URL** till `https://jwt.ms`.

    Svars-URL: en måste börja med schemat `https`, och alla svars-URL-värden måste dela en enda DNS-domän. Exempel: om programmet har en svars-URL för `https://login.contoso.com`, du kan lägga till data som den här URL: en `https://login.contoso.com/new`. Eller du kan referera till DNS-underdomänen av `login.contoso.com`, till exempel `https://new.login.contoso.com`. Om du vill ha ett program med `login-east.contoso.com` och `login-west.contoso.com` som svars-URL: er, måste du lägga till dessa-URL: Svarswebbadresser i den här ordningen: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Du kan lägga till två senare eftersom de är underdomäner i den första svars-URL `contoso.com`.

7. Klicka på **Skapa**.

## <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Om ditt program utbyter en kod för en token, måste du skapa en programhemlighet.

1. I den **Azure AD B2C - program** väljer du det program du skapade, till exempel *webapp1*.
2. Välj **nycklar** och välj sedan **skapa nycklar**.
3. Välj **spara** att visa nyckeln. Anteckna **appnyckel**-värdet. Du använder det här värdet som programhemlighet i programkoden.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klienthemlighet

Därefter lär du dig hur du skapar användarflöden så att användarna att registrera, logga in och hanterar sina profiler.

> [!div class="nextstepaction"]
> [Skapa användarflöden i Azure Active Directory B2C >](tutorial-create-user-flows.md)
