---
title: Hantera Virtuella diskar i Azure-stacken | Microsoft Docs
description: Etablera diskar för virtuella datorer i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 314c5b51608192719c77ce143b3530f0bb310bc2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Etablera virtuella disklagring i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Den här artikeln beskriver hur du etablerar lagring för virtuella diskar med hjälp av Azure Stack-portalen eller med hjälp av PowerShell.

## <a name="overview"></a>Översikt

Azure-stacken stöder användning av [ohanterad diskar](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) på virtuella datorer som både ett operativsystem (OS) och en datadisk.

Om du vill använda ohanterade diskar som du skapar en [lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) att lagra diskar. Diskarna som du skapar kallas för Virtuella diskar och lagras i behållare i lagringskontot.

### <a name="best-practice-guidelines"></a>Riktlinjer för bästa praxis

För att förbättra prestandan och minska övergripande kostnader, rekommenderar vi att du placerar varje virtuell disk i en separat behållare. En behållare får innehålla antingen en OS-disk eller en datadisk, men inte båda samtidigt. (Men det finns inget att förhindra att båda typerna av disk i samma behållare.)

Om du lägger till en eller flera datadiskar till en virtuell dator kan du använda ytterligare behållare som en plats för att lagra dessa diskar. OS-disken för ytterligare virtuella datorer bör också vara i sin egen behållare.

När du skapar flera virtuella datorer kan återanvända du samma lagringskonto för varje ny virtuell dator. De behållare som du skapar måste vara unika.

### <a name="adding-new-disks"></a>Lägga till nya diskar

I följande tabell visas hur du lägger till diskar med hjälp av portalen och med hjälp av PowerShell.

| Metod | Alternativ
|-|-|
|[Användarportalen](#use-the-portal-to-add-additional-disks-to-a-vm)|-Lägg till nya datadiskar till en befintlig virtuell dator. Nya diskar skapas av Azure-stacken. </br> </br>-Lägg till en befintlig (VHD)-diskfil till en tidigare etablerade VM. Om du vill göra detta måste du förbereda .vhd och överföra filen till Azure-stacken. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | – Skapa en ny virtuell dator med en OS-disk och lägga till en eller flera datadiskar till den virtuella datorn på samma gång. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Använda portalen för att lägga till diskar till en virtuell dator

Som standard när du använder portalen för att skapa en virtuell dator för de flesta marketplace-objekt skapas endast OS-disken.

När du har skapat en virtuell dator kan du använda portalen för att:
* Skapa en ny datadisk och koppla den till den virtuella datorn.
* Överför en befintlig datadisk och koppla den till den virtuella datorn.

Varje ohanterade disk som du lägger till ska placeras i en separat behållare.

>[!NOTE]
>Diskar skapas och hanteras av Azure kallas [hanterade diskar](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Använda portalen för att skapa och koppla en ny datadisk

1.  I portalen klickar du på **virtuella datorer**.    
    ![Exempel: VM instrumentpanelen](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Välj en virtuell dator som tidigare har etablerats.   
    ![Exempel: Välj en virtuell dator i instrumentpanelen](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Den virtuella datorn, klickar du på **diskar** > **bifoga nya**.       
    ![Exempel: Koppla en ny disk till den virtuella datorn](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  I den **bifoga den nya disken** rutan klickar du på **plats**. Platsen är som standard samma behållare som innehåller OS-disk.      
    ![Exempel: Set diskplatsen](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Välj den **lagringskonto** ska användas. Välj sedan den **behållare** där du vill placera datadisken. Från den **behållare** kan du skapa en ny behållare om du vill. Du kan sedan ändra platsen för den nya disken till sin egen behållare. När du använder en separat behållare för varje disk kan distribuera du placeringen av datadisk som kan förbättra prestanda. Klicka på **Välj** att spara markeringen.     
    ![Exempel: Välj en behållare](media/azure-stack-manage-vm-disks/select-container.png)

6.  I den **bifoga den nya disken** och uppdatera den **namn**, **typen**, **storlek**, och **Värdcachelagring** inställningar på disken. Klicka på **OK** att spara den nya diskkonfigurationen för den virtuella datorn.  
    ![Exempel: Fullständig disk bifogad fil](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  När Azure Stack skapar disken och kopplar den till den virtuella datorn, den nya disken visas i inställningarna för den virtuella disken under **DATADISKAR**.   
    ![Exempel: Visa disk](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Bifoga en befintlig datadisk till en virtuell dator

1.  [Förbereda en VHD-fil](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) för användning som datadisk för en virtuell dator. Överför den VHD-filen till ett lagringskonto som du använder med den virtuella datorn som du vill koppla till VHD-filen.

  Planera att använda en annan behållare för VHD-filen än den behållare som innehåller OS-disk.   
  ![Exempel: Ladda upp en VHD-fil](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Du är redo att koppla den virtuella Hårddisken till en virtuell dator när VHD-filen har överförts. Klicka på menyn till vänster **virtuella datorer**.  
 ![Exempel: Välj en virtuell dator i instrumentpanelen](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Välj den virtuella datorn i listan.    
  ![Exempel: Välj en virtuell dator i instrumentpanelen](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  På sidan för den virtuella datorn **diskar** > **bifoga befintliga**.   
  ![Exempel: Bifoga en befintlig disk](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  I den **bifoga den befintliga disken** klickar du på **VHD-filen**. Den **lagringskonton** öppnas.    
  ![Exempel: Välj en VHD-fil](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  Under **lagringskonton**, Välj kontot som ska användas och väljer sedan en behållare som innehåller VHD-filen som du överfört tidigare. Markera VHD-filen och klicka sedan på **Välj** att spara markeringen.    
  ![Exempel: Välj en behållare](media/azure-stack-manage-vm-disks/select-container2.png)

7.  Under **bifoga den befintliga disken**, den markerade filen finns under **VHD-filen**. Uppdatering av **Värdcachelagring** för disken och klicka sedan på **OK** att spara den nya diskkonfigurationen för den virtuella datorn.    
  ![Exempel: Koppla VHD-filen](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  När Azure Stack skapar disken och kopplar den till den virtuella datorn, den nya disken visas i inställningarna för den virtuella disken under **Datadiskar**.   
  ![Exempel: Slutföra disken bifoga](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Använd PowerShell för att lägga till flera ohanterade diskar till en virtuell dator
Du kan använda PowerShell för att etablera en virtuell dator och lägger till en ny datadisk eller koppla en befintlig **VHD** filen som en datadisk.

Den **Lägg till AzureRmVMDataDisk** cmdlet lägger till en datadisk till en virtuell dator. Du kan lägga till en datadisk när du skapar en virtuell dator eller du kan lägga till en datadisk till en befintlig virtuell dator. Ange den **VhdUri** parametern för att distribuera diskar till olika behållare.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Lägg till diskar i en ny virtuell dator
I följande exempel används PowerShell-kommandon för att skapa en virtuell dator med tre datadiskar, som alla finns i en annan behållare.

Det första kommandot skapar en virtuell dator-objekt och lagrar den i den *$VirtualMachine* variabeln. Kommandot tilldelar ett namn och storleken på den virtuella datorn.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

Följande tre kommandon tilldelar sökvägar för tre datadiskar till den *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, och *$DataDiskVhdUri03* variabler. Definiera en annan sökväg i URL: en för att distribuera diskar till olika behållare.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

De sista tre kommandona lägga till datadiskar till den virtuella datorn lagras i *$VirtualMachine*. Varje kommando anger namn, plats och ytterligare egenskaper för disken. URI för varje disk som är lagrad i *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, och *$DataDiskVhdUri03*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Använd följande PowerShell-kommandon för att lägga till Operativsystemskonfiguration för disk- och den virtuella datorn och starta den nya virtuella datorn.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Lägg till diskar i en befintlig virtuell dator
I följande exempel används PowerShell-kommandon för att lägga till tre datadiskar för i en befintlig virtuell dator.
Det första kommandot hämtar den virtuella datorn med namnet VirtualMachine med hjälp av den **Get-AzureRmVM** cmdlet. Kommandot lagrar den virtuella datorn i den *$VirtualMachine* variabeln.
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
Kommandona tre tilldela variablerna $DataDiskVhdUri01 och DataDiskVhdUri02 $ $DataDiskVhdUri03 sökvägar för tre datadiskar.  Annan sökvägsnamn i vhduri ange olika behållare för disk-placering.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  Följande tre kommandon lägga till datadiskar till den virtuella datorn lagras i den *$VirtualMachine* variabeln. Varje kommando anger namn, plats och ytterligare egenskaper för disken. URI för varje disk som är lagrad i *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, och *$DataDiskVhdUri03*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  Kommandot uppdaterar tillståndet för den virtuella datorn lagras i *$VirtualMachine* i -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure-stacken virtuella datorer i [överväganden för virtuella datorer i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
