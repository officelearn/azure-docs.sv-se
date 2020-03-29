---
title: Så här testar du din app i Azure | Microsoft-dokument
description: Lär dig hur du skapar en filresurs i ett labb och monterar den på din lokala dator och en virtuell dator i labbet och distribuerar sedan skrivbords-/webbprogram till filresursen och testar dem.
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
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65872469"
---
# <a name="test-your-app-in-azure"></a>Testa appen i Azure 
Den här artikeln innehåller steg för att testa ditt program i Azure med DevTest Labs. Först konfigurerar du en filresurs i ett labb och monterar den som en enhet på din lokala utvecklingsdator och en virtuell dator i ett labb. Sedan använder du Visual Studio 2019 för att distribuera din app till filresursen så att du kan köra appen på den virtuella datorn i labbet.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav 
1. [Skapa en Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en och logga in på [Azure-portalen](https://portal.azure.com).
2. Följ instruktionerna i [den här artikeln](devtest-lab-create-lab.md) för att skapa ett labb med Azure DevTest Labs. Fäst labbet på instrumentpanelen så att du enkelt kan hitta det nästa gång du loggar in. Med Azure DevTest Labs kan du snabbt skapa resurser i Azure genom att minimera avfall och kontrollera kostnader. Mer information om DevTest Labs finns i [översikten](devtest-lab-overview.md). 
3. Skapa ett Azure Storage-konto i labbets resursgrupp genom att följa instruktionerna i artikeln [Skapa ett lagringskonto.](../storage/common/storage-create-storage-account.md) På sidan **Skapa lagringskonto** väljer du **Använd befintlig** för **resursgrupp**och väljer **labbets resursgrupp**. 
4. Skapa en filresurs i din Azure-lagring genom att följa instruktionerna i artikeln [Skapa en filresurs i Azure Files.](../storage/files/storage-how-to-create-file-share.md) 

## <a name="mount-the-file-share-on-your-local-machine"></a>Montera filresursen på din lokala dator
1. På den lokala datorn använder du skriptet från [Autentiseringsuppgifter för Azure-filresurs i Windows-avsnittet](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) [Använd en Azure-filresurs med](../storage/files/storage-how-to-use-files-windows.md) Windows-artikel. 
2. Använd `net use` sedan kommandot för att montera filresursen på datorn. Här är exempelkommandot: Ange ditt Azure-lagringsnamn och filresursnamn innan du kör kommandot. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Skapa en virtuell dator i labbet
1. På sidan **Fildelning** väljer du **resursgruppen** i sökvägen högst upp. Du ser sidan **Resursgrupp.** 
    
    ![Välj resursgrupp från sökvägen](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. På sidan **Resursgrupp** väljer du **det labb** som du skapade i DevTest Labs.

    ![Välja labbet](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. På sidan **DevTest Lab** för ditt labb väljer du **+ Lägg till** i verktygsfältet. 

    ![Knappen Lägg till för labbet](media/test-app-in-azure/add-button-in-lab.png)
4. Sök efter **litendisk**på sidan **Välj bas** och välj **[smalldisk] Windows Server 2016 Data Center**. 

    ![Välj Windows-server för liten disk](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. På sidan **Virtuell dator** anger du namn på **virtuella datorer,** **användarnamn,** **lösenord**och väljer **Skapa**.    
    
    ![Skapa sida för virtuella datorer](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Montera filresursen på den virtuella datorn
1. När den virtuella datorn har skapats väljer du den **virtuella datorn** i listan.    

    ![Välj den virtuella labbdatorn](media/test-app-in-azure/select-lab-vm.png)
2. Välj **Anslut** i verktygsfältet om du vill ansluta till den virtuella datorn. 
3. [Installera Azure PowerShell](/powershell/azure/install-az-ps).
4. Följ instruktionerna i avsnittet Montera fildelningen. 

## <a name="publish-your-app-from-visual-studio"></a>Publicera din app från Visual Studio
I det här avsnittet publicerar du din app från Visual Studio till en testad virtuell dator i molnet.

1. Skapa ett skrivbords-/webbprogram med Visual Studio 2019.
2. Skapa din app.
3. Om du vill publicera appen högerklickar du på projektet i **Lösningsutforskaren**och väljer **Publicera**. 
4. Ange den **enhet** som är mappad till filresursen i **guiden Publicera.**

    **Skrivbordsapp:**

    ![Skrivbordsapp](media/test-app-in-azure/desktop-app.png)

    **Webbapp:**

    ![Webbapp](media/test-app-in-azure/web-app.png)

1. Välj **Nästa** om du vill slutföra publiceringsarbetsflödet och välj **Slutför**. När du är klar med guidestegen skapar Visual Studio programmet och publicerar det till filresursen. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testa appen på testdatorn i labbet

1. Navigera till sidan för virtuella datorer för den virtuella datorn i labbet. 
2. Välj **Start** i verktygsfältet om du vill starta den virtuella datorn om den är i stoppat tillstånd. Du kan ställa in principer för automatisk start och automatisk avstängning för den virtuella datorn för att undvika att starta och stoppa varje gång. 
3. Välj **Anslut**.

    ![Sida för virtuell dator](media/test-app-in-azure/virtual-machine-page.png)
4. Starta **Utforskaren**i den virtuella datorn och välj **Den här datorn** för att hitta filresursen.

    ![Hitta resurs på virtuell dator](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Om du inte kan hitta filresursen på den virtuella datorn eller på den lokala datorn `net use` kan du av någon anledning återmontera den genom att köra kommandot. Du hittar `net use` kommandot i **Anslutningsguiden** för **filresursen** i Azure-portalen.
1. Öppna filresursen och bekräfta att du ser appen du distribuerade från Visual Studio. 

    ![Öppen resurs på virtuell dator](media/test-app-in-azure/open-file-share.png)

    Du kan nu komma åt och testa din app i testdatorn som du skapade i Azure.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om du vill lära dig hur du använder virtuella datorer i ett labb. 

- [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)
- [Starta om en virtuell labbdator](devtest-lab-restart-vm.md)
- [Ändra storlek på en virtuell labbdator](devtest-lab-resize-vm.md)
