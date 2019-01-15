---
title: Etablering guide för SQL Server-datorer med Azure PowerShell | Microsoft Docs
description: Innehåller steg och PowerShell-kommandon för att skapa en Azure-dator med SQL Server VM-avbildningar i galleriet.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: jroth
ms.openlocfilehash: 770272372c77421ef994f3adc9d36f47a235093c
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261816"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Så här etablerar du SQL Server-datorer med Azure PowerShell

Den här guiden beskriver dina alternativ för att skapa Windows SQL Server-datorer med Azure PowerShell. En effektiv Azure PowerShell-exempel med flera standardvärden finns i den [SQL-dator med Azure PowerShell-Snabbstart](quickstart-sql-vm-create-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här artikeln kräver Azure PowerShell-Modulversion 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

1. Öppna PowerShell och upprätta åtkomst till ditt Azure-konto genom att köra kommandot **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Du bör se en skärm för att ange dina autentiseringsuppgifter. Använd samma e-postadress och lösenord som du använder för att logga in på Azure Portal.

## <a name="define-image-variables"></a>Definiera variabler för avbildning
Starta genom att definiera ett antal variabler för att återanvända värden och förenkla skriptskapande. Ändra parametervärden som du vill, men tänk på att namnge restriktioner relaterade till namn längder och specialtecken när du ändrar de värden som anges.

### <a name="location-and-resource-group"></a>Platsen och resursgruppen
Definiera dataområdet och resursgrupp där du skapar andra VM-resurserna.

Ändra som du vill och kör sedan följande cmdlets för att initiera dessa variabler.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Lagringsegenskaper
Definiera lagringskontot och lagringstyp som ska användas av den virtuella datorn.

Ändra som du vill ha och sedan kör du följande cmdlet för att initiera dessa variabler. Vi rekommenderar att du använder [Premiumlagring](../premium-storage.md) för produktionsarbetsbelastningar.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Nätverksegenskaper
Definiera egenskaper som ska användas av nätverk i den virtuella datorn. 

- Nätverksgränssnitt
- Allokeringsmetoden för TCP/IP
- Namn på virtuellt nätverk
- Namn på virtuella undernät
- IP-adressintervall för det virtuella nätverket
- IP-adressintervall för undernätet
- Offentliga domännamnsetikett

Ändra som du vill ha och sedan köra denna cmdlet för att initiera dessa variabler.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Egenskaper för virtuell dator
Definiera namnet på virtuella datorn, namnet på datorn, storleken på virtuella datorn och operativsystemet diskens namn för den virtuella datorn.

Ändra som du vill ha och sedan köra denna cmdlet för att initiera dessa variabler.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Välj en SQL Server-avbildning
Definiera avbildningen som SQL Server ska användas för den virtuella datorn.

1. Först, listar alla erbjudanden för SQL Server-avbildning med den **Get-AzureRmVMImageOffer** kommando:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Den här självstudien använder du följande variabler ange SQL Server 2017 på Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Sedan en lista över tillgängliga versioner för ditt erbjudande.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Den här självstudien använder du SQL Server 2017 Developer edition (**SQLDEV**). Utvecklarversionen fritt är licensierad för utveckling och testning och du betalar bara för kostnaden för att köra den virtuella datorn.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Med Resource Manager-distributionsmodellen är det första objektet som du skapar resursgruppen. Använd den [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet för att skapa en Azure-resursgrupp och dess resurser. Ange de variabler som du tidigare har initierats för resursgruppens namn och plats.

Kör denna cmdlet för att skapa en ny resursgrupp.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Den virtuella datorn kräver lagringsresurser för operativsystemdisken och för de SQL Server data och loggfiler. För enkelhetens skull ska du skapa en enskild disk för båda. Du kan lägga till ytterligare diskar senare med den [Lägg till Azure-Disk](/powershell/module/servicemanagement/azure/add-azuredisk) cmdlet för att placera dina SQL Server-data och loggfiler filer på dedikerade diskar. Använd den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet för att skapa ett standardlagringskonto i din nya resursgrupp. Ange de variabler som du tidigare har initierats för lagringskontonamn, storage Sku-namn och plats.

Kör denna cmdlet för att skapa det nya kontot.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Kan ta några minuter att skapa lagringskontot.

## <a name="create-network-resources"></a>Skapa nätverksresurser
Den virtuella datorn kräver ett antal nätverksresurser för nätverksanslutning.

* Varje virtuell dator kräver ett virtuellt nätverk.
* Ett virtuellt nätverk måste ha minst ett undernät som definierats.
* Ett nätverksgränssnitt måste definieras med en offentlig eller privat IP-adress.

### <a name="create-a-virtual-network-subnet-configuration"></a>Skapa en undernätskonfiguration för virtuellt nätverk
Börja med att skapa en undernätskonfiguration för det virtuella nätverket. I den här självstudien skapar du en standard undernätet med hjälp av den [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet. Ange de variabler som du tidigare har initierats för undernätsprefixet för namn och adress.

> [!NOTE]
> Du kan definiera ytterligare egenskaper för den virtuella nätverkskonfigurationen på undernätet med hjälp av den här cmdleten, men som ligger utanför omfånget för den här självstudien.

Kör denna cmdlet för att skapa din virtuella undernät-konfiguration.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Därefter skapar det virtuella nätverket i din nya resurs med det [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet. Ange de variabler som du tidigare har initierats för namn, plats och adressprefix. Använd Undernätskonfigurationen som du definierade i föregående steg.

Kör denna cmdlet för att skapa det virtuella nätverket.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Skapa offentlig IP-adress
Nu när ditt virtuella nätverk har definierats, måste du konfigurera en IP-adress för anslutning till den virtuella datorn. I den här självstudien skapar du en offentlig IP-adress med hjälp av dynamisk IP-adresser för att stödja Internetanslutning. Använd den [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) cmdlet för att skapa den offentliga IP-adressen i din nya resursgrupp. Ange de variabler som du tidigare har initierats för namn, plats, allokeringsmetoden och DNS-domännamnsetikett.

> [!NOTE]
> Du kan definiera ytterligare egenskaper för offentliga IP-adress med hjälp av den här cmdleten, men som ligger utanför omfånget för den här första självstudien. Du kan också skapa en privat adress eller en adress med en statisk adress, men som också är utanför omfattningen för den här självstudien.

Kör denna cmdlet för att skapa din offentliga IP-adress.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Skapa nätverkssäkerhetsgruppen
Skapa en nätverkssäkerhetsgrupp för att skydda den virtuella dator och SQL Server-trafiken.

1. Skapa först en nätverkssäkerhetsgruppregel för RDP att tillåta anslutningar till fjärrskrivbord.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurera en regel för nätverkssäkerhetsgrupp som tillåter trafik på TCP-port 1433. Detta möjliggör anslutningar till SQL Server via internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Skapa nätverkssäkerhetsgruppen.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Skapa nätverksgränssnittet
Du är nu redo att skapa nätverksgränssnitt för den virtuella datorn. Använd den [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) cmdlet för att skapa ett nätverksgränssnitt i din nya resursgrupp. Ange namn, plats, undernät och offentlig IP-adress som tidigare definierats.

Kör denna cmdlet för att skapa ett nätverksgränssnitt.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurera ett VM-objekt
Nu när lagrings- och nätverksresurser definieras, är du redo att definiera beräkningsresurser för den virtuella datorn.

- Ange storleken på virtuella datorn och olika egenskaperna för operativsystemet.
- Ange det nätverksgränssnitt som du skapade tidigare.
- Definiera blob-lagring.
- Ange operativsystemets disk.

### <a name="create-the-vm-object"></a>Skapa VM-objekt
Starta genom att ange storleken på virtuella datorn. Ange en DS13 i den här självstudien. Använd den [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) cmdlet för att skapa en konfigurerbar virtuella datorobjektet. Ange de variabler som du tidigare har initierats för namn och storlek.

Kör denna cmdlet för att skapa det virtuella datorobjektet.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Skapa ett autentiseringsuppgiftsobjekt för att lagra namn och lösenord för autentiseringsuppgifter för lokal administratör
Innan du kan ange egenskaperna för operativsystemet för den virtuella datorn, måste du ange autentiseringsuppgifter för det lokala administratörskontot som en säker sträng. Du åstadkommer detta genom att använda den [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

Kör följande cmdlet och i fönstret PowerShell autentiseringsuppgifter, skriver du namnet och lösenordet för det lokala administratörskontot på den virtuella datorn.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ange egenskaperna för operativsystemet för den virtuella datorn
Nu är du redo att ställa in egenskaperna för operativsystemet i den virtuella datorn med den [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) cmdlet.

- Ange typ av operativsystem som Windows.
- Kräver den [VM-agenten](../../extensions/agent-windows.md) installeras.
- Ange att cmdleten aktiverar automatisk uppdatering.
- Ange de variabler som du tidigare har initierats för virtuella datornamn, datornamnet och autentiseringsuppgifter.

Kör denna cmdlet för att ställa in egenskaperna för operativsystemet för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Lägga till nätverksgränssnittet till den virtuella datorn
Använd sedan den [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) cmdlet för att lägga till nätverksgränssnittet med hjälp av variabeln som du angav tidigare.

Kör denna cmdlet för att ställa in nätverksgränssnitt för den virtuella datorn.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ange platsen för blob-lagring för disken som ska användas av den virtuella datorn
Nu ska vi konfigurera blob-lagringsplats för den Virtuella datorns disk med hjälp av variablerna som du angav tidigare.

Kör denna cmdlet för att ange platsen för blob-lagring.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ange operativsystemet diskegenskaper för den virtuella datorn
Nu ska vi konfigurera operativsystemet diskegenskaper för den virtuella datorn med den [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) cmdlet. 

- Ange att operativsystemet för den virtuella datorn kommer från en avbildning.
- Ange cachelagring för att läsa endast (eftersom SQL Server installeras på samma disk).
- Ange de variabler som du tidigare har initierats för virtuella datorns namn och operativsystemdisken.

Kör denna cmdlet för att ställa in operativsystemet diskegenskaper för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Ange plattformsavbildningen som för den virtuella datorn
Det sista konfigurationssteget är att ange plattformsavbildningen som för din virtuella dator. Använd den senaste SQL Server 2016 CTP-avbildningen för den här självstudien. Använd den [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) cmdlet för att använda den här avbildningen med de variabler som du angav tidigare.

Kör denna cmdlet för att ange plattformsavbildningen som för din virtuella dator.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Skapa den virtuella SQL-datorn
Nu när du är klar konfigurationsstegen, är du redo att skapa den virtuella datorn. Använd den [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet för att skapa den virtuella datorn med hjälp av variablerna som du har definierat.

> [!TIP]
> Kan ta några minuter att skapa den virtuella datorn.

Kör denna cmdlet för att skapa den virtuella datorn.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Den virtuella datorn skapas.

> [!NOTE]
> Om du får ett felmeddelande om startdiagnostik kan ignorera du den. Ett standardlagringskonto skapas för startdiagnostik eftersom det angivna lagringskontot för den virtuella datorns disk är ett premium storage-konto.

## <a name="install-the-sql-iaas-agent"></a>Installera SQL Iaas-agenten
SQL Server-datorer stöder automatisk hanteringsfunktioner med den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md). Kör följande kommando för att installera agenten på den nya virtuella datorn när den har skapats.


   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>Stoppa eller ta bort en virtuell dator

Om du inte behöver den virtuella datorn ska köras kontinuerligt kan undvika du onödiga kostnader genom att stoppa den när den inte används. Följande kommando stoppar den virtuella datorn men lämnar den tillgänglig för framtida bruk.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn med kommandot **Remove-AzureRmResourceGroup**. Detta permanent tar bort den virtuella datorn, så Använd det här kommandot med försiktighet.

## <a name="example-script"></a>Exempelskript
Följande skript innehåller det fullständiga PowerShell-skriptet för den här självstudiekursen. Det förutsätts att du redan har installerat Azure-prenumerationen ska användas med den **Connect-AzureRmAccount** och **Select-AzureRmSubscription** kommandon.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Nästa steg
När den virtuella datorn har skapats kan du:

- Anslut till den virtuella datorn med RDP
- Konfigurera SQL Server-inställningar i portalen för din virtuella dator, inklusive:
   - [Lagringsinställningar](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatiserade hanteringsuppgifter](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurera anslutningen](virtual-machines-windows-sql-connect.md)
- Ansluta klienter och program till den nya SQL Server-instansen

