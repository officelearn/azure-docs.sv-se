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
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c28686c3b6494a0cf8938d39ab9b8338de7aa0c1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Windows-dator i Azure Portal

Det går att skapa virtuella datorer (VM) via Azure Portal. Med den här metoden får du ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. Den här snabbstarten beskriver hur du använder Azure Portal för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2016. För att sedan se hur den fungerar i praktiken ansluter du till den virtuella datorn med RDP och installerar IIS-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.

2. I sökrutan ovanför listan över resurser i Azure Marketplace, söker di efter och väljer **Windows Server 2016 Datacenter** och därefter **Dkapa**.

3. Ange ett namn, t.ex *myVM*, lämna disktypen som *SSD* och ange ett användarnamn, t.ex *azureuser*. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)

5. Välja att **Skapa ny** resursgrupp och ange sedan ett namn, till exempel *myResourceGroup*. Välj din önskade **Plats** och välj **OK**.

4. Välj en storlek för den virtuella datorn. Du kan till exempel filtrera efter *Beräkningstyp* eller *Disktyp*. Ett förslag på storlek på en virtuell dator är *D2s_v3*.

    ![Skärmbild som visar storlekar på virtuella datorer](./media/quick-create-portal/create-windows-vm-portal-sizes.png)

5. Lämna standardinställningarna under **Inställningar** och välj **OK**.

6. På sammanfattningssidan klickar du på **Skapa** för att starta distributionen av den virtuella datorn.

7. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för den virtuella datorn automatiskt.

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en fjärrskrivbordsanslutning till den virtuella datorn. Dessa instruktioner förklarar hur du ansluter till den virtuella datorn från en Windows-dator. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

1. Klicka på knappen **Anslut** på den virtuella datorns egenskapssida. 

    ![Ansluta till en virtuell Azure-dator från portalen](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png)
    
2. På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta med DNS-namn via port 3389 och klickar på **Hämta RDP-filen**.

2. Öppna den hämtade RDP-filen och klicka på **Anslut** när du tillfrågas. 

3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange *vmname*\* som användarnamn* och det lösenord som du skapade för den virtuella datorn och klicka sedan på **OK**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

En nätverkssäkerhetsgrupp (NSG) säkrar ingående och utgående trafik. När en VM skapas från Azure Portal skapas en regel för inkommande trafik på port 3389 för RDP-anslutningar. Eftersom denna VM är värd för en webbserver måste du skapa en NSG-regel för port 80.

1. Välj **Nätverk** på översiktssidan för den virtuella datorn.
2. Listan med befintliga regler för inkommande och utgående visas. Välj att **Lägg till regel för inkommande portar**.
3. Välj alternativet **Grundläggande** överst och välj sedan *HTTP* från listan över tillgängliga tjänster. Du tilldelas port 80, en prioritet och ett namn.
4. Välj **Lägg till** för att skapa regeln.

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

När IIS har installerats och port 80 är öppen på den virtuella datorn från Internet använder du valfri webbläsare för att visa standardvälkomstsidan för IIS. Använd den virtuella datorns offentliga IP-adress, som du hämtade i ett tidigare steg. I följande exempel visas standardwebbplatsen för IIS:

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
