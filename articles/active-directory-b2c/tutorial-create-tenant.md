---
title: Självstudie – skapa en Azure Active Directory B2C-klient | Microsoft Docs
description: Lär dig hur du förbereder för att registrera dina program genom att skapa en Azure Active Directory B2C-klient med Azure portal.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.openlocfilehash: 20865fc3adf8610b5a4ce111e3db91aef714fdd6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448313"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Självstudier: Skapa en Azure Active Directory B2C-klient

Innan dina program kan interagera med Azure Active Directory (Azure AD) B2C, måste de registreras i en klient som du hanterar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

Du lär dig hur du registrerar ett program i nästa självstudie.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

1. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
2. I sökrutan ovanför listan över resurser i Azure Marketplace, Sök efter och välj **Active Directory B2C**, och klicka sedan på **skapa**.
3. Välj **skapar en ny Azure AD B2C-klient**, ange ett organisationsnamn och ett första domännamn som används i innehavarens namn, Välj land och klicka sedan på **skapa**. Vara säker på landet där klienten eftersom den inte kan ändras senare.

    ![Skapa en klient](./media/tutorial-create-tenant/create-tenant.png)

    I det här exemplet är klientnamnet contoso0522Tenant.onmicrosoft.com

Börja hantera din nya klient genom att klicka på ordet **här** står det **Klicka här för att hantera den nya katalogen**. Du ser en **Felsök** meddelande om att du måste länka din prenumeration till den nya innehavaren. 

## <a name="link-your-tenant-to-your-subscription"></a>Länka din klient till din prenumeration

Du måste länka din Azure AD B2C-klient till din Azure-prenumeration att aktivera alla funktioner och betalar för användningen. Om du inte länkar din klient till din prenumeration kan fungerar dina program inte korrekt.

1. Kontrollera att du använder den katalog som innehåller den prenumeration som du vill koppla till den nya klienten genom att växla mellan katalogen i det övre högra hörnet i Azure Portal.

    ![Växla kataloger](./media/tutorial-create-tenant/switch-directories.png)

    Och sedan välja den katalog som innehåller din prenumeration.

    ![Välj katalog](./media/tutorial-create-tenant/select-directory.png)

2. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
3. I sökrutan ovanför listan över resurser i Azure Marketplace, Sök efter och välj **Active Directory B2C**, och klicka sedan på **skapa**.
4. Välj **länka en befintlig Azure AD B2C-Klientorganisation till min Azure-prenumeration**, Välj den klient som du har skapat, Välj din prenumeration, anger *myContosoTenantRG* resursgruppens namn, godkänner den plats och klicka sedan på **skapa**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

> [!div class="nextstepaction"]
> [Aktivera en webbapp för autentisering med konton](active-directory-b2c-tutorials-web-app.md)