---
title: Testa din app i Azure | Microsoft Docs
description: Lär dig hur du skapar en filresurs i ett labb och montera den på din lokala dator och en virtuell dator i labbet, och sedan distribuera desktop/webbprogram till filresursen och testa dem.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: a58ebe14749add30deb229f1b7e30fbb6e9921d7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814912"
---
# <a name="test-your-app-in-azure"></a>Testa appen i Azure 
Den här artikeln innehåller steg för att testa ditt program i Azure med DevTest Labs. Först måste du ställa in en filresurs i ett laboratorium och montera den som en enhet på utvecklingsdatorn lokal och en virtuell dator i ett labb. Sedan använder du Visual Studio 2017 för att distribuera appen till filresursen så att du kan köra appen på den virtuella datorn i laboratoriet.  

## <a name="prerequisites"></a>Förutsättningar 
1. [Skapa en Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en och logga in på [Azure-portalen](https://portal.azure.com).
2. Följ instruktionerna i [i den här artikeln](devtest-lab-create-lab.md) att skapa ett labb med Azure DevTest Labs. Fäst labb på din instrumentpanel så att du lätt kan hitta den nästa gång du loggar in. Azure DevTest Labs kan du snabbt skapa resurser i Azure som minimerar avbrott och kontrollerar kostnaderna. Läs mer om DevTest Labs i [översikt](devtest-lab-overview.md). 
3. Skapa ett Azure Storage-konto i testgruppen resurs genom att följa instruktionerna i den [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md) artikeln. På den **skapa lagringskonto** väljer **Använd befintlig** för **resursgrupp**, och välj den **övningen resursgrupp**. 
4. Skapa en filresurs i Azure storage genom att följa instruktionerna i den [skapa en filresurs i Azure Files](../storage/files/storage-how-to-create-file-share.md) artikeln. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Montera filresursen på den lokala datorn
1. På den lokala datorn, använder skriptet från [spara Azure filen dela autentiseringsuppgifter i Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) delen av [använder en Azure-filresurs med Windows](../storage/files/storage-how-to-use-files-windows.md) artikeln. 
2. Använd sedan `net use` kommando för att montera filresursen på din dator. Här är exempel-kommando: Ange namnet på Azure storage och namn på filresurs innan du kör kommandot. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Skapa en virtuell dator i labbet
1. På den **filresurs** väljer den **resursgrupp** i adressfältmenyn längst upp. Du ser den **resursgrupp** sidan. 
    
    ![Välj resursgrupp adressfältmenyn](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. På den **resursgrupp** väljer den **lab** du skapade i DevTest Labs.

    ![Välja labbet](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. På den **labb** för labbet, väljer **+ Lägg till** i verktygsfältet. 

    ![Lägg till knapp för labbet](media/test-app-in-azure/add-button-in-lab.png)
4. På den **väljer bas** söker du efter **smalldisk**, och välj **[smalldisk] Windows Server 2016 Datacenter**. 

    ![Välj liten disk Windows server](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. På den **VM** anger **virtuellt datornamn**, **användarnamn**, **lösenord**, och välj **skapa** .    
    
    ![Skapa VM-sida](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Montera filresursen på den virtuella datorn
1. När du har skapat den virtuella datorn, väljer du den **VM** i listan.    

    ![Välj lab VM](media/test-app-in-azure/select-lab-vm.png)
2. Välj **Connect** i verktygsfältet för att ansluta till den virtuella datorn. 
3. [Installera Azure PowerShell](https://azure.microsoft.com/downloads/) med hjälp av den **Windows installerar** länken i den **kommandoradsverktyg** avsnittet. Andra sätt att installera Azure PowerShell, se [i den här artikeln](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.8.1).
4. Följ instruktionerna i monterings avsnittet dela filen. 

## <a name="publish-your-app-from-visual-studio"></a>Publicera en app från Visual Studio
I det här avsnittet kan du publicera din app från Visual Studio för att testa virtuell dator i molnet.

1. Skapa en desktop/webbprogram med hjälp av Visual Studio 2017.
2. Skapa din app.
3. Om du vill publicera appen högerklickar du på projektet i den **Solution Explorer**, och välj **publicera**. 
4. I den **Publiceringsguiden**, ange den **enhet** som är mappad till filresursen.

    **Skrivbordsappen:**

    ![Skrivbordsapp](media/test-app-in-azure/desktop-app.png)

    **Webbapp:**

    ![Webbapp](media/test-app-in-azure/web-app.png)

1. Välj **nästa** slutförts publicera arbetsflödet och välj **Slutför**. När du slutför stegen i guiden, Visual Studio skapar ditt program och publicerar den till filresursen. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testa appen på din Virtuella testdator i laboratoriet

1. Gå till sidan virtuell dator för din virtuella dator i labbet. 
2. Välj **starta** i verktygsfältet för att starta den virtuella datorn om den är i stoppad tillstånd. Du kan konfigurera principer för automatisk start och automatisk avstängning för den virtuella datorn att undvika att starta och stoppa varje gång. 
3. Välj **Anslut**.

    ![Sidan virtuell dator](media/test-app-in-azure/virtual-machine-page.png)
4. I den virtuella datorn och starta **Utforskaren**, och välj **den här datorn** att hitta filresursen.

    ![Hitta resursen på den virtuella datorn](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Av någon anledning, om det inte går att hitta filresursen på den virtuella datorn eller på den lokala datorn, du kan montera den genom att köra den `net use` kommando. Du hittar den `net use` på den **Connect** guiden för din **filresurs** i Azure-portalen.
1. Öppna filresursen och kontrollera att du ser den app som du har distribuerat från Visual Studio. 

    ![Öppna resursen på den virtuella datorn](media/test-app-in-azure/open-file-share.png)

    Du kan nu komma åt och testa din app i test VM som du skapade i Azure.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för att lära dig hur du använder virtuella datorer i ett labb. 

- [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)
- [Starta om ett labb VM](devtest-lab-restart-vm.md)
- [Ändra storlek på ett labb VM](devtest-lab-resize-vm.md)
