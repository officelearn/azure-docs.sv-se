---
title: Hantera labb konton i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar ett labbkonto, visa alla lab-konton eller ta bort ett labbkonto i en Azure-prenumeration.
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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: 7e3142e0274f2328d3e0c8a3e6f9a2e4c3d45d87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959145"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Hantera labb konton i Azure Lab Services 
I Azure Lab Services är ett labbkonto en behållare för hanterade labbar som klassrum labs. En administratör konfigurerar ett testlabb-konto med Azure Lab Services och ger åtkomst till labbägare som kan skapa labb i kontot. Den här artikeln beskriver hur du skapar ett labbkonto, visa alla lab-konton eller ta bort ett labbkonto.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** på den vänstra menyn. Välj **Lab-konton** i den **DEVOPS** avsnittet. Om du väljer star (`*`) bredvid **Lab-konton**, läggs den till den **Favoriter** avsnitt i den vänstra menyn. Nästa gång och senare, väljer du **Lab-konton** under **Favoriter**.

    ![Alla tjänster -> Lab-konton](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. På den **Lab-konton** väljer **Lägg till** i verktygsfältet. 

    ![Välj Lägg till på sidan Lab-konton](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. På den **Labbkonto** gör du följande åtgärder: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas. 
    5. Välj **Skapa**. 

        ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Välj den **klockikonen** i verktygsfältet (**meddelanden**), bekräfta att distributionen är klar och välj sedan **gå till resurs**. 

    Du kan också välja **uppdatera** på den **Lab-konton** och välj labb-kontot som du skapade. 

    ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb gör du följande: 

Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen **Labbskapare**:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Access Control -> knappen Lägg till rolltilldelning](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På den **Lägg till rolltilldelning** väljer **Labbskaparen** för **rollen**, Välj den användare som du vill lägga till rollen Lab skapare och välj **spara**. 

    ![Lägg till labbskaparen](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>Ange Marketplace-avbildningar som är tillgängliga för skapare av testlabb
Som labbkontoägare kan du ange Marketplace-avbildningar som labbskapare kan använda för att skapa labb i labbkontot. 

1. Välj **Marketplace-avbildningar** på menyn till vänster. Som standard kan se du en fullständig lista över avbildningar (både aktiverade och inaktiverade). Du kan filtrera listan för att endast se aktiverade/inaktiverade avbildningar genom att välja alternativet **Endast aktiverade**/**Endast inaktiverade** från listrutan längst upp. 
    
    ![Sida för Marketplace-avbildningar](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    De Marketplace-avbildningar som visas i listan är bara de filer som uppfyller följande villkor:
        
    - Skapar en enskild virtuell dator.
    - Använder Azure Resource Manager för att etablera virtuella datorer
    - Kräver inte att du köper en extra licensieringsplan
2. För att **inaktivera** en Marketplace-avbildning som har aktiverats genomför du någon av följande åtgärder: 
    1. Välj **... (ellips)** i den sista kolumnen och välj **Inaktivera avbildning**. 

        ![Inaktivera en avbildning](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Välj en eller flera avbildningar från listan genom att markera kryssrutorna före avbildningsnamnen i listan och välja **Inaktivera valda avbildningar**. 

        ![Inaktivera flera avbildningar](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. På ett liknande sätt **aktiverar** du en Marketplace-avbildning med någon av följande åtgärder: 
    1. Välj **... (ellips)** i den sista kolumnen och välj **Aktivera avbildning**. 
    2. Välj en eller flera avbildningar från listan genom att markera kryssrutorna före avbildningsnamnen i listan och välja **Aktivera valda avbildningar**. 

## <a name="view-lab-accounts"></a>Visa lab-konton
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla resurser** på menyn. 
3. Välj **Lab-konton** för den **typ**. 
    Du kan också filtrera efter prenumeration, resursgrupp, platser och taggar. 

    ![Alla resurser -> Lab-konton](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Ta bort ett labbkonto
Följ anvisningarna i föregående avsnitt som visar lab-konton i en lista. Använd följande instruktioner för att ta bort ett labbkonto: 

1. Välj den **labbkonto** som du vill ta bort. 
2. Välj **ta bort** från verktygsfältet. 

    ![Lab-konton -> Ta bort-knappen](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ja** bekräftelse.
1. Välj **Ta bort**. 

    ![Ta bort labbkonto - bekräftelse](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Visa och hantera labb i labbkonto

1. På den **Labbkonto** väljer **Labs** på den vänstra menyn.

    ![Labs i kontot](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Du ser en **listan över labbar** i kontot med följande information: 
    1. Namnet på labbet.
    2. Det datum då labbet skapades. 
    3. E-postadressen för användaren som skapade labbet. 
    4. Maximalt antal användare som har åtkomst till labbet. 
    5. Status för labbet. 

## <a name="delete-a-lab-in-the-lab-account"></a>Ta bort ett labb i labbkonto
Följ anvisningarna i föregående avsnitt för att se en lista över labs i labbkonto.

1. Välj **... (tre punkter)** , och välj **ta bort**. 

    ![Ta bort ett labb - knappen](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Välj **Ja** i varningsmeddelandet. 

    ![Bekräfta borttagning av testlabb](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)