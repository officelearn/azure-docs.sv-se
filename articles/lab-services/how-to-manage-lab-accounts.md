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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 1286698fb7dd13c7568a0fa8b20c50511d5a6919
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Hantera lab konton i Azure Lab Services (tidigare Azure DevTest Labs)
Den här artikeln beskriver hur du skapar ett labb-konto, visa alla lab konton eller ta bort ett labb-konto.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Skapa en resurs** på huvudmenyn till vänster.
3. Sök efter **Lab Services** på Azure Marketplace och välj **Lab Services** i listrutan. 
4. Välj **Lab Sercices (förhandsgranskning)** i flitered lista över tjänster. 
5. I fönstret **Skapa ett labbkonto** väljer du **Skapa**.
7. Utför följande i fönstret **Labbkonto**: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas. 
    5. Välj **Skapa**. 

        ![Fönstret Skapa ett labbkonto](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Om du inte ser sidan för labbkontot väljer du knappen **Meddelanden** och klickar sedan på knappen **Gå till resurs** i meddelandena. 

    ![Fönstret Skapa ett labbkonto](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen Labbskapare:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till** i verktygsfältet. 

    ![Sidan Labbkonto](./media/tutorial-setup-lab-account/access-control.png)
2. På sidan **Lägg till behörigheter** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägga till en användare i rollen Labbskapare](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


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
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](tutorial-setup-classroom-lab.md)
- [Konfigurera ett anpassat labb](tutorial-create-custom-lab.md)
