---
title: Snabb start – skapa en virtuell Windows-dator med Azure PowerShell
description: I den här snabbstarten lär du dig hur du använder Azure PowerShell för att skapa en virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 607ad17af42b88737d8ca4ebc379504d9b0b40df
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073442"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Snabbstart: Skapa en virtuell Windows-dator i Azure med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2016. Du kommer även att RDP-ansluta till den virtuella datorn och installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Ange namnet på varje resurs så skapas de av cmdleten `New-AzVM` om de inte redan finns.

Vid uppmaningen anger du ett användarnamn och lösenord som ska användas som autentiseringsuppgifter för inloggning på den virtuella datorn:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen är klar ansluter du till den virtuella datorn med RDP. För att sedan se hur den virtuella datorn fungerar i praktiken installerar du IIS-webbservern.

Du hämtar den virtuella datorns offentliga IP-adress genom att köra cmdleten [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress):

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Skapa en fjärrskrivbordssession från den lokala datorn genom att köra följande kommando. Ersätt IP-adressen med den virtuella datorns offentliga IP-adress. 

```powershell
mstsc /v:publicIpAddress
```

I fönstret **Windows-säkerhet** väljer du **fler alternativ** och väljer sedan **Använd ett annat konto**. Ange användarnamnet som **localhost**\\*användarnamn* och ange det lösenord som du skapade för den virtuella datorn. Klicka sedan på **OK**.

Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att skapa anslutningen

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.

## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

När IIS har installerats och port 80 är öppen på den virtuella datorn från Internet använder du valfri webbläsare för att visa standardvälkomstsidan för IIS. Använd den virtuella datorns offentliga IP-adress, som du hämtade i ett tidigare steg. I följande exempel visas standardwebbplatsen för IIS:

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
