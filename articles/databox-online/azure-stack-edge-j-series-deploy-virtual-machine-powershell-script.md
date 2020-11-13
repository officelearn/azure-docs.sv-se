---
title: Distribuera virtuella datorer på Azure Stack Edge Pro GPU-enhet via Azure PowerShell-skript
description: 'Beskriver hur du skapar och hanterar virtuella datorer (VM: ar) på en Azure Stack Edge Pro-enhet med ett Azure PowerShell-skript.'
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: cd877a91c7c082ce7845394f2636857f33b7940f
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578764"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Distribuera virtuella datorer på Azure Stack Edge Pro GPU-enhet via Azure PowerShell-skript

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

I den här självstudien beskrivs hur du skapar och hanterar en virtuell dator på din Azure Stack Edge Pro-enhet med ett Azure PowerShell-skript.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar skapa och hantera en virtuell dator på din Azure Stack Edge Pro-enhet med det här skriptet måste du kontrol lera att du har slutfört kraven som anges i följande steg:

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>För Azure Stack Edge Pro-enhet via det lokala webb gränssnittet

1. Du har slutfört nätverks inställningarna på din Azure Stack Edge Pro-enhet enligt beskrivningen i [steg 1: konfigurera Azure Stack Edge Pro-enhet](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Aktiverat ett nätverks gränssnitt för beräkning. Nätverks gränssnittets IP-adress används för att skapa en virtuell växel för VM-distributionen. Följande steg vägleder dig genom processen:

    1. Gå till **beräknings inställningarna**. Välj det nätverks gränssnitt som du ska använda för att skapa en virtuell växel.

        > [!IMPORTANT] 
        > Du kan bara konfigurera en port för beräkning.

    2. Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge Pro skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet.

3. Du har skapat och installerat alla certifikat på din Azure Stack Edge Pro-enhet och i det betrodda rot arkivet för din klient. Följ proceduren som beskrivs i [steg 2: skapa och installera certifikat](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

### <a name="for-your-windows-client"></a>För Windows-klienten

1. Du har definierat Azure-konsekventa tjänster för det virtuella Internet protokollet (VIP) på sidan **nätverk** i det lokala webb gränssnittet för enheten. Du måste lägga till denna VIP för att:

    - Värd filen på klienten eller,
    - DNS-serverkonfigurationen
    
    > [!IMPORTANT]
    > Vi rekommenderar att du ändrar DNS-serverkonfigurationen för slut punkts namn matchning.

    1. Starta **anteckningar** som administratör (administratörs behörighet krävs för att spara filen) och öppna sedan **hosts** -filen som finns på `C:\Windows\System32\Drivers\etc` .
    
        ![Windows Explorer Hosts-fil](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Lägg till följande poster i **hosts** -filen och Ersätt med lämpliga värden för din enhet:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        För lagrings kontot kan du ange ett namn som du vill att skriptet ska använda senare för att skapa ett nytt lagrings konto. Skriptet kontrollerar inte om lagrings kontot är befintligt.

    3. Använd följande bild för referens. Spara **värdens** fil.

        ![Hosts-fil i anteckningar](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Hämta PowerShell-skriptet](https://aka.ms/ase-vm-powershell) som används i den här proceduren.

3. Kontrol lera att Windows-klienten kör PowerShell 5,0 eller senare.

4. Kontrol lera att `Azure.Storage Module version 4.5.0` är installerat på datorn. Du kan hämta den här modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0). Om du vill installera den här modulen skriver du:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Verifiera versionen av den installerade modulen genom att skriva:

    `Get-InstalledModule -name Azure.Storage`

    Om du vill avinstallera andra versioner av moduler skriver du:

    `Uninstall-Module -Name Azure.Storage`

5. [Ladda ned AzCopy 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#download-azcopy) till din Windows-klient. Anteckna den här platsen när du skickar den som en parameter när du kör skriptet.

6. Kontrol lera att Windows-klienten kör TLS 1,2 eller senare.


## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Kör PowerShell som administratör.
2. Gå till den mapp där du laddade ned skriptet på klienten.  
3. Använd följande kommando för att köra skriptet:
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    Här följer exempel på när skriptet körs för att skapa en virtuell Windows-dator och en virtuell Linux-dator.

    **För en virtuell Windows-dator:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **För en virtuell Linux-dator:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. Om du vill rensa de resurser som skriptet har skapat använder du följande kommandon:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Nästa steg

[Distribuera virtuella datorer med Azure PowerShell-cmdletar](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)
