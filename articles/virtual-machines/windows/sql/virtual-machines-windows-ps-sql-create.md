---
title: "Skapa en virtuell dator för SQL Server i Azure PowerShell (Resource Manager) | Microsoft Docs"
description: "Innehåller steg och PowerShell-skript för att skapa en virtuell Azure-dator med SQL Server virtuella galleriavbildningar."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/29/2017
ms.author: jroth
ms.openlocfilehash: 33c306258b6be40f2c5cbc016e3c84e36bf61e0d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Etablera en virtuell dator med SQL Server med hjälp av Azure PowerShell (Resource Manager)
> [!div class="op_single_selector"]
> * [Portal](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>Översikt
Den här kursen visar hur du skapar en enda Azure virtuell dator med hjälp av den **Azure Resource Manager** distributionsmodellen med hjälp av Azure PowerShell-cmdlets. I den här självstudiekursen skapar vi en virtuell dator med hjälp av en enskild disk från en avbildning i SQL-galleriet. Vi skapar nya providers för lagring, nätverk och beräkningsresurser som ska användas av den virtuella datorn. Om du har befintliga providers för någon av dessa resurser, kan du använda dessa providers i stället.

Om du behöver den klassiska versionen av det här avsnittet finns [etablera en virtuell dator med SQL Server med hjälp av Azure PowerShell klassiska](../classic/ps-sql-create.md).

## <a name="prerequisites"></a>Krav
Den här kursen behöver du:

* En Azure-konto och prenumeration innan du börjar. Om du inte har någon registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* [Azure PowerShell)](/powershell/azure/overview), lägsta version av 1.4.0 eller senare (den här kursen som skrivits med version 1.5.0).
  * Om du vill hämta din version skriver **Azure Get-Module - ListAvailable**.

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration
Öppna Windows PowerShell och upprätta åtkomst till ditt Azure-konto genom att köra följande cmdlet. Visas med ett tecken på skärmen för att ange dina autentiseringsuppgifter. Använd samma e-post och lösenord som du använder för att logga in på Azure-portalen.

```PowerShell
Add-AzureRmAccount
```

Se viss information på skärmen som inkluderar prenumerationsnamn och ID för prenumerationen standard när du har loggat in. Det här är den prenumeration där resurser för den här självstudiekursen kommer att skapas om du inte ändrar till en annan prenumeration. Om du har flera prenumerationer kör du följande cmdlet för att returnera en lista över alla dina prenumerationer:

```PowerShell
Get-AzureRmSubscription
```
Kör följande kommando för att ändra till en annan prenumeration med mål-SubscriptionName.

```PowerShell
Select-AzureRmSubscription -SubscriptionName YourTargetSubscriptionName
```

## <a name="define-image-variables"></a>Definiera variabler för avbildning
För att förenkla användbarhet och förståelse av skriptet slutförda från den här självstudiekursen kommer vi börjar genom att definiera ett antal variabler. Ändra parametervärden som du vill, men varning för namngivning av restriktioner relaterade till namnet längder och specialtecken när du ändrar de värden som anges.

### <a name="location-and-resource-group"></a>Plats och resursgruppen.
Du kan använda två variabler för att definiera dataområdet och den resursgrupp som du vill skapa de andra resurserna för den virtuella datorn.

Ändra efter behov och kör följande cmdlets för att initiera dessa variabler.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"
```

### <a name="storage-properties"></a>Lagringsegenskaper för
Du kan använda följande variabler för att definiera storage-konto och lagringstyp som ska användas av den virtuella datorn.

Ändra efter behov och kör följande cmdlet för att initiera dessa variabler. Observera att i det här exemplet använder [Premiumlagring](../premium-storage.md), som rekommenderas för produktionsarbetsbelastningar. Mer information om den här vägledningen och andra rekommendationer finns [prestandarelaterade Metodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Egenskaper för nätverk
Du kan använda följande variabler för att definiera nätverksgränssnittet, TCP/IP-allokeringsmetod, det virtuella nätverksnamnet, virtuella undernätsnamn, IP-adressintervall för det virtuella nätverket, IP-adressintervall för undernätet och offentliga domännamnet som ska användas av nätverket i den virtuella datorn.

Ändra efter behov och kör följande cmdlet för att initiera dessa variabler.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = "sqlvm1"
```

### <a name="virtual-machine-properties"></a>Egenskaper för virtuell dator
Du kan använda följande variabler för att definiera namnet på virtuella datorn, namnet på datorn, storleken på virtuella datorn och operativsystemet diskens namn för den virtuella datorn.

Ändra efter behov och kör följande cmdlet för att initiera dessa variabler.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="image-properties"></a>Bildegenskaper
Du kan använda följande variabler för att definiera avbildningen som ska användas för den virtuella datorn. I det här exemplet används SQL Server 2016 Developer edition avbildningen. Utvecklarversionen fritt är licensierad för testning och utveckling och du betalar bara för kostnaden för att köra den virtuella datorn.

Ändra efter behov och kör följande cmdlet för att initiera dessa variabler.

```PowerShell
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "SQLDEV"
$Version = "latest"
```

Observera att du kan få en fullständig lista över erbjudanden för SQL Server-avbildning med kommandot Get-AzureRmVMImageOffer:

```PowerShell
Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'
```

Och du kan se tillgängliga för ett erbjudande med kommandot Get-AzureRmVMImageSku SKU: er. Följande kommando visar alla SKU: er för den **SQL2016SP1 WS2016** erbjuder.

```PowerShell
Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer 'SQL2016SP1-WS2016' | Select Skus
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Med Resource Manager-distributionsmodellen är det första objektet som du skapar resursgruppen. Vi använder den [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) för att skapa en Azure-resursgrupp och dess resurser med resursgruppens namn och plats som anges av de variabler som du tidigare har initierat.

Kör följande cmdlet för att skapa din nya resursgrupp.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Den virtuella datorn krävs lagringsresurser för operativsystemets disk och för de SQL Server data- och loggfiler. För enkelhetens skull skapar vi en enskild disk för båda. Du kan koppla ytterligare diskar senare med den [Lägg till Azure-disken](/powershell/module/azure/add-azuredisk) cmdlet för att placera din SQL Server-data och loggfilen filer på dedikerade diskar. Vi använder den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) för att skapa ett standardlagringskonto i din nya resursgrupp och med lagringskontonamn, Sku namn och plats som definierats med hjälp av de variabler som du tidigare har initierat.

Kör följande cmdlet för att skapa det nya kontot.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location
```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Den virtuella datorn kräver ett antal nätverksresurser för nätverksanslutningen.

* Varje virtuell dator kräver ett virtuellt nätverk.
* Ett virtuellt nätverk måste ha minst ett undernät som har definierats.
* Ett nätverksgränssnitt måste definieras med en offentlig eller privat IP-adress.

### <a name="create-a-virtual-network-subnet-configuration"></a>Skapa en konfiguration av undernät för virtuellt nätverk
Vi startar genom att skapa en konfiguration av undernät för våra virtuella nätverket. I våra självstudier ska vi skapar en standard undernätet med hjälp av den [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet. Vi skapar våra undernätskonfiguration av virtuellt nätverk med namn och adress undernätsprefixet definieras med hjälp av de variabler som du tidigare har initierat.

> [!NOTE]
> Du kan ange ytterligare egenskaper för virtuellt nätverk undernätskonfiguration använder denna cmdlet, men som ligger utanför omfånget för den här kursen.

Kör följande cmdlet för att skapa konfigurationen för virtuella undernät.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Därefter skapar vi vårt virtuella datornätverk som använder den [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet. Vi skapar vårt virtuella nätverk i din nya resursgrupp, med namn, plats och adress prefix definieras de variabler som du tidigare har initierat, samt använder undernätskonfiguration som du definierade i föregående steg.

Kör följande cmdlet för att skapa det virtuella nätverket.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Skapa offentlig IP-adress
Nu när vi har vårt virtuella nätverk som definierats som vi behöver konfigurera en IP-adress för anslutning till den virtuella datorn. Den här självstudiekursen skapar vi en offentlig IP-adress med hjälp av dynamisk IP-adresser för att stödja Internetanslutning. Vi använder den [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) för att skapa den offentliga IP-adressen i resursen grupp skapat prevously och med namn, plats, fördelning och DNS-domännamnet anges med hjälp av de variabler som du tidigare har initierat.

> [!NOTE]
> Du kan ange ytterligare egenskaper för den offentliga IP-adressen som använder denna cmdlet, men som ligger utanför omfånget för den här första självstudien. Du kan också skapa en privat adress eller en adress med en statisk adress, men som även är utanför omfattningen för den här kursen.

Kör följande cmdlet för att skapa din offentliga IP-adress.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-interface"></a>Skapa nätverksgränssnittet
Vi är nu redo att skapa nätverksgränssnittet som våra virtuella datorn ska använda. Vi använder den [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) för att skapa våra nätverksgränssnitt i resursgruppen som du skapade tidigare och med namn, plats, undernät och offentliga IP-adressen har definierats.

Kör följande cmdlet för att skapa nätverksgränssnittet.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id
```

## <a name="configure-a-vm-object"></a>Konfigurera ett VM-objekt
Nu när vi har lagring och nätverksresurser som definierats är vi redo att definiera beräkningsresurser för den virtuella datorn. I våra självstudier ska vi ange storleken på virtuella datorn och egenskaper för olika operativsystem, ange det nätverksgränssnitt som vi skapade tidigare, definiera blob-lagring och sedan ange operativsystemets disk.

### <a name="create-the-vm-object"></a>Skapa VM-objekt
Vi startar genom att ange storleken på virtuella datorn. Den här självstudiekursen kommer specificerar vi en DS13. Vi använder den [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) för att skapa en konfigurerbar virtuella datorobjektet med namnet och storleken som definieras med hjälp av de variabler som du tidigare har initierat.

Kör följande cmdlet för att skapa det virtuella datorobjektet.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Skapa ett autentiseringsuppgiftobjekt för namn och lösenord för autentiseringsuppgifter för lokal administratör
Innan vi kan ange egenskaperna för operativsystemet för den virtuella datorn måste behöver vi ange autentiseringsuppgifter för det lokala administratörskontot som en säker sträng. För att åstadkomma detta, kommer vi att använda den [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

Kör följande cmdlet och i fönstret Windows PowerShell autentiseringsuppgifter, skriver du namnet och lösenordet för det lokala administratörskontot på den virtuella datorn i Windows.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ange egenskaperna för operativsystemet för den virtuella datorn
Vi är nu redo att ange egenskaper för den virtuella datorns operativsystem. Vi använder den [Set AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) för att ange vilken typ av operativsystem som Windows, kräver den [virtuella datorns agent](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) anger att cmdleten aktiverar automatisk uppdatering är installerad, och ange namnet på virtuella datorn, datornamnet och autentiseringsuppgifter med hjälp av de variabler som du tidigare har initierat.

Kör följande cmdlet för att ange egenskaperna för operativsystemet för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Lägg till nätverksgränssnittet till den virtuella datorn
Nu ska vi lägger till nätverksgränssnittet som vi skapade tidigare till den virtuella datorn. Vi använder den [Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) för att lägga till nätverksgränssnittet som använder nätverket gränssnittet variabeln som du angav tidigare.

Kör följande cmdlet om du vill ange nätverksgränssnittet för din virtuella dator.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ange blob-lagringsplats för disken som ska användas av den virtuella datorn
Vi kommer sedan att ange blob-lagringsplats för disken som ska användas av den virtuella datorn med de variabler som du angav tidigare.

Kör följande cmdlet för att ange platsen för blob-lagring.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ange operativsystemet diskegenskaper för den virtuella datorn
Vi kommer därefter ange operativsystemet diskegenskaper för den virtuella datorn. Vi använder den [Set AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) för att ange att operativsystemet för den virtuella datorn ska hämtas från en bild för att definiera cachelagring för att skrivskyddat (eftersom SQL Server installeras på samma disk) och ange namnet på virtuella datorn och operativsystemdisken definieras med hjälp av de variabler som vi definierade tidigare.

Kör följande cmdlet om du vill ange operativsystemet diskegenskaper för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Ange plattformsavbildningen som för den virtuella datorn
Vår senaste konfigurationssteget är att ange plattformsavbildning för våra virtuella datorn. I våra självstudier ska använder vi den senaste SQL Server 2016 CTP-bilden. Vi använder den [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) för att använda den här avbildningen som definieras av de variabler som du angav tidigare.

Kör följande cmdlet för att ange plattformsavbildningen som för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Skapa den virtuella SQL-datorn
Nu när du är klar konfigurationsstegen är du redo att skapa den virtuella datorn. Vi använder den [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) för att skapa den virtuella datorn med de variabler som vi har definierat.

Kör följande cmdlet om du vill skapa den virtuella datorn.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Den virtuella datorn skapas. Observera att skapas ett standardlagringskonto för startdiagnostikinställningar eftersom det angivna lagringskontot för den virtuella disken är ett premiumlagringskonto.

Nu kan du visa den här datorn i Azure-portalen för att se [offentliga IP-adressen och dess fullständigt kvalificerade domännamnet](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="example-script"></a>Exempelskriptet
Följande skript innehåller fullständig PowerShell-skriptet för den här självstudiekursen. Det förutsätts att du har redan installerat Azure-prenumerationen för användning med den **Add-AzureRmAccount** och **Select-AzureRmSubscription** kommandon.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = "sqlvm1"

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "Enterprise"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

## <a name="next-steps"></a>Nästa steg
Du är redo att ansluta till den virtuella datorn med installation och RDP-anslutning när du har skapat den virtuella datorn. Mer information finns i [Anslut till en SQL Server-dator i Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
