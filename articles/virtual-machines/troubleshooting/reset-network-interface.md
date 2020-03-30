---
title: Återställa nätverksgränssnittet för Azure Windows VM| Microsoft-dokument
description: Visar hur du återställer nätverksgränssnittet för Azure Windows VM
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
ms.openlocfilehash: a8bd12d98b76d5848753987c4f7bcb76d4e2266d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250080"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Återställa nätverksgränssnittet för en virtuell Azure Windows-dator 

Den hÃ¤r artikeln visar fÃ¶r att ã¥tsätta nÃ¤tverksgränssnittet fÃ¶r Azure Windows VM fÃ¶r att lÃ¶sa problem nÃ¤r du inte kan ansluta till Microsoft Azure Windows Virtual Machine (VM) efter:

* Du inaktiverar standardnätverksgränssnittet (NIC). 
* Du ställer in en statisk IP för nätverkskortet manuellt. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Återställ nätverksgränssnittet

### <a name="for-vms-deployed-in-resource-group-model"></a>För virtuella datorer som distribueras i resursgruppsmodellen

1.  Gå till [Azure-portalen](https://ms.portal.azure.com).
2.  Välj den berörda virtuella datorn.
3.  Välj **Nätverk** och välj sedan nätverksgränssnittet för den virtuella datorn.

    ![Plats för nätverksgränssnitt](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Välj **IP-konfigurationer**.
5.  Välj IP. 
6.  Om den **privata IP-tilldelningen** inte är **statisk**ändrar du den till **Statisk**.
7.  Ändra **IP-adressen** till en annan IP-adress som är tillgänglig i undernätet.
8. Den virtuella datorn startar om för att initiera det nya nätverkskortet till systemet.
9.  Försök rdp till din maskin. Om det lyckas kan du ändra tillbaka den privata IP-adressen till originalet om du vill. Annars kan du behålla den. 

#### <a name="use-azure-powershell"></a>Använda Azure PowerShell

1. Kontrollera att du har [den senaste Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) installerad
2. Öppna en förhöjd Azure PowerShell-session (Kör som administratör). Kör följande kommandon:

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
3. Försök rdp till din maskin.  Om det lyckas kan du ändra tillbaka den privata IP-adressen till originalet om du vill. Annars kan du behålla den.

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Så här återställer du nätverksgränssnittet:

#### <a name="use-azure-portal"></a>Använda Azure-portalen

1.  Gå till [Azure-portalen]( https://ms.portal.azure.com).
2.  Välj **virtuella datorer (klassiskt)**.
3.  Välj den berörda virtuella datorn.
4.  Välj **IP-adresser**.
5.  Om den **privata IP-tilldelningen** inte är **statisk**ändrar du den till **Statisk**.
6.  Ändra **IP-adressen** till en annan IP-adress som är tillgänglig i undernätet.
7.  Välj **Spara**.
8.  Den virtuella datorn startar om för att initiera det nya nätverkskortet till systemet.
9.  Försök rdp till din maskin. Om det lyckas kan du välja att återställa den privata IP-adressen tillbaka till originalet.  

#### <a name="use-azure-powershell"></a>Använda Azure PowerShell

1. Kontrollera att du har [den senaste Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) installerad.
2. Öppna en förhöjd Azure PowerShell-session (Kör som administratör). Kör följande kommandon:

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
3. Försök rdp till din maskin. Om det lyckas kan du ändra tillbaka den privata IP-adressen till originalet om du vill. Annars kan du behålla den. 

## <a name="delete-the-unavailable-nics"></a>Ta bort de nätverkskort som inte är tillgängliga
När du kan fjärrskrivbord till datorn måste du ta bort de gamla nätverkskorten för att undvika det potentiella problemet:

1.  Öppna Enhetshanteraren.
2.  Välj **Visa** > **Visa dolda enheter**.
3.  Välj **Nätverkskort**. 
4.  Sök efter korten som heter "Microsoft Hyper-V-nätverkskort".
5.  Du kan se ett kort som inte är tillgängligt som är nedtonat. Högerklicka på kortet och välj sedan Avinstallera.

    ![bilden av nätverkskortet](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Avinstallera endast de nätverkskort som inte är tillgängliga och som har namnet "Microsoft Hyper-V-nätverkskort". Om du avinstallerar något av de andra dolda korten kan det orsaka ytterligare problem.
    >
    >

6.  Nu bör alla otillgängliga adaptrar rensas från ditt system.
