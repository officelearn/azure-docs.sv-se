---
title: Hantera lab konton i Azure Lab Services | Microsoft Docs
description: Lär dig mer om att skapa ett labb-konto, visa alla lab konton eller ta bort ett labb-konto i en Azure-prenumeration.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 53494abead5701052f6e08f68b01ccdf1bfa211c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Hantera lab konton i Azure Lab Services (tidigare Azure DevTest Labs)
Den här artikeln beskriver hur du skapar ett labb-konto, visa alla lab konton eller ta bort ett labb-konto.

## <a name="create-a-lab-account"></a>Skapa ett labb-konto
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På huvudmenyn på vänster sida väljer **skapar du en resurs**.
3. Sök efter **Lab Services** i Azure Marketplace och välj **Lab Services** i den nedrullningsbara listan. 
4. Välj **Lab Sercices (förhandsgranskning)** i flitered lista över tjänster. 
5. I den **skapa ett labb konto** väljer **skapa**.
7. I den **Lab konto** och göra följande: 
    1. För **Lab kontonamn**, ange ett namn. 
    2. Välj den **Azure-prenumeration** som du vill skapa labb-kontot.
    3. För **resursgruppen**väljer **Skapa nytt**, och ange ett namn för resursgruppen.
    4. För **plats**, Välj en plats/den region där du vill labb-konto som ska skapas. 
    5. Välj **Skapa**. 

        ![Skapa ett labb konto fönster](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Om du inte ser sidan för labb-konto, Välj den **meddelanden** knappen och klicka sedan på **finns resurs** knapp i meddelanden. 

    ![Skapa ett labb konto fönster](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Du ser följande **lab konto** sidan:

    ![Sidan för labb-konto](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Lab Creator
För att ge lärare behörighet att skapa labs för sina klasser, lägger du till dem i labbet Creator rollen:

1. På den **Lab konto** väljer **åtkomstkontroll (IAM)**, och klicka på **+ Lägg till** i verktygsfältet. 

    ![Sidan för labb-konto](./media/tutorial-setup-lab-account/access-control.png)
2. På den **lägga till behörigheter** väljer **Lab Creator** för **rollen**, Välj den användare som du vill lägga till rollen i labbet skapare och välj **spara**. 

    ![Lägg till användare i rollen Lab Creator](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Visa lab konton
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla resurser** på menyn. 
3. Välj **Lab Services** för den **typen**. 
    Du kan också filtrera efter prenumeration, resursgrupp, platser och taggar. 

## <a name="delete-a-lab-account"></a>Ta bort ett labb-konto
Följ anvisningarna i föregående avsnitt som visar lab konton i en lista. Använd följande instruktioner för att ta bort ett labb-konto: 

1. Välj den **lab konto** som du vill ta bort. 
2. Välj **ta bort** från verktygsfältet. 
3. Typen **Ja** bekräftelse.
4. Välj **Ta bort**. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att ställa in ett testlabb med Azure Lab Services:

- [Skapa ett klassrum labb](tutorial-setup-classroom-lab.md)
- [Skapa en anpassad labb](tutorial-create-custom-lab.md)
