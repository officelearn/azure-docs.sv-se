---
title: "Så här skapar du virtuella SQL Server-datorer med Azure PowerShell | Microsoft Docs"
description: "Innehåller steg och PowerShell-kommandon för att skapa en virtuell Azure-dator med SQL Server virtuella galleriavbildningar."
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
ms.date: 11/29/2017
ms.author: jroth
ms.openlocfilehash: 00f229c1b7fd9f3abaea1a07cec9b44efac5a5fa
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-create-sql-server-virtual-machines-with-azure-powershell"></a>Hur du skapar SQL Server-datorer med Azure PowerShell

Den här guiden förklarar alternativen för att skapa Windows SQL Server-datorer med Azure PowerShell. En effektiv Azure PowerShell-exempel med flera standardvärden finns i [SQL VM Azure PowerShell quickstart](quickstart-sql-vm-create-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

1. Öppna PowerShell och upprätta åtkomst till ditt Azure-konto genom att köra kommandot **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Du bör se en inloggningsskärm där du kan ange dina autentiseringsuppgifter. Använd samma e-postadress och lösenord som du använder för att logga in på Azure Portal.

## <a name="define-image-variables"></a>Definiera variabler för avbildning
Starta genom att definiera ett antal variabler för att förenkla skapande återanvändning och skript. Ändra parametervärden som du vill, men varning för namngivning av restriktioner relaterade till namnet längder och specialtecken när du ändrar de värden som anges.

### <a name="location-and-resource-group"></a>Plats och resursgruppen.
Du kan använda två variabler för att definiera dataområdet och den resursgrupp som du skapar de andra resurserna för den virtuella datorn.

Ändra efter behov och kör följande cmdlets för att initiera dessa variabler.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Lagringsegenskaper för
Du kan använda följande variabler för att definiera storage-konto och lagringstyp som ska användas av den virtuella datorn.

Ändra efter behov och kör följande cmdlet för att initiera dessa variabler. Observera att i det här exemplet använder [Premiumlagring](../premium-storage.md), som rekommenderas för produktionsarbetsbelastningar.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Egenskaper för nätverk
Du kan använda följande variabler för att definiera nätverksgränssnittet, TCP/IP-allokeringsmetod, det virtuella nätverksnamnet, virtuella undernätsnamn, IP-adressintervall för det virtuella nätverket, IP-adressintervall för undernätet och offentliga domännamnet som ska användas av nätverket i den virtuella datorn.

Ändra efter behov och kör följande cmdlet för att initiera dessa variabler.

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
Du kan använda följande variabler för att definiera namnet på virtuella datorn, namnet på datorn, storleken på virtuella datorn och operativsystemet diskens namn för den virtuella datorn.

Ändra efter behov och kör följande cmdlet för att initiera dessa variabler.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Välj en SQL Server-avbildning
Använd följande variabler för att definiera den SQL Server-avbildningen som ska användas för den virtuella datorn.

1. Först lista ut alla erbjudanden för SQL Server-avbildning med den **Get-AzureRmVMImageOffer** kommando:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. För den här kursen använder du följande variabler för att ange 2017 för SQL Server på Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Nästa lista ut tillgängliga versioner för ditt erbjudande.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. För den här kursen använder SQL Server 2017 Developer edition (**SQLDEV**). Utvecklarversionen fritt är licensierad för testning och utveckling och du betalar bara för kostnaden för att köra den virtuella datorn.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Med Resource Manager-distributionsmodellen är det första objektet som du skapar resursgruppen. Använd den [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) för att skapa en Azure-resursgrupp och dess resurser med resursgruppens namn och plats som anges av de variabler som du tidigare har initierat.

Kör följande cmdlet för att skapa din nya resursgrupp.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Den virtuella datorn krävs lagringsresurser för operativsystemets disk och för de SQL Server data- och loggfiler. För enkelhetens skull skapa vi en enskild disk för båda. Du kan koppla ytterligare diskar senare med den [Lägg till Azure-disken](/powershell/module/azure/add-azuredisk) cmdlet för att placera din SQL Server-data och loggfilen filer på dedikerade diskar. Använd den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) för att skapa ett standardlagringskonto i din nya resursgrupp och med lagringskontonamn, Sku namn och plats som definierats med hjälp av de variabler som du tidigare har initierat .

Kör följande cmdlet för att skapa det nya kontot.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Kan ta några minuter att skapa lagringskontot.

## <a name="create-network-resources"></a>Skapa nätverksresurser
Den virtuella datorn kräver ett antal nätverksresurser för nätverksanslutningen.

* Varje virtuell dator kräver ett virtuellt nätverk.
* Ett virtuellt nätverk måste ha minst ett undernät som har definierats.
* Ett nätverksgränssnitt måste definieras med en offentlig eller privat IP-adress.

### <a name="create-a-virtual-network-subnet-configuration"></a>Skapa en konfiguration av undernät för virtuellt nätverk
Börja med att skapa en konfiguration av undernät för våra virtuella nätverket. I våra självstudier ska vi skapa en standard undernätet med hjälp av den [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet. Vi skapa våra undernätskonfiguration av virtuellt nätverk med namn och adress undernätsprefixet definieras med hjälp av de variabler som du tidigare har initierat.

> [!NOTE]
> Du kan ange ytterligare egenskaper för virtuellt nätverk undernätskonfiguration använder denna cmdlet, men som ligger utanför omfånget för den här kursen.

Kör följande cmdlet för att skapa konfigurationen för virtuella undernät.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Nu ska du skapa virtuella nätverk med hjälp av den [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet. Skapa virtuellt nätverk i din nya resursgrupp, med namn, plats och adress prefix definieras de variabler som du tidigare har initierat, samt använder undernätskonfiguration som du definierade i föregående steg.

Kör följande cmdlet för att skapa det virtuella nätverket.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Skapa offentlig IP-adress
Nu när vi har vårt virtuella nätverk som definierats som vi behöver konfigurera en IP-adress för anslutning till den virtuella datorn. För den här självstudiekursen skapar vi en offentlig IP-adress med hjälp av dynamisk IP-adresser för att stödja Internetanslutning. Använd den [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) för att skapa den offentliga IP-adressen i resursgruppen som du skapade tidigare och med namn, plats, fördelning och DNS-domännamnet anges med hjälp av variablerna som du tidigare har initierats.

> [!NOTE]
> Du kan ange ytterligare egenskaper för den offentliga IP-adressen som använder denna cmdlet, men som ligger utanför omfånget för den här första självstudien. Du kan också skapa en privat adress eller en adress med en statisk adress, men som även är utanför omfattningen för den här kursen.

Kör följande cmdlet för att skapa din offentliga IP-adress.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Skapa nätverkssäkerhetsgruppen
Skapa en säkerhetsgrupp för nätverk om du vill skydda den virtuella dator och SQL Server-trafiken.

1. Först skapa en grupp nätverkssäkerhetsregeln för RDP att tillåta anslutningar till fjärrskrivbord.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurera en nätverkssäkerhetsregeln på gruppen som tillåter trafik på TCP-port 1433. Detta möjliggör anslutningar till SQL Server via internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Skapa sedan nätverkssäkerhetsgruppen.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Skapa nätverksgränssnittet
Vi är nu redo att skapa nätverksgränssnittet som våra virtuella datorn ska använda. Vi kallar det [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) för att skapa våra nätverksgränssnitt i resursgruppen som du skapade tidigare och med namn, plats, undernät och offentliga IP-adressen har definierats.

Kör följande cmdlet för att skapa nätverksgränssnittet.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurera ett VM-objekt
Nu när vi har lagring och nätverksresurser som definierats är vi redo att definiera beräkningsresurser för den virtuella datorn. I våra självstudier ska vi ange storleken på virtuella datorn och egenskaper för olika operativsystem, ange det nätverksgränssnitt som vi skapade tidigare, definiera blob-lagring och sedan ange operativsystemets disk.

### <a name="create-the-vm-object"></a>Skapa VM-objekt
Starta genom att ange storleken på virtuella datorn. Den här självstudiekursen kommer specificerar vi en DS13. Vi kallar det [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) för att skapa en konfigurerbar virtuella datorobjektet med namnet och storleken som definieras med hjälp av de variabler som du tidigare har initierat.

Kör följande cmdlet för att skapa det virtuella datorobjektet.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Skapa ett autentiseringsuppgiftobjekt för namn och lösenord för autentiseringsuppgifter för lokal administratör
Innan vi kan ange egenskaperna för operativsystemet för den virtuella datorn måste behöver vi ange autentiseringsuppgifter för det lokala administratörskontot som en säker sträng. Du åstadkommer detta genom att använda den [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

Kör följande cmdlet och i fönstret PowerShell autentiseringsuppgifter, skriver du namnet och lösenordet för det lokala administratörskontot på den virtuella datorn.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ange egenskaperna för operativsystemet för den virtuella datorn
Nu vi är redo att ange egenskaper för den virtuella datorns operativsystem med [Set AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) för att ange vilken typ av operativsystem som Windows, kräver den [virtuella datorns agent](../classic/agents-and-extensions-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) Ange att cmdlet aktiverar automatisk uppdatering är installerad, och ange namnet på virtuella datorn, datornamnet och autentiseringsuppgifter med hjälp av de variabler som du tidigare har initierat.

Kör följande cmdlet för att ange egenskaperna för operativsystemet för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Lägg till nätverksgränssnittet till den virtuella datorn
Sedan lägger vi till nätverksgränssnittet som vi skapade tidigare till den virtuella datorn. Anropa den [Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) för att lägga till nätverksgränssnittet som använder nätverket gränssnittet variabeln som du angav tidigare.

Kör följande cmdlet om du vill ange nätverksgränssnittet för din virtuella dator.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ange blob-lagringsplats för disken som ska användas av den virtuella datorn
Ange därefter blob-lagringsplats för disken som ska användas av den virtuella datorn med de variabler som du angav tidigare.

Kör följande cmdlet för att ange platsen för blob-lagring.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ange operativsystemet diskegenskaper för den virtuella datorn
Ange sedan operativsystemet diskegenskaper för den virtuella datorn. Använd den [Set AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) för att ange att operativsystemet för den virtuella datorn ska hämtas från en avbildning för att ange cachelagring för att skrivskyddat (eftersom SQL Server installeras på samma disk) och definiera den virtuella datorn namn och operativsystemdisken definieras med hjälp av de variabler som vi definierade tidigare.

Kör följande cmdlet om du vill ange operativsystemet diskegenskaper för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Ange plattformsavbildningen som för den virtuella datorn
Vår senaste konfigurationssteget är att ange plattformsavbildning för våra virtuella datorn. I våra självstudier ska använder vi den senaste SQL Server 2016 CTP-bilden. Använd den [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) för att använda den här avbildningen som definieras av de variabler som du angav tidigare.

Kör följande cmdlet för att ange plattformsavbildningen som för den virtuella datorn.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Skapa den virtuella SQL-datorn
Nu när du är klar konfigurationsstegen är du redo att skapa den virtuella datorn. Använd den [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) för att skapa den virtuella datorn med de variabler som vi har definierat.

Kör följande cmdlet om du vill skapa den virtuella datorn.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Den virtuella datorn skapas.

> [!NOTE]
> Du kan ignorera felet om start-diagnostik. Ett standardlagringskonto skapas för startdiagnostikinställningar, eftersom det angivna lagringskontot för den virtuella disken är ett premiumlagringskonto.

## <a name="install-the-sql-iaas-agent"></a>Installera SQL Iaas-agenten
Virtuella datorer i SQL Server stöder automatisk hanteringsfunktioner med den [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). När den nya virtuella datorn har skapats kan du installera agenten på den genom att köra följande kommando.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Ta bort ett test VM

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Följande kommando stoppar den virtuella datorn men lämnar den tillgänglig för framtida bruk.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn med kommandot **Remove-AzureRmResourceGroup**. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet.

## <a name="example-script"></a>Exempelskriptet
Följande skript innehåller fullständig PowerShell-skriptet för den här självstudiekursen. Det förutsätts att du har redan installerat Azure-prenumerationen för användning med den **Add-AzureRmAccount** och **Select-AzureRmSubscription** kommandon.

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

- Ansluta till den virtuella datorn med fjärrskrivbord (RDP).
- Konfigurera SQL Server-inställningar i portalen för den virtuella datorn, inklusive:
   - [Inställningar för lagring](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatiserade hanteringsuppgifter](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurera anslutning](virtual-machines-windows-sql-connect.md).
- Ansluta klienter och program till den nya SQL Server-instansen.

