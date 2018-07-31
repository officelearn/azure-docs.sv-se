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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: fd43c62f1a291a59d5d373437a49b263d6af4cb3
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345896"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Hantera labb konton i Azure Lab Services 
I Azure Lab Services är ett labbkonto en behållare för hanterade labbar som klassrum labs. En administratör konfigurerar ett testlabb-konto med Azure Lab Services och ger åtkomst till labbägare som kan skapa labb i kontot. Den här artikeln beskriver hur du skapar ett labbkonto, visa alla lab-konton eller ta bort ett labbkonto.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Skapa en resurs** på huvudmenyn till vänster.
3. Sök efter **Lab Services** på Azure Marketplace och välj **Lab Services** i listrutan. 
4. Välj **Lab Services (förhandsvisning)** i den filtrerade listan över tjänster. 
5. I fönstret **Skapa ett labbkonto** väljer du **Skapa**.
7. Utför följande i fönstret **Labbkonto**: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas. 
    5. Välj **Skapa**. 

        ![Fönstret Skapa ett labbkonto](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Om du inte ser sidan för labbkontot väljer du knappen **Meddelanden** och klickar sedan på knappen **Gå till resurs** i meddelandena. 

    ![Fönstret Skapa ett labbkonto](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb gör du följande: 

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till** i verktygsfältet. 

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/access-control.png)
2. På sidan **Lägg till behörigheter** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägga till en användare i rollen Labbskapare](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Ange Marketplace-avbildningar som är tillgängliga för labbägare
Som konto labbägare kan ange du Marketplace-avbildningar som lab skapare kan använda för att skapa labb i labbkonto. 

1. Välj **Marketplace-avbildningar** på menyn till vänster. Som standard kan se du en fullständig lista över avbildningar (både aktiverade och inaktiverade). Du kan filtrera listan för att endast se aktiverade/inaktiverade avbildningar genom att välja alternativet **Endast aktiverade**/**Endast inaktiverade** från listrutan längst upp. 
    
    ![Sida för Marketplace-avbildningar](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Marketplace-avbildningar som visas i listan är bara de filer som uppfyller följande villkor:
        
    - Skapar en enskild virtuell dator.
    - Använder Azure Resource Manager för att etablera virtuella datorer
    - Du behöver köpa ett extra licensieringsplan
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
3. Välj **Lab Services** för den **typ**. 
    Du kan också filtrera efter prenumeration, resursgrupp, platser och taggar. 

## <a name="delete-a-lab-account"></a>Ta bort ett labbkonto
Följ anvisningarna i föregående avsnitt som visar lab-konton i en lista. Använd följande instruktioner för att ta bort ett labbkonto: 

1. Välj den **labbkonto** som du vill ta bort. 
2. Välj **ta bort** från verktygsfältet. 
3. Typ **Ja** bekräftelse.
4. Välj **Ta bort**. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](tutorial-setup-classroom-lab.md)
- [Konfigurera ett labb](../tutorial-create-custom-lab.md)
