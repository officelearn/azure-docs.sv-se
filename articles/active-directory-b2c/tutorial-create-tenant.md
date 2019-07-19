---
title: Självstudie – Skapa en Azure Active Directory B2C-klient
description: Lär dig hur du förbereder registreringen av dina program genom att skapa en Azure Active Directory B2C-klient med hjälp av Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ce389d1f434fb0eb37413873b02e3ddfff8f7fba
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849396"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Självstudier: Skapa en Azure Active Directory B2C klient

Innan dina program kan interagera med Azure Active Directory-B2C (Azure AD) måste de registreras i en klient som du hanterar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

Du får lära dig hur du registrerar ett program i nästa självstudie.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din prenumeration. Klicka på **filtret katalog och prenumeration** på den översta menyn och välj sedan den katalog som innehåller din prenumeration. Den här katalogen skiljer sig från den som kommer att innehålla Azure AD B2C klient.

    ![Katalog-och prenumerations filter med en prenumerations klient vald](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal.
4. Sök efter och välj **Active Directory B2C**och klicka sedan på **skapa**.
5. Välj **skapa en ny Azure AD B2C klient** och ange ett organisations namn och ett första domän namn. Välj land/region (det kan inte ändras senare) och klicka sedan på **skapa**.

    Det första domän namnet används som en del av ditt klient namn. I det här exemplet är klient namnet *contoso0926Tenant.onmicrosoft.com*:

    ![Sidan B2C för skapande av klient i Azure Portal](./media/tutorial-create-tenant/create-tenant.PNG)

6. På sidan **Skapa ny B2C-klient eller länka till befintlig klient** väljer du **länka en befintlig Azure AD B2C klient till min Azure-prenumeration**.

    Välj den klient som du skapade och välj din prenumeration.

    För resurs grupp väljer du **Skapa ny**. Ange ett namn för den resurs grupp som ska innehålla klienten, Välj platsen och klicka sedan på **skapa**.
1. Om du vill börja använda din nya klient måste du kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att klicka på **filtret katalog och prenumeration** på den översta menyn och välja den katalog som innehåller den.

    Om du först inte ser din nya Azure B2C-klient i listan uppdaterar du webbläsarfönstret och väljer sedan **katalogen och prenumerations filtret** igen på den översta menyn.

    ![Katalog-och prenumerations filter med B2C-klient valt](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

Nu ska du lära dig hur du registrerar ett webb program i din nya klient.

> [!div class="nextstepaction"]
> [Registrera dina program >](tutorial-register-applications.md)
