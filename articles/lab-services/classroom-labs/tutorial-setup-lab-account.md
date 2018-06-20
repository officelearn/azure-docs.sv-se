---
title: Konfigurera ett labbkonto med Azure Lab Services | Microsoft Docs
description: I den här självstudien konfigurerar du ett labbkonto med Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 600be7518bc526d3f147bb16377677854b676f63
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823137"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Självstudie: Konfigurera ett labbkonto med Azure Lab Services
I Azure Lab Services, fungerar ett labbkonto som det centrala kontot där alla din organisations labbar hanteras. I ditt labbkonto, ger du behörighet till andra att skapa labbar och konfigurera principer som gäller för alla labbar under labbkontot. I den här självstudien, får du lära dig hur du skapar ett labbkonto som en labbadministratör. 

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Skapa ett labbkonto
> * Lägga till en användare i rollen Labbskapare

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Skapa en resurs** på huvudmenyn till vänster.
3. Sök efter **Lab Services** på Azure Marketplace och välj **Lab Services** i listrutan. 
4. Välj **Lab Services (förhandsvisning)** i den filtrerade listan över tjänster. 
1. I fönstret **Skapa ett labbkonto** väljer du **Skapa**.
2. Utför följande i fönstret **Labbkonto**: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas. 
    5. Välj **Skapa**. 

        ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Om du inte ser sidan för labbkontot väljer du knappen **Meddelanden** och klickar sedan på knappen **Gå till resurs** i meddelandena. 

    ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen Labbskapare:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till** i verktygsfältet. 

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/access-control.png)
2. På sidan **Lägg till behörigheter** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägga till en användare i rollen Labbskapare](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett labbkonto. Mer information om hur du skapar ett klassrumslabb som ett yrke finns i nästa självstudie:

> [!div class="nextstepaction"]
> [Konfigurera ett klassrumslabb](tutorial-setup-classroom-lab.md)

