---
title: "Skapa en virtuell Azure-dator med snabbare nätverk | Microsoft Docs"
description: "Lär dig hur du skapar en virtuell dator med snabbare nätverk."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e887230a102f5c6289ca2eec0e4700a0e1fdfde
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Skapa en virtuell dator med snabbare nätverk

Lär dig hur du skapar ett Azure Virtual Machine (VM) med snabbare nätverk i den här självstudiekursen. Snabbare nätverksfunktioner är GA för Windows och en offentlig förhandsgranskning för specifika Linux-distributioner. Snabbare nätverksfunktioner kan single-root I/O virtualization (SR-IOV) till en virtuell dator, vilket avsevärt minskar tiden dess nätverksprestanda. Den här sökvägen för högpresterande kringgår värden från datapath minskar latens och jitter CPU-belastningen för användning med de mest krävande nätverksbelastning på VM-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer (VM) med och utan snabbare nätverksfunktioner:

![Jämförelse](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Utan snabbare nätverk måste alla nätverkstrafiken till och från den virtuella datorn passerar värden och den virtuella växeln. Den virtuella växeln tillhandahåller alla tvingande, till exempel nätverkssäkerhetsgrupper, åtkomstkontrollistor, isolering och andra virtualiserade nätverkstjänster för nätverkstrafik. Mer information om virtuella växlar på [Hyper-V-nätverksvirtualisering och den virtuella växeln](https://technet.microsoft.com/library/jj945275.aspx) artikel.

Med snabbare nätverksfunktioner trafik anländer till den Virtuella datorns nätverksgränssnitt (NIC) och sedan vidarebefordras till den virtuella datorn. Alla principer för nätverk som den virtuella växeln gäller utan snabbare nätverk avläst och tillämpas i maskinvara. Tillämpa principen i den maskinvara som gör det möjligt för nätverkskort för att vidarebefordra trafik direkt till den virtuella datorn, vilket kringgår värden och den virtuella växeln samtidigt som den principen tillämpades på värden.

Fördelarna med snabbare nätverksfunktioner gäller endast för den virtuella datorn som den är aktiverad på. Det är perfekt att aktivera funktionen på minst två virtuella datorer anslutna till samma Azure virtuella nätverk (VNet) för bästa resultat. Vid kommunikation över Vnet eller anslutande lokalt har funktionen minimal inverkan på övergripande svarstiden.

> [!WARNING]
> Den här Linux förhandsversion får inte ha samma nivå av tillgänglighet och tillförlitlighet som viktiga funktioner som är i allmänhet tillgänglighet. Funktionen stöds inte, kan ha begränsad kapacitet och kanske inte tillgänglig på alla platser i Azure. Kontrollera sidan Azure Virtual Network uppdateringar för de senaste meddelanden på tillgänglighet och status för den här funktionen.

## <a name="benefits"></a>Fördelar
* **Lägre latens / högre paket per sekund (pps):** att ta bort den virtuella växeln från datapath tar bort paket tid i värden för behandling av princip för och ökar antalet paket som kan bearbetas inuti den virtuella datorn.
* **Minskar jitter:** virtuella växeln bearbetning beror på mängden principinformation som måste tillämpas och arbetsbelastningen processorkraft som utför bearbetning. Avlastning tvingande principer till maskinvaran som tar bort den variationen genom att leverera paket direkt till den virtuella datorn, ta bort värden till programvara avbrott och kontext växlar för VM-kommunikation och alla.
* **Minskas CPU-användning:** kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

## <a name="Limitations"></a>Begränsningar
Följande begränsningar gäller när du använder den här funktionen:

* **Network interface skapa:** Accelerated nätverk kan bara aktiveras för en ny nätverkskort. Det går inte att aktivera för en befintlig nätverkskort.
* **Skapa en virtuell dator:** A nätverkskortet med snabbare nätverksfunktioner som är aktiverad kan endast kopplas till en virtuell dator när den virtuella datorn skapas. Nätverkskortet kan inte kopplas till en befintlig virtuell dator.
* **Regioner:** virtuella Windows-datorer med snabbare nätverksfunktioner erbjuds i de flesta Azure-regioner. Linux virtuella datorer med snabbare nätverksfunktioner erbjuds i flera områden. Regioner som den här funktionen finns i expanderar. Se Azure virtuella nätverk uppdaterar blogg under den senaste informationen.   
* **Operativsystem som stöds:** Windows: Microsoft Windows Server 2012 R2 Datacenter och Windows Server 2016. Linux: Ubuntu Server 16.04 LTS med kernel 4.4.0-77 eller högre, SLES 12 SP2, RHEL 7.3 och CentOS 7.3 (publicerad av ”falsk Wave programvara”).
* **VM-storlek:** generella och beräknings-optimerad instans storlekar med minst åtta kärnor. Mer information finns i [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar artiklar. Uppsättningen stöds storlekar på VM-instansen kommer att expandera i framtiden.
* **Distribution via Azure Resource Manager (ARM):** snabbare nätverk är inte tillgänglig för distribution via ASM/RDFE.

Ändringar av dessa begränsningar meddelas via den [virtuella Azure-nätverk uppdaterar](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview/) sidan.

## <a name="create-a-windows-vm"></a>Skapa en virtuell Windows-dator
Du kan använda Azure-portalen eller Azure [PowerShell](#windows-powershell) att skapa den virtuella datorn.

### <a name="windows-portal"></a>Portal

1. Öppna en webbläsare på Azure [portal](https://portal.azure.com) och logga in med ditt Azure [konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p).
2. I portalen klickar du på **+ ny** > **Compute** > **Windows Server 2016 Datacenter**. 
3. I den **Windows Server 2016 Datacenter** bladet som visas, lämna *Resource Manager* valda under **Välj en distributionsmodell**, och klicka på **skapa**.
4. I den **grunderna** bladet som visas, ange följande värden låter återstående standardalternativen eller Välj eller ange egna värden och klicka på den **OK** knappen:

    |Inställning|Värde|
    |---|---|
    |Namn|MyVm|
    |Resursgrupp|Lämna **Skapa nytt** markerad och ange *MyResourceGroup*|
    |Plats|Västra USA 2|

    Om du har använt Azure lär du dig mer om [resursgrupper](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), och [platser](https://azure.microsoft.com/regions) (som även kallas regioner).
5. I den **välja en storlek** bladet som visas, ange *8* i den **minsta kärnor** rutan och klicka sedan på **visa alla**.
6. Klicka på **DS4_V2 Standard**, eller någon stöds VM, klicka på den **Välj** knappen.
7. I den **inställningar** bladet som visas, lämnar du alla inställningar som-är undantag för **aktiverad** under **snabbare nätverk**, klicka på den **OK** knappen. **Obs:** om, i föregående steg du har valt värden för VM-storlek, operativsystem eller plats som inte finns med i den [begränsningar](#Limitations) i den här artikeln **snabbare nätverk** inte visas.
8. I den **sammanfattning** bladet som visas, klicka på den **OK** knappen. Azure börjar skapa den virtuella datorn. Skapa en virtuell dator tar några minuter.
9. Om du vill installera snabbare nätverksdrivrutin för Windows att slutföra stegen i den [konfigurera Windows](#configure-windows) i den här artikeln.

### <a name="windows-powershell"></a>PowerShell
1. Installera den senaste versionen av Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modul. Om du har använt Azure PowerShell, läsa den [översikt över Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel.
2. Starta en PowerShell-session genom att klicka på Start, skriva **powershell**, klicka på **PowerShell** i sökresultatet.
3. I PowerShell-fönstret anger du den `login-azurermaccount` kommando för att logga in med ditt Azure [konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Kopiera följande skript i webbläsaren:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. Högerklicka på om du vill klistra in skript och starta körs i PowerShell-fönstret. Du tillfrågas om användarnamn och lösenord. Använd dessa autentiseringsuppgifter för att logga in på den virtuella datorn när du ansluter till den i nästa steg. Om skriptet misslyckas och du har ändrat värden i skriptet innan du kör den, bekräftar du de värden som du använde för VM-storlek, operativsystem, och platsen visas i den [begränsningar](#Limitations) i den här artikeln.
6. Om du vill installera snabbare nätverksdrivrutin för Windows att slutföra stegen i den [konfigurera Windows](#configure-windows) i den här artikeln.

### <a name="configure-windows"></a>Konfigurera Windows
När du har skapat den virtuella datorn i Azure måste du installera snabbare nätverksdrivrutin för Windows. Innan du utför följande steg måste du skapa en virtuell Windows-dator med snabbare nätverk med hjälp av antingen den [portal](#windows-portal) eller [PowerShell](#windows-powershell) steg i den här artikeln. 

1. Öppna en webbläsare på Azure [portal](https://portal.azure.com) och logga in med ditt Azure [konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p).
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *MyVm*. När **MyVm** visas i sökresultaten klickar du på den.
3. I den **MyVm** bladet som visas, klicka på den **Anslut** knappen i det övre vänstra hörnet på bladet. **Obs:** om **skapa** visas under den **Anslut** knappen Azure inte ännu har skapar den virtuella datorn. Klicka på **Anslut** efter att du inte längre se **skapa** under den **Anslut** knappen.
4. Tillåt webbläsaren att ladda ned den **MyVm.rdp** fil.  När du har hämtat, klickar du på filen för att öppna den. 
5. Klicka på den **Anslut** knappen i den **anslutning till fjärrskrivbord** som visas får ett meddelande om att utgivaren av fjärranslutningen inte kan identifieras.
6. I den **Windows-säkerhet** som visas, klicka på **fler alternativ**, klicka på **Använd ett annat konto**. Ange användarnamnet och lösenordet du angav i steg 4 och klicka sedan på den **OK** knappen.
7. Klicka på den **Ja** i rutan anslutning till fjärrskrivbord som visas, får ett meddelande om att identiteten för fjärrdatorn inte kan verifieras.
8. Högerklicka på Start och klicka på **Enhetshanteraren**. Expandera den **nätverkskort** nod. Bekräfta att den **Mellanox ConnectX 3 virtuella funktionen Ethernet-nätverkskort** visas som i följande bild:
   
    ![Enhetshanteraren](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. Snabbare nätverksfunktioner har nu aktiverats för den virtuella datorn.

## <a name="create-a-linux-vm"></a>Skapa en virtuell Linux-dator
Du kan använda Azure-portalen eller Azure [PowerShell](#linux-powershell) att skapa en Ubuntu eller SLES VM. Det finns ett annat arbetsflöde RHEL och CentOS virtuella datorer.  Se anvisningarna nedan.

### <a name="linux-portal"></a>Portal
1. Registrera dig för snabbare nätverksfunktioner för Linux preview genom att fylla i steg 1-5 i den [och skapar en Linux VM - PowerShell](#linux-powershell) i den här artikeln.  Du kan inte registrera dig för förhandsgranskning i portalen.
2. Slutför steg 1 – 8 i den [skapa en virtuell dator i Windows - portal](#windows-portal) i den här artikeln. I steg 2, klickar du på **Ubuntu Server 16.04 LTS** i stället för **Windows Server 2016 Datacenter**. Välj att använda ett lösenord i stället för en SSH-nyckel om för Produktionsdistribution, du kan använda antingen för den här självstudiekursen. Om **snabbare nätverk** visas inte när du har slutfört steg 7 i den [skapa en virtuell dator i Windows - portal](#windows-portal) avsnitt i den här artikeln sannolikt av något av följande skäl:
    - Du ännu inte har registrerats för förhandsgranskningen. Bekräfta att din registreringstillstånd är **registrerade**, enligt beskrivningen i steg 4 i den [och skapar en Linux VM - Powershell](#linux-powershell) i den här artikeln. **Obs:** om du har deltagit i Accelerated nätverk för virtuella Windows-datorer preview (det är inte längre nödvändigt att registrera om du vill använda Accelerated nätverk för Windows VMs) du inte är automatiskt registrerad för Accelerated nätverk för virtuella Linux-datorer Förhandsgranska. Du måste registrera dig för Accelerated nätverk för virtuella Linux-datorer Förhandsgranska för att delta i den.
    - Du har inte valt VM-storlek, operativsystem, eller plats som anges i den [begränsningar](#limitations) i den här artikeln.
3. Om du vill installera snabbare nätverksdrivrutin för Linux slutföra stegen i den [konfigurera Linux](#configure-linux) i den här artikeln.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Om du skapar virtuella Linux-datorer med snabbare nätverk i en prenumeration och försök sedan att skapa en virtuell Windows-dator med snabbare nätverksfunktioner i samma prenumeration, misslyckas skapa Windows VM. Den här förhandsversionen rekommenderas att du testar Linux och Windows-datorer med snabbare nätverk i separata prenumerationer.
>

1. Installera den senaste versionen av Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modul. Om du har använt Azure PowerShell, läsa den [översikt över Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel.
2. Starta en PowerShell-session genom att klicka på Start, skriva **powershell**, klicka på **PowerShell** i sökresultatet.
3. I PowerShell-fönstret anger du den `login-azurermaccount` kommando för att logga in med ditt Azure [konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Registrera dig för snabbare nätverksfunktioner för Azure preview genom att utföra följande steg:
    - Skicka ett e-postmeddelande till [ axnpreview@microsoft.com ](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) med Azure prenumerations-ID och avsedd att användas. Vänta tills en e-postbekräftelse från Microsoft om prenumerationen har aktiverats.
    - Ange följande kommando för att bekräfta att du är registrerad för förhandsversionen av:
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        Fortsätt inte med steg 5 tills **registrerade** visas i utdata när du har angett det föregående kommandot. Din utdata måste ser ut som följande utdata innan du fortsätter:
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Om du har deltagit i Accelerated nätverk för virtuella Windows-datorer preview (det är inte längre nödvändigt att registrera om du vill använda Accelerated nätverk för virtuella Windows-datorer) är du inte automatiskt registrerad för Accelerated nätverk för virtuella Linux-datorer Förhandsgranska. Du måste registrera dig för Accelerated nätverk för virtuella Linux-datorer Förhandsgranska för att delta i den.
      >
5. Kopiera följande skript ersätter Ubuntu eller SLES efter behov i webbläsaren.  Igen, Redhat och CentOS har ett annat arbetsflöde som beskrivs nedan:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. Högerklicka på om du vill klistra in skript och starta körs i PowerShell-fönstret. Du tillfrågas om användarnamn och lösenord. Använd dessa autentiseringsuppgifter för att logga in på den virtuella datorn när du ansluter till den i nästa steg. Om skriptet misslyckas, bekräftar du att:
    - Du är registrerad för förhandsversionen av enligt beskrivningen i steg 4
    - Om du har ändrat VM-storlek, typ av operativsystem eller plats värden i skriptet innan den körs, att värden visas i den [begränsningar](#Limitations) i den här artikeln.
7. Om du vill installera snabbare nätverksdrivrutin för Linux slutföra stegen i den [konfigurera Linux](#configure-linux) i den här artikeln.

### <a name="configure-linux"></a>Konfigurera Linux

När du har skapat den virtuella datorn i Azure måste du installera snabbare nätverksdrivrutin för Linux. Innan du utför följande steg måste du skapa en Linux VM snabbare nätverk med hjälp av antingen den [portal](#linux-portal) eller [PowerShell](#linux-powershell) steg i den här artikeln. 

1. Öppna en webbläsare på Azure [portal](https://portal.azure.com) och logga in med ditt Azure [konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Överst i portalen till höger om den *söka resurser* klickar du på den **> _** ikon för att starta ett Bash molnet gränssnitt (förhandsversion). Fönstret Bash molnet shell visas längst ned i portalen efter några sekunder visas en  **username@Azure:~ $** prompt. Om du kan SSH till den virtuella datorn från datorn i stället för molnet shell förutsätter instruktioner i den här självstudiekursen att du använder molntjänster shell.
3. Överst i portalen, i rutan som innehåller texten *söka resurser*, typen *MyVm*. När **MyVm** visas i sökresultaten klickar du på den.
4. I den **MyVm** bladet som visas, klicka på den **Anslut** knappen i det övre vänstra hörnet på bladet. **Obs:** om **skapa** visas under den **Anslut** knappen Azure inte ännu har skapar den virtuella datorn. Klicka på **Anslut** efter att du inte längre se **skapa** under den **Anslut** knappen.
5. Azure öppnar en uppmaning att ange den `ssh adminuser@<ipaddress>`. Ange det här kommandot i molnet shell (eller kopiera den från rutan som fanns i steg 4 och klistra in den i molnet Shell), och tryck sedan på RETUR.
6. Ange **Ja** på frågan tillfrågas du om du vill fortsätta ansluta, tryck på RETUR.
7. Ange lösenordet du angav när du skapar den virtuella datorn. En gång loggat in till den virtuella datorn, visas en adminuser@MyVm:~ $ prompten. Du är nu inloggad till den virtuella datorn via molnet shell-sessionen. **Obs:** moln shell sessioner timeout efter 10 minuter av inaktivitet.

Nu variera instruktionerna beroende på den distributionsplats som du använder. 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. I Kommandotolken anger `uname -r` och bekräfta versionen för:

    * Ubuntu är ”4.4.0-77-generic” eller högre
    * SLES är ”4.4.59-92.20-default” eller högre

2. Skapa en avkastningen mellan standard nätverk vNIC och snabbare nätverk vNIC genom att köra kommandon som följer. Nätverkstrafik använder den högre prestanda snabbare nätverk vNIC, medan avkastningen garanterar att nätverkstrafiken inte avbryts över vissa ändringar i konfigurationen.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. När skriptet har körts, den virtuella datorn startar om efter 60 sekunder pausa.
4. När den virtuella datorn startas om och återansluter du till den genom att följa steg 5 – 7 igen.
5. Kör den `ifconfig` kommando och bekräfta att bond0 är nu tillgänglig och gränssnittet visas som upp. 
 
 >[!NOTE]
      >Program med snabbare nätverk måste kommunicera över den *bond0* gränssnitt inte *eth0*.  Gränssnittsnamnet ändras innan snabbare nätverksfunktioner når allmän tillgänglighet.

#### <a name="rhelcentos"></a>RHEL/CentOS

Skapar en Red Hat Enterprise Linux eller CentOS 7.3 VM kräver vissa ytterligare åtgärder för att läsa in de senaste drivrutinerna som behövs för SR-IOV och drivrutinens VF (Virtual Function) för nätverkskortet. Den första fasen av instruktionerna förbereder en avbildning som kan användas för att en eller flera virtuella datorer som har de drivrutiner som redan har lästs in.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>Steg ett: Förbered en Red Hat Enterprise Linux eller CentOS 7.3 basavbildning. 

1.  Etablera en icke - SRIOV CentOS 7.3 VM på Azure

2.  Installera LIS 4.2.2:
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  Hämta konfigurationsfiler
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  Ta bort etableringen av den här virtuella datorn

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  Stoppa den virtuella datorn, från Azure-portalen och gå till Virtuella datorer ”diskar”, samla in OSDisk VHD-URI. Den här URI: N innehåller grundläggande bild VHD namn och dess storage-konto. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>Steg två: etablera nya virtuella datorer på Azure

1.  Etablera nya virtuella datorer baserade med New-AzureRMVMConfig med hjälp av basavbildningen VHD i den första fasen, med AcceleratedNetworking aktiverad på vNIC:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  När virtuella datorer Starta Kontrollera VF-enhet genom att ”lspci” och Mellanox transaktionen. Vi bör till exempel se det här objektet i lspci utdata:
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  Kör skriptet partnerskap av:

    ```bash
    sudo bondvf.sh
    ```

4.  Starta om de nya virtuella datorerna:

    ```bash
    sudo reboot
    ```

Den virtuella datorn ska börja med bond0 konfigurerats och snabbare nätverk sökvägen aktiverad.  Kör `ifconfig` att bekräfta.
