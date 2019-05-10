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
ms.date: 04/24/2019
ms.author: spelluru
ms.openlocfilehash: 0977c4537e409b59be7f9031c488b3317f9f2f0f
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415800"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Självstudier: Konfigurera ett labbkonto med Azure Lab Services
I Azure Lab Services, fungerar ett labbkonto som det centrala kontot där alla din organisations labbar hanteras. I ditt labbkonto, ger du behörighet till andra att skapa labbar och konfigurera principer som gäller för alla labbar under labbkontot. I den här självstudien, får du lära dig hur du skapar ett labbkonto som en labbadministratör. 

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Skapa ett labbkonto
> * Lägga till en användare i rollen Labbskapare
> * Ange Marketplace-avbildningar som är tillgängliga för labbägare

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **Lab Services** i den **DEVOPS** avsnittet. Om du väljer star (`*`) bredvid **Lab Services**, läggs den till den **Favoriter** avsnitt i den vänstra menyn. Nästa gång och senare, väljer du **Lab Services** under **Favoriter**.

    ![Alla tjänster -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. På den **Lab Services** väljer **Lägg till** i verktygsfältet. 

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

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb gör du följande: 

Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen **Labbskapare**:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** följt av **+ Lägg till** och sedan **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Åtkomstkontroll -> Lägg till rolltilldelning, knapp](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På sidan **Lägg till rolltilldelning** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägg till labbuppgiftsförfattare](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Ange Marketplace-avbildningar som är tillgängliga för labbuppgiftsförfattare
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

## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett labbkonto. Mer information om hur du skapar ett klassrumslabb som ett yrke finns i nästa självstudie:

> [!div class="nextstepaction"]
> [Konfigurera ett klassrumslabb](tutorial-setup-classroom-lab.md)

