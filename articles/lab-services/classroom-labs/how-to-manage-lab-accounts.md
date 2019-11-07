---
title: Hantera labb konton i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar ett labb konto, visar alla labb konton eller tar bort ett labb konto i en Azure-prenumeration.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: ee64780bca13bf497793dc90ad22d3eaf91949a6
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606332"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Hantera labb konton i Azure Lab Services 
I Azure Lab Services är ett labb konto en behållare för hanterade labb typer, till exempel klass rums labb. En administratör konfigurerar ett labb konto med Azure Lab Services och ger till gång till labb ägare som kan skapa labb i kontot. Den här artikeln beskriver hur du skapar ett labb konto, visar alla labb konton eller tar bort ett labb konto.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **labb konton** i avsnittet **DevOps** . Om du väljer stjärnan (`*`) bredvid **Labbkonton** läggs det till i avsnittet **FAVORITER** på den vänstra menyn. Från och med nästa gång väljer du **Labbkonton** under **FAVORITER**.

    ![Alla tjänster -> Labbkonton](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. På sidan **Labbkonton** väljer du **+ Lägg till** i verktygsfältet. 

    ![Välj Lägg till på sidan Labbkonton](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Utför följande åtgärder i fönstret **Labbkonton**: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas. 
    5. Välj ett befintligt **delat avbildnings Galleri** eller skapa ett. Du kan spara mallen VM i det delade avbildnings galleriet så att den återanvänds av andra. Detaljerad information om delade avbildnings gallerier finns [i använda ett delat avbildnings galleri i Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. För **peer Virtual Network**väljer du ett peer-virtuellt nätverk (VNet) för labb nätverket. Labb som skapats i det här kontot är anslutna till det valda virtuella nätverket och har åtkomst till resurserna i det valda virtuella nätverket. 
    7. Ange ett **adress intervall** för virtuella datorer i labbet. Adress intervallet bör vara i CIDR-notation (Classless Inter-Domain routing) (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i det här adress intervallet. Mer information finns i [Ange ett adress intervall för virtuella datorer i labbet](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. I fältet **Tillåt labbets skapare att välja plats för labbet** anger du om du vill att labbskaparna ska kunna välja en plats för labbet. Det här alternativet är inaktiverat som standard. När det är inaktiverat kan inte labbskaparna ange någon plats för labbet som de skapar. Labbarna skapas på den geografiska plats som är närmast labbkontot. När det är aktiverat kan en labbskapare välja en plats vid den tid då labbet skapas.      
    9. Välj **Skapa**. 

        ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Välj **klockikonen** i verktygsfältet (**meddelanden**), bekräfta att distributionen är klar och välj sedan **Gå till resurs**. 

    Du kan också välja **Uppdatera** på sidan **Labbbkonton** och välja labbkontot som du skapade. 

    ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Visa labb konton
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla resurser** på menyn. 
3. Välj **labb konton** för **typen**. 
    Du kan också filtrera efter prenumeration, resurs grupp, platser och taggar. 

    ![Alla resurser-> Lab-konton](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Visa och hantera labb i labb kontot

1. På sidan **labb konto** väljer du **labb** på den vänstra menyn.

    ![Labb i kontot](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Du ser en **lista med labb** i kontot med följande information: 
    1. Labbets namn.
    2. Det datum då labbet skapades. 
    3. E-postadressen till den användare som skapade labbet. 
    4. Maximalt antal användare som tillåts i labbet. 
    5. Labbets status. 

## <a name="delete-a-lab-in-the-lab-account"></a>Ta bort ett labb i labb kontot
Följ anvisningarna i föregående avsnitt om du vill se en lista över labben i labb kontot.

1. Välj **... (tre punkter)** och välj **ta bort**. 

    ![Ta bort en labb knapp](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Välj **Ja** i varnings meddelandet. 

    ![Bekräfta borttagning av labb](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Ta bort ett labb konto
Följ anvisningarna från föregående avsnitt som visar labb konton i en lista. Använd följande instruktioner för att ta bort ett labb konto: 

1. Välj det **labb konto** som du vill ta bort. 
2. Välj **ta bort** från verktygsfältet. 

    ![Labb konton – > knappen Ta bort](../media/how-to-manage-lab-accounts/delete-button.png)
1. Skriv **Ja** för bekräftelse.
1. Välj **Ta bort**. 

    ![Ta bort labb konto – bekräftelse](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Du kan också använda PowerShell-modulen AZ. LabServices (för hands version) för att hantera labb konton. Mer information finns på [Start sidan för AZ. LabServices på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Nästa steg
Se följande artikel: [så här konfigurerar du labb konton](how-to-configure-lab-accounts.md).