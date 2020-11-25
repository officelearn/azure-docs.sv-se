---
title: Så här återställer du nätverks gränssnittet för Azure Windows VM | Microsoft Docs
description: Visar hur du återställer nätverks gränssnittet för Azure Windows VM
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 6c4e507df0f112934979d4e59778b667743cf623
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022912"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Återställa nätverksgränssnittet för en virtuell Azure Windows-dator 

Den här artikeln visar hur du återställer nätverks gränssnittet för Azure Windows VM för att lösa problem när du inte kan ansluta till Microsoft Azure virtuell Windows-dator (VM) efter:

* Du inaktiverar standard nätverks gränssnittet (NIC). 
* Du anger en statisk IP-adress för NÄTVERKSKORTet manuellt. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Återställ nätverks gränssnitt

### <a name="for-vms-deployed-in-resource-group-model"></a>För virtuella datorer som distribueras i resurs grupps modellen

1.  Gå till [Azure-portalen](https://ms.portal.azure.com).
2.  Välj den berörda virtuella datorn.
3.  Välj **nätverk** och välj sedan nätverks gränssnittet för den virtuella datorn.

    ![Nätverks gränssnitts plats](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Välj **IP-konfigurationer**.
5.  Välj IP. 
6.  Om **tilldelningen för den privata IP-adressen**  inte är  **statisk**, ändra den till **statisk**.
7.  Ändra **IP-adressen** till en annan IP-adress som är tillgänglig i under nätet.
8. Den virtuella datorn startas om för att initiera det nya NÄTVERKSKORTet i systemet.
9.  Försök att använda RDP till din dator. Om det lyckas kan du ändra den privata IP-adressen tillbaka till den ursprungliga om du vill. Annars kan du behålla det. 

#### <a name="use-azure-powershell"></a>Använda Azure PowerShell

1. Kontrol lera att du har [de senaste Azure PowerShell](/powershell/azure/) installerade
2. Öppna en förhöjd Azure PowerShell-session (kör som administratör). Kör följande kommandon:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Försök att använda RDP till din dator.  Om det lyckas kan du ändra den privata IP-adressen tillbaka till den ursprungliga om du vill. Annars kan du behålla det.

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Följ dessa steg om du vill återställa nätverks gränssnittet:

#### <a name="use-azure-portal"></a>Använda Azure-portalen

1.  Gå till [Azure-portalen]( https://ms.portal.azure.com).
2.  Välj **Virtual Machines (klassisk)**.
3.  Välj den berörda virtuella datorn.
4.  Välj **IP-adresser**.
5.  Om **tilldelningen för den privata IP-adressen**  inte är  **statisk**, ändra den till **statisk**.
6.  Ändra **IP-adressen** till en annan IP-adress som är tillgänglig i under nätet.
7.  Välj **Spara**.
8.  Den virtuella datorn startas om för att initiera det nya NÄTVERKSKORTet i systemet.
9.  Försök att använda RDP till din dator. Om det lyckas kan du välja att återställa den privata IP-adressen till den ursprungliga.  

#### <a name="use-azure-powershell"></a>Använda Azure PowerShell

1. Kontrol lera att du har [de senaste Azure PowerShell](/powershell/azure/) installerade.
2. Öppna en förhöjd Azure PowerShell-session (kör som administratör). Kör följande kommandon:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Försök att använda RDP till din dator. Om det lyckas kan du ändra den privata IP-adressen tillbaka till den ursprungliga om du vill. Annars kan du behålla det. 

## <a name="delete-the-unavailable-nics"></a>Ta bort otillgängliga nätverkskort
När du har fjärr skrivbordet till datorn måste du ta bort de gamla nätverkskorten för att undvika det potentiella problemet:

1.  Öppna Enhetshanteraren.
2.  Välj **Visa**  >  **Visa dolda enheter**.
3.  Välj **nätverkskort**. 
4.  Sök efter de nätverkskort som heter "Microsoft Hyper-V nätverkskort".
5.  Du kan se ett otillgängligt kort som är nedtonat. Högerklicka på kortet och välj sedan Avinstallera.

    ![NÄTVERKSKORTets avbildning](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Avinstallera bara otillgängliga nätverkskort som har namnet "Microsoft Hyper-V nätverkskort". Om du avinstallerar något av de andra dolda korten kan det orsaka ytterligare problem.
    >
    >

6.  Nu bör alla otillgängliga kort tas bort från systemet.
