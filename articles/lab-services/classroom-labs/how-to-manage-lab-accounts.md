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
ms.openlocfilehash: ff2968f8e2fa9a705817b020f2daa6582d78029c
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225310"
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
Om du vill konfigurera ett klassrumslabb i ett labbkonto, måste du vara medlem i den **Labbskaparen** roll i labbkonto. Det konto som du använde för att skapa labb-kontot läggs automatiskt till den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb, kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb, gör du följande: 

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till** i verktygsfältet. 

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/access-control.png)
2. På sidan **Lägg till behörigheter** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägga till en användare i rollen Labbskapare](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Ange Marketplace-avbildningar som är tillgängliga för labbägare
I det här avsnittet ska ange du Marketplace-avbildningar som labbägare kan använda för att skapa labb för klassrum. 

1. Välj **Marketplace-avbildningar** på menyn till vänster. Som standard kan se du en fullständig lista över avbildningar (både aktiverade och inaktiverade). Du kan filtrera listan för att se endast aktiverad/inaktiverad avbildningar genom att välja den **aktiverat endast**/**endast inaktiverad** alternativet från listrutan längst upp. 

    ![Marketplace-sidan för bilder](../media/tutorial-setup-lab-account/marketplace-images-page.png)
2. Att **inaktivera** en Marketplace-avbildning som har aktiverats, gör du något av följande åtgärder: 
    1. Välj **... (tre punkter)**  i den sista kolumnen och välj **inaktivera bild**. 

        ![Inaktivera en bild](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Välj en eller flera avbildningar från listan genom att markera kryssrutorna innan namnen i listan och välj **inaktivera valda avbildningar**. 

        ![Inaktivera flera avbildningar](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. På samma sätt, som **aktivera** en Marketplace-avbildning, gör du något av följande åtgärder: 
    1. Välj **... (tre punkter)**  i den sista kolumnen och välj **aktivera avbildning**. 
    2. Välj en eller flera avbildningar från listan genom att markera kryssrutorna innan namnen i listan och välj **aktivera valda avbildningar**. 

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
