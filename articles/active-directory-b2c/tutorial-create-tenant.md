---
title: Självstudiekurs – skapa en Azure Active Directory B2C-klient | Microsoft Docs
description: Lär dig hur du förbereder för att registrera ditt program genom att skapa en Azure Active Directory B2C-klient som använder Azure portal.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/19/2018
ms.author: davidmu
ms.openlocfilehash: 04f3dbbe461bfe0f07b6930a92bdd8a721e55098
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296101"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Självstudier: Skapa en Azure Active Directory B2C-klient

Innan dina program kan interagera med Azure Active Directory (AD Azure) B2C, måste de registreras i en klient som du hanterar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

Du lär dig hur du registrerar ett program i nästa kurs.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

1. Välj **skapar du en resurs** i det övre vänstra hörnet i Azure-portalen.
2. I sökrutan ovanför listan över resurser i Azure Marketplace, söka efter och välja **Active Directory B2C**, och klicka sedan på **skapa**.
3. Välj **skapa en ny Azure AD B2C-klient**, ange ett organisationsnamn och ett första domännamn som används i innehavarens namn, väljer du landet och klicka sedan på **skapa**. Glöm inte av klienten eftersom den inte kan ändras senare.

    ![Skapa en klient](./media/tutorial-create-tenant/create-tenant.png)

    I det här exemplet är klientnamnet contoso0522Tenant.onmicrosoft.com

Om du vill börja hantera dina nya innehavaren, klickar du på ordet **här** står det **Klicka här, för att hantera nya directory**. Du ser en **Felsök** meddelande om att du måste länka din prenumeration till den nya innehavaren. 

## <a name="link-your-tenant-to-your-subscription"></a>Länka din klient till din prenumeration

Du måste länka din Azure AD B2C-klient till din Azure-prenumeration att aktivera alla funktioner och betalar avgifter för användning. Om du inte länka din klient till din prenumeration, fungerar dina program inte korrekt.

1. Kontrollera att du använder den katalog som innehåller den prenumeration som du vill koppla till den nya innehavaren genom att växla katalogen i övre högra hörnet i Azure-portalen.

    ![Växla kataloger](./media/tutorial-create-tenant/switch-directories.png)

    Och sedan välja den katalog som innehåller din prenumeration.

    ![Välj katalog](./media/tutorial-create-tenant/select-directory.png)

2. Välj **skapar du en resurs** i det övre övre vänstra hörnet i Azure-portalen.
3. I sökrutan ovanför listan över resurser i Azure Marketplace, söka efter och välja **Active Directory B2C**, och klicka sedan på **skapa**.
4. Välj **länka ett befintligt Azure AD B2C-klient till min Azure-prenumeration**, Välj den klient som du har skapat, väljer din prenumeration, ange *myContosoTenantRG* resursgruppens namn, acceptera den plats och klicka sedan på **skapa**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration

> [!div class="nextstepaction"]
> [Aktivera ett webbprogram att autentisera med konton](active-directory-b2c-tutorials-web-app.md)