---
title: Självstudie – skapa en Azure Active Directory B2C-klient | Microsoft Docs
description: Lär dig hur du förbereder för att registrera dina program genom att skapa en Azure Active Directory B2C-klient med Azure portal.
services: B2C
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 12ce37579a6f93ba600c39416c7566ac673e9560
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723341"
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
2. Se till att du använder den katalog som innehåller din prenumeration genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller den. Den här katalogen är annorlunda än det som innehåller din Azure AD B2C-klient.

    ![Växla till prenumerationskatalogen](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
4. Sök efter och välj **Active Directory B2C**, och klicka sedan på **skapa**.
5. Välj **skapar en ny Azure AD B2C-klient**, ange ett organisationsnamn och ett första domännamn som används i innehavarens namn, Välj land (inte kan ändras senare) och klicka sedan på **skapa**.

    ![Skapa en klient](./media/tutorial-create-tenant/create-tenant.png)

    I det här exemplet är klientnamnet contoso0926Tenant.onmicrosoft.com

6. På den **Skapa ny B2C-klient eller länka till en befintlig klient** väljer **länka en befintlig Azure AD B2C-Klientorganisation till min Azure-prenumeration**väljer du den klient som du har skapat, Välj din prenumeration och sedan Klicka på **Skapa ny**.
7. Ange ett namn för resursgruppen som ska innehålla klienten, välj platsen och klicka sedan på **skapa**.
8. Om du vill börja använda den nya klientorganisationen, kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller den.

    ![Växla till directory-klient](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

> [!div class="nextstepaction"]
> [Registrera ditt program](tutorial-register-applications.md)
