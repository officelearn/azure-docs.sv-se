---
title: Hantera labbkonton i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du skapar ett labbkonto, visar alla labbkonton eller tar bort ett labbkonto i en Azure-prenumeration.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284296"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Hantera labbkonton i Azure Lab Services 
I Azure Lab Services är ett labbkonto en behållare för hanterade labbtyper som klassrumslabb. En administratör skapar ett labbkonto med Azure Lab Services och ger åtkomst till labbägare som kan skapa labb i kontot. I den här artikeln beskrivs hur du skapar ett labbkonto, visar alla labbkonton eller tar bort ett labbkonto.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **Labbkonton** i avsnittet **DevOps.** Om du väljer stjärnan (`*`) bredvid **Labbkonton** läggs det till i avsnittet **FAVORITER** på den vänstra menyn. Från och med nästa gång väljer du **Labbkonton** under **FAVORITER**.

    ![Alla tjänster -> Labbkonton](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. På sidan **Labbkonton** väljer du **Lägg till** i verktygsfältet eller **Skapa labbkonto** på sidan. 

    ![Välj Lägg till på sidan Labbkonton](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Gör följande åtgärder på fliken **Grunderna** på sidan **Skapa ett labbkonto:** 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas.
    5. I fältet **Tillåt labbets skapare att välja plats för labbet** anger du om du vill att labbskaparna ska kunna välja en plats för labbet. Det här alternativet är inaktiverat som standard. När det är inaktiverat kan inte labbskaparna ange någon plats för labbet som de skapar. Labbarna skapas på den geografiska plats som är närmast labbkontot. När det är aktiverat kan en labbskapare välja en plats vid den tid då labbet skapas. Mer information finns i [Tillåt labbskapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md). 

        ![Skapa labbkonto -> Basics](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Välj **Nästa: Avancerat** längst ned på sidan för att navigera till fliken **Avancerat** och gör sedan följande: 
    1. Markera ett befintligt **delat bildgalleri** eller skapa ett. Du kan spara mallen VM i det delade bildgalleriet så att den kan återanvändas av andra. Detaljerad information om delade bildgallerier finns [i Använda ett delat bildgalleri i Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Ange om du automatiskt vill **stänga av Virtuella Windows-datorer** när användare kopplar från dem. Ange hur länge de virtuella datorerna ska vänta på att användaren ska återansluta innan den stängs av automatiskt. 
    3. För **virtuellt peer-nätverk**väljer du ett virtuellt peer-nätverk (VNet) för labbnätverket. Labb som skapas i det här kontot är anslutna till det valda virtuella nätverket och har åtkomst till resurserna i det valda virtuella nätverket. Mer information finns i [Ansluta labbets virtuella nätverk med ett virtuellt peer-nätverk](how-to-connect-peer-virtual-network.md).    
    8. Ange ett **adressintervall** för virtuella datorer i labbet. Adressintervallet ska finnas i CIDR-notationen (Classless inter-domain routing) (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i det här adressintervallet. Mer information finns i [Ange ett adressintervall för virtuella datorer i labbet](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![Skapa labbkonto -> Avancerat](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Välj **Nästa: Taggar** längst ned på sidan för att växla till fliken **Taggar.** Lägg till alla taggar som du vill associera med labbkontot. Taggar är namn-/värdepar som gör att du kan kategorisera resurser och visa samlad fakturering genom att använda samma tagg på flera resurser och resursgrupper. Mer information finns i [Använda taggar för att ordna dina Azure-resurser](../../azure-resource-manager/management/tag-resources.md).

    ![Skapa labbkonto -> taggar](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Välj **Granska + skapa** längst ned på den här sidan för att växla till fliken Granska + **skapa.** 
4. Granska sammanfattningsinformationen på den här sidan och välj **Skapa**. 

    ![Skapa labbkonto -> taggar](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Vänta tills distributionen är klar, expandera **Nästa steg**och välj Gå **till resurs** som visas i följande bild: 

    Du kan också välja **klockikonen** i verktygsfältet (**Meddelanden**), bekräfta att distributionen lyckades och sedan välja **Gå till resurs**. 

    Du kan också välja **Uppdatera** på sidan **Labbbkonton** och välja labbkontot som du skapade. 

    ![Fönstret Skapa ett labbkonto](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Visa labbkonton
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** på menyn. 
3. Välj **Labbkonton** för **typen**. 
    Du kan också filtrera efter prenumeration, resursgrupp, platser och taggar. 

    ![Alla resurser -> lab-konton](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Visa och hantera labb i labbkontot

1. På sidan **Labbkonto** väljer du **Alla labb** på den vänstra menyn.

    ![Labb i kontot](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Du ser en **lista över labb** i kontot med följande information: 
    1. Labbets namn.
    2. Det datum då labbet skapades. 
    3. E-postadress till den användare som skapade labbet. 
    4. Maximalt antal användare som tillåts komma in i labbet. 
    5. Status för labbet. 
    6. Rolltilldelningar. 

## <a name="delete-a-lab-in-the-lab-account"></a>Ta bort ett labb i labbkontot
Följ instruktionerna i föregående avsnitt för att se en lista över labben i labbkontot.

1. Välj **... (ellips)** och välj **Ta bort**. 

    ![Ta bort ett labb - knapp](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Välj **Ja** i varningsmeddelandet. 

    ![Bekräfta borttagning av labb](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Ta bort ett labbkonto
Följ instruktionerna från föregående avsnitt som visar labbkonton i en lista. Använd följande instruktioner för att ta bort ett labbkonto: 

1. Välj det **labbkonto** som du vill ta bort. 
2. Välj **Ta bort** i verktygsfältet. 

    ![Knappen Labbkonton -> Ta bort](../media/how-to-manage-lab-accounts/delete-button.png)
1. Skriv **Ja** för bekräftelse.
1. Välj **Ta bort**. 

    ![Ta bort labbkonto - bekräftelse](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Du kan också använda Az.LabServices PowerShell-modulen (förhandsversion) för att hantera labbkonton. Mer information finns [på Az.LabServices hemsida på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Ansluta labbets nätverk med ett virtuellt peer-nätverk](how-to-connect-peer-virtual-network.md)
- [Bifoga ett delat bildgalleri i ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägga till en användare som labbägare](how-to-add-user-lab-owner.md)
- [Visa brandväggsinställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)