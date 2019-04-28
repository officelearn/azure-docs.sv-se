---
title: Snabbstart – Skapa en Windows-VM i Azure Portal | Microsoft Docs
description: I den här snabbstarten lär du dig hur du skapar en virtuell Windows-dator med hjälp av Azure Portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/19/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 08c33068845855fca85f793c21264582be423c2f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078446"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Windows-dator på Azure-portalen

Det går att skapa virtuella Azure-datorer via Azure Portal. Med den här metoden får du ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. Den här snabbstarten beskriver hur du använder Azure Portal för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2016. För att sedan se hur den fungerar i praktiken ansluter du till den virtuella datorn med RDP och installerar IIS-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure Portal.

1. I den **New** sidan under **vanliga**väljer **Windows Server 2016 Datacenter**.

1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myResourceGroup* som namn. 

    ![Skapa en ny resursgrupp för din virtuella dator](./media/quick-create-portal/project-details.png)

1. Under **Instansinformation** skriver du *myVM* för **Namn på virtuell dator** och väljer *USA, östra* som **Plats**. Låt de övriga standardvärdena vara som de är.

    ![Avsnittet Instansinformation](./media/quick-create-portal/instance-details.png)

1. Under **Administratörskonto**, anger du ett användarnamn, som *azureuser*, och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Ange ditt användarnamn och lösenord](./media/quick-create-portal/administrator-account.png)

1. Under **Regler för inkommande portar** väljer du **Tillåt valda portar** och sedan **RDP (3389)** och **HTTP** från listrutan.

    ![Öppna portar för RDP och HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

    ![Granska och skapa](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en fjärrskrivbordsanslutning till den virtuella datorn. Dessa instruktioner förklarar hur du ansluter till den virtuella datorn från en Windows-dator. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

1. Klicka på knappen **Anslut** på den virtuella datorns egenskapssida. 

    ![Ansluta till en virtuell Azure-dator från portalen](./media/quick-create-portal/portal-quick-start-9.png)
    
2. På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta med DNS-namn via port 3389 och klickar på **Hämta RDP-filen**.

2. Öppna den hämtade RDP-filen och klicka på **Anslut** när du tillfrågas. 

3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange användarnamnet som **localhost**\\*användarnamn* och ange det lösenord som du skapade för den virtuella datorn. Klicka sedan på **OK**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att skapa anslutningen.

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.


## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

I portalen väljer du den virtuella datorn, och i översikten för den virtuella datorn använder du knappen **Klicka för att kopiera** till höger om IP-adressen för att kopiera och klistra in den i en ny flik i webbläsaren. Standardvälkomstsidan för IIS öppnas och bör se ut så här:

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
