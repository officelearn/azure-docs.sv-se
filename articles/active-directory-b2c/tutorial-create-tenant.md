---
title: Självstudie – skapa en Azure Active Directory B2C-klient
description: Lär dig hur du förbereder för att registrera dina program genom att skapa en Azure Active Directory B2C-klient med Azure portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056313"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Självstudier: Skapa en Azure Active Directory B2C-klient

Innan dina program kan interagera med Azure Active Directory (Azure AD) B2C, måste de registreras i en klient som du hanterar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

Du lär dig hur du registrerar ett program i nästa självstudie.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrollera att du använder den katalog som innehåller din prenumeration. Klicka på den **katalog- och prenumerationsfilter** på den översta menyn, välj sedan den katalog som innehåller din prenumeration. Den här katalogen skiljer sig från det som innehåller din Azure AD B2C-klient.

    ![Växla till prenumerationskatalogen](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
4. Sök efter och välj **Active Directory B2C**, och klicka sedan på **skapa**.
5. Välj **skapar en ny Azure AD B2C-klient** och ange ett organisationsnamn och ett första domännamn. Välj land/region (inte kan ändras senare) och klicka sedan på **skapa**.

    Det ursprungliga domännamnet används som en del av namnet på din klientorganisation. I det här exemplet är klientnamnet *contoso0926Tenant.onmicrosoft.com*:

    ![Skapa en klient](./media/tutorial-create-tenant/create-tenant.PNG)

6. På den **Skapa ny B2C-klient eller länka till en befintlig klient** väljer **länka en befintlig Azure AD B2C-Klientorganisation till min Azure-prenumeration**.

    Välj den klient som du skapade och välj din prenumeration.

    Resursgrupp, Välj **Skapa ny**. Ange ett namn för resursgruppen som ska innehålla klienten, välj platsen och klicka sedan på **skapa**.
1. Om du vill börja använda den nya klientorganisationen, kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller den.

    Om du inte ser den nya Azure B2C-klientorganisationen i listan över först, uppdatera webbläsarfönstret och välj sedan den **katalog- och prenumerationsfilter** igen på den översta menyn.

    ![Växla till directory-klient](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

Lär dig sedan att registrera ett webbprogram i din nya klient.

> [!div class="nextstepaction"]
> [Registrera ditt program >](tutorial-register-applications.md)
