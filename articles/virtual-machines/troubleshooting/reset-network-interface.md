---
title: Så här återställer du nätverksgränssnitt för virtuella Azure Windows-datorer | Microsoft Docs
description: Visar hur du återställer nätverksgränssnitt för virtuella Azure Windows-datorer
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 3a8e005f8678deef9fc4aebd2d620619fe6074bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307331"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Så här återställer du nätverksgränssnitt för virtuella Azure Windows-datorer 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Den här artikeln visar hur du återställer nätverksgränssnitt för virtuella Azure Windows-datorer att lösa problem när du inte kan ansluta till Microsoft Azure Windows Virtual Machine (VM) efter:

* Du inaktiverar Standardnätverksgränssnittet (nätverkskort). 
* Du ange manuellt en statisk IP-adress för nätverkskortet. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Återställ nätverksgränssnittet

### <a name="for-vms-deployed-in-resource-group-model"></a>För virtuella datorer som distribueras i Resource group-modellen

1.  Gå till [Azure-portalen](https://ms.portal.azure.com).
2.  Välj den berörda virtuella datorn.
3.  Välj **nätverk** och välj sedan nätverksgränssnittet för den virtuella datorn.

    ![Gränssnittet nätverksplats](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Välj **IP-konfigurationer**.
5.  Välj IP-Adressen. 
6.  Om den **privat IP-tilldelning** är inte **statiska**, ändra den till **statiska**.
7.  Ändra den **IP-adress** till en annan IP-adress som är tillgänglig i undernätet.
8. Den virtuella datorn startas om för att initiera det nya nätverkskortet i systemet.
9.  Försök att RDP till din dator. Om detta lyckas kan du ändra privat IP-adress tillbaka till ursprungligt om du vill ha. I annat fall kan du behålla den. 

#### <a name="use-azure-powershell"></a>Använda Azure PowerShell

1. Se till att du har [den senaste Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) installerad
2. Öppna en upphöjd Azure PowerShell-session (Kör som administratör). Kör följande kommandon:

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
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Försök att RDP till din dator.  Om detta lyckas kan du ändra privat IP-adress tillbaka till ursprungligt om du vill ha. I annat fall kan du behålla den.

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

Följ dessa steg om du vill återställa nätverksgränssnitt:

#### <a name="use-azure-portal"></a>Använda Azure-portalen

1.  Gå till [Azure-portalen]( https://ms.portal.azure.com).
2.  Välj **virtuella datorer (klassiska)**.
3.  Välj den berörda virtuella datorn.
4.  Välj **IP-adresser**.
5.  Om den **privat IP-tilldelning** är inte **statiska**, ändra den till **statiska**.
6.  Ändra den **IP-adress** till en annan IP-adress som är tillgänglig i undernätet.
7.  Välj **Spara**.
8.  Den virtuella datorn startas om för att initiera det nya nätverkskortet i systemet.
9.  Försök att RDP till din dator. Om detta lyckas kan du återställa den privata IP-adressen till ursprungligt.  

#### <a name="use-azure-powershell"></a>Använda Azure PowerShell

1. Se till att du har [den senaste Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) installerad.
2. Öppna en upphöjd Azure PowerShell-session (Kör som administratör). Kör följande kommandon:

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
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Försök att RDP till din dator. Om detta lyckas kan du ändra privat IP-adress tillbaka till ursprungligt om du vill ha. I annat fall kan du behålla den. 

## <a name="delete-the-unavailable-nics"></a>Ta bort de tillgängliga nätverkskorten
När du kan fjärrskrivbord till datorn måste du ta bort de gamla nätverkskorten för att undvika potentiella problem:

1.  Öppna Enhetshanteraren.
2.  Välj **visa** > **Visa dolda enheter**.
3.  Välj **nätverkskort**. 
4.  Kontrollera om finns nätverkskort med namnet ”Microsoft Hyper-V-nätverkskort”.
5.  Du kan se ett otillgängligt nätverkskort som är nedtonat. Högerklicka på nätverkskortet och välj sedan avinstallera.

    ![Bild av nätverkskortet](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Endast avinstallera de inte tillgängliga-kort som har namnet ”Microsoft Hyper-V-nätverkskort”. Om du avinstallerar någon av de andra dolt kort, kan det orsaka ytterligare problem.
    >
    >

6.  Alla otillgänglig kort ska nu vara avmarkerad i systemet.
