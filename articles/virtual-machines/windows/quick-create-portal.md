---
title: Snabbstart – Skapa en virtuell Windows-dator i Azure-portalen
description: I den här snabbstarten lär du dig hur du skapar en virtuell Windows-dator med hjälp av Azure Portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a304e400d193eb4b2d0c2e8ec30ea03ea41977f8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606644"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Windows-dator i Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Med den här metoden får du ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. Den här snabbstarten visar hur du använder Azure-portalen för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2019. För att se hur den virtuella datorn fungerar i praktiken ansluter du till den med RDP och installerar IIS-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Skriv **virtuella datorer** i sökningen.
1. Under **Tjänster**väljer du **Virtuella datorer**.
1. På sidan **Virtuella datorer** väljer du **Lägg till**. 
1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myResourceGroup* som namn. 

    ![Skapa en ny resursgrupp för din virtuella dator](./media/quick-create-portal/project-details.png)

1. Under **Instansinformation**skriver du *myVM* för namnet på den **virtuella datorn** och väljer Östra *USA* för **din region**och väljer sedan Windows Server *2019 Datacenter* för **avbildningen**. Låt de övriga standardvärdena vara som de är.

    ![Avsnittet Instansinformation](./media/quick-create-portal/instance-details.png)

1. Under **Administratörskonto**, anger du ett användarnamn, som *azureuser*, och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Ange ditt användarnamn och lösenord](./media/quick-create-portal/administrator-account.png)

1. Under **Regler för inkommande port**väljer du Tillåt valda **portar** och väljer sedan **RDP (3389)** och **HTTP (80)** i listrutan.

    ![Öppna portar för RDP och HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

    ![Granska och skapa](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en fjärrskrivbordsanslutning till den virtuella datorn. Dessa instruktioner förklarar hur du ansluter till den virtuella datorn från en Windows-dator. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) från Mac App Store.

1. Klicka på knappen **Anslut** på översiktssidan för den virtuella datorn. 

    ![Ansluta till en virtuell Azure-dator från portalen](./media/quick-create-portal/portal-quick-start-9.png)
    
2. På sidan **Anslut till virtuell dator** behåller du standardalternativen för att ansluta via IP-adress, över port 3389 och klickar på Hämta **RDP-fil**.

2. Öppna den hämtade RDP-filen och klicka på **Anslut** när du tillfrågas. 

3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Skriv användarnamnet som **localhost**\\*användarnamn,* ange lösenord som du skapade för den virtuella datorn och klicka sedan på **OK**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att skapa anslutningen.

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.


## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

I portalen väljer du den virtuella datorn och i översikten över den virtuella datorn använder du knappen **Klicka för att kopiera till** höger om IP-adressen för att kopiera den och klistra in den i en webbläsarflik. Standard-IIS-välkomstsidan öppnas och bör se ut så här:

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. 

Välj resursgruppen för den virtuella datorn och välj sedan **Ta bort**. Bekräfta namnet på resursgruppen för att slutföra borttagningen av resurserna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)