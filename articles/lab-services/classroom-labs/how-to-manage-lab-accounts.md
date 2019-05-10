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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412808"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Hantera labb konton i Azure Lab Services 
Ett labbkonto är en behållare för hanterade lab typer, till exempel klassrum labb i Azure Lab Services. En administratör konfigurerar ett testlabb-konto med Azure Lab Services och ger åtkomst till labbägare som kan skapa labb i kontot. Den här artikeln beskriver hur du skapar ett labbkonto, visa alla lab-konton eller ta bort ett labbkonto.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **Labbkonton** i **DEVOPS**-avsnittet. Om du väljer stjärnan (`*`) bredvid **Labbkonton** läggs det till i avsnittet **FAVORITER** på den vänstra menyn. Från och med nästa gång väljer du **Labbkonton** under **FAVORITER**.

    ![Alla tjänster -> Labbkonton](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. På sidan **Labbkonton** väljer du **+ Lägg till** i verktygsfältet. 

    ![Välj Lägg till på sidan Labbkonton](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Utför följande åtgärder i fönstret **Labbkonton**: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas. 
    5. Välj en befintlig **delade bildgalleriet** eller skapa ett. Du kan spara mallen VM i delade galleriet för att det ska återanvändas av andra. Detaljerad information om delade bildgallerier finns [använder en delad bildgalleriet i Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. För **Peerkopplade virtuella nätverket**, välja ett peer virtuellt nätverk (VNet) i testlabbets nätverk. Labs som skapats i det här kontot är anslutna till det valda virtuella nätverket och har åtkomst till resurser i det valda virtuella nätverket. 
    7. Ange en **adressintervall** för virtuella datorer i labbet. Adressintervallet ska vara i classless Inter-Domain routing (CIDR)-format (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i den här adressintervall. Mer information finns i [ange ett adressintervall för virtuella datorer i labbet](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. I fältet **Tillåt labbets skapare att välja plats för labbet** anger du om du vill att labbskaparna ska kunna välja en plats för labbet. Det här alternativet är inaktiverat som standard. När det är inaktiverat kan inte labbskaparna ange någon plats för labbet som de skapar. Labbarna skapas på den geografiska plats som är närmast labbkontot. När det är aktiverat kan en labbskapare välja en plats vid den tid då labbet skapas.      
    9. Välj **Skapa**. 

        ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Välj **klockikonen** i verktygsfältet (**meddelanden**), bekräfta att distributionen är klar och välj sedan **Gå till resurs**. 

    Du kan också välja **Uppdatera** på sidan **Labbbkonton** och välja labbkontot som du skapade. 

    ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Visa lab-konton
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla resurser** på menyn. 
3. Välj **Lab-konton** för den **typ**. 
    Du kan också filtrera efter prenumeration, resursgrupp, platser och taggar. 

    ![Alla resurser -> Lab-konton](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

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

## <a name="delete-a-lab-account"></a>Ta bort ett labbkonto
Följ anvisningarna i föregående avsnitt som visar lab-konton i en lista. Använd följande instruktioner för att ta bort ett labbkonto: 

1. Välj den **labbkonto** som du vill ta bort. 
2. Välj **ta bort** från verktygsfältet. 

    ![Lab-konton -> Ta bort-knappen](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ja** bekräftelse.
1. Välj **Ta bort**. 

    ![Ta bort labbkonto - bekräftelse](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)


## <a name="next-steps"></a>Nästa steg
Se följande artikel: [Så här konfigurerar du lab-konton](how-to-configure-lab-accounts.md).