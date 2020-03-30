---
title: Etableringsguide för virtuella SQL Server-datorer med Azure PowerShell | Microsoft-dokument
description: Innehåller steg och PowerShell-kommandon för att skapa en virtuell Azure-dator med virtuella SQL Server-galleriavbildningar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1578547fbca4caaecb209021569f0fbb2f1ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790630"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Etablera virtuella SQL Server-datorer med Azure PowerShell

I den här guiden beskrivs alternativen för att skapa virtuella datorer med Windows SQL Server med Azure PowerShell. Ett förenklat Azure PowerShell-exempel med fler standardvärden finns i [snabbstarten för SQL VM Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

1. Öppna PowerShell och upprätta åtkomst till ditt Azure-konto genom att köra kommandot **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Du bör se en skärm där du kan ange dina autentiseringsuppgifter. Använd samma e-postadress och lösenord som du använder för att logga in på Azure Portal.

## <a name="define-image-variables"></a>Definiera bildvariabler
Om du vill återanvända värden och förenkla skapandet av skript börjar du med att definiera ett antal variabler. Ändra parametervärdena som du vill, men var medveten om namngivningsbegränsningar relaterade till namnlängder och specialtecken när du ändrar de angivna värdena.

### <a name="location-and-resource-group"></a>Plats- och resursgrupp
Definiera dataområdet och resursgruppen som du skapar de andra vm-resurserna i.

Ändra som du vill och kör sedan dessa cmdlets för att initiera dessa variabler.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Lagringsegenskaper
Definiera lagringskontot och vilken typ av lagring som ska användas av den virtuella datorn.

Ändra som du vill och kör sedan följande cmdlet för att initiera dessa variabler. Vi rekommenderar att du använder [premium-SSD:er](../disks-types.md#premium-ssd) för produktionsarbetsbelastningar.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Nätverksegenskaper
Definiera de egenskaper som ska användas av nätverket på den virtuella datorn. 

- Nätverksgränssnitt
- TCP/IP-allokeringsmetod
- Virtuellt nätverksnamn
- Namn på virtuellt undernät
- Intervall av IP-adresser för det virtuella nätverket
- Intervall av IP-adresser för undernätet
- Namnetikett för allmän egendom

Ändra som du vill och kör sedan den här cmdleten för att initiera dessa variabler.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Egenskaper för virtuella datorer
Definiera namnet på den virtuella datorn, datornamnet, storleken på den virtuella datorn och operativsystemets disknamn för den virtuella datorn.

Ändra som du vill och kör sedan den här cmdleten för att initiera dessa variabler.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Välja en SQL Server-avbildning

Använd följande variabler för att definiera den SQL Server-avbildning som ska användas för den virtuella datorn. 

1. Först lista ut alla SQL Server-avbildningserbjudanden med `Get-AzVMImageOffer` kommandot. Det här kommandot visar aktuella avbildningar som är tillgängliga i Azure Portal och även äldre avbildningar som bara kan installeras med PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Den här självstudien använder du följande variabler för att ange SQL Server 2017 på Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Därefter lista tillgängliga utgåvor för ditt erbjudande.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Den här självstudien använder du SQL Server 2017 Developer edition (**SQLDEV**). Utvecklarutgåvan är fritt licensierad för testning och utveckling och du betalar bara för kostnaden för att köra den virtuella datorn.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Med resurshanterarens distributionsmodell är det första objektet som du skapar resursgruppen. Använd cmdleten [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) för att skapa en Azure-resursgrupp och dess resurser. Ange de variabler som du tidigare initierade för resursgruppens namn och plats.

Kör den här cmdleten för att skapa den nya resursgruppen.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Den virtuella datorn kräver lagringsresurser för operativsystemdisken och för SQL Server-data och loggfiler. För enkelhetens skull skapar du en enda disk för båda. Du kan bifoga ytterligare diskar senare med cmdleten Lägg till Azure Disk för att placera DINA SQL [Server-data](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) och loggfiler på dedikerade diskar. Använd [cmdleten New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) för att skapa ett standardlagringskonto i den nya resursgruppen. Ange de variabler som du tidigare initierade för lagringskontonamnet, lagrings-Sku-namnet och platsen.

Kör den här cmdleten för att skapa ditt nya lagringskonto.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Det kan ta några minuter att skapa lagringskontot.

## <a name="create-network-resources"></a>Skapa nätverksresurser
Den virtuella datorn kräver ett antal nätverksresurser för nätverksanslutning.

* Varje virtuell dator kräver ett virtuellt nätverk.
* Ett virtuellt nätverk måste ha minst ett undernät definierat.
* Ett nätverksgränssnitt måste definieras med antingen en offentlig eller en privat IP-adress.

### <a name="create-a-virtual-network-subnet-configuration"></a>Skapa en konfiguration av ett virtuellt nätverksnät
Börja med att skapa en undernätskonfiguration för det virtuella nätverket. För den här självstudien skapar du ett standardundernät med cmdleten [New-AzVirtualNetworkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Ange de variabler som du tidigare initierade för undernätsnamnet och adressprefixet.

> [!NOTE]
> Du kan definiera ytterligare egenskaper för den virtuella nätverksundernätskonfigurationen med den här cmdleten, men det ligger utanför den här självstudiens omfattning.

Kör den här cmdleten för att skapa din virtuella nätkonfiguration.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa sedan ditt virtuella nätverk i den nya resursgruppen med cmdleten [New-AzVirtualNetwork.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) Ange de variabler som du tidigare initierade för namn, plats och adressprefix. Använd den nätkonfiguration som du definierade i föregående steg.

Kör den här cmdleten för att skapa ditt virtuella nätverk.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Skapa den offentliga IP-adressen
Nu när det virtuella nätverket har definierats måste du konfigurera en IP-adress för anslutning till den virtuella datorn. För den här självstudien skapar du en offentlig IP-adress med dynamisk IP-adressering för att stödja Internet-anslutning. Använd [cmdleten New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) för att skapa den offentliga IP-adressen i den nya resursgruppen. Ange de variabler som du tidigare initierade för namn, plats, allokeringsmetod och DNS-domännamnsetikett.

> [!NOTE]
> Du kan definiera ytterligare egenskaper för den offentliga IP-adressen med den här cmdleten, men det ligger utanför den här ursprungliga självstudiens omfattning. Du kan också skapa en privat adress eller en adress med en statisk adress, men det ligger också utanför den här självstudiens omfattning.

Kör den här cmdleten för att skapa din offentliga IP-adress.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Skapa nätverkssäkerhetsgruppen
Skapa en nätverkssäkerhetsgrupp för att skydda den virtuella datorn och SQL Server-trafiken.

1. Skapa först en regel för nätverkssäkerhetsgrupp för RDP för att tillåta fjärrskrivbordsanslutningar.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurera en regel för nätverkssäkerhetsgrupper som tillåter trafik på TCP-port 1433. Om du gör det kan anslutningar till SQL Server via internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Skapa nätverkssäkerhetsgruppen.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Skapa nätverksgränssnittet
Du är nu redo att skapa nätverksgränssnittet för den virtuella datorn. Använd [cmdleten New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) för att skapa nätverksgränssnittet i den nya resursgruppen. Ange namn, plats, undernät och offentlig IP-adress som tidigare definierats.

Kör den här cmdleten för att skapa nätverksgränssnittet.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurera ett VM-objekt
Nu när lagrings- och nätverksresurser har definierats är du redo att definiera beräkningsresurser för den virtuella datorn.

- Ange storleken på den virtuella datorn och olika operativsystemegenskaper.
- Ange det nätverksgränssnitt som du tidigare skapade.
- Definiera blob-lagring.
- Ange operativsystemdisken.

### <a name="create-the-vm-object"></a>Skapa VM-objektet
Börja med att ange storleken på den virtuella datorn. För den här självstudien anger du en DS13. Använd [cmdleten New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) för att skapa ett konfigurerbart virtuellt datorobjekt. Ange de variabler som du tidigare initierade för namn och storlek.

Kör den här cmdleten för att skapa objektet för den virtuella datorn.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Skapa ett autentiseringsuppgifter för att innehålla namn och lösenord för de lokala administratörsuppgifterna
Innan du kan ange operativsystemegenskaperna för den virtuella datorn måste du ange autentiseringsuppgifterna för det lokala administratörskontot som en säker sträng. För att åstadkomma detta, använd [Get-Autentiseringsuppgifter](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

Kör följande cmdlet och skriv namnet och lösenordet som ska användas för det lokala administratörskontot på den virtuella datorn i fönstret PowerShell-begäran om autentiseringsuppgifter.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ange operativsystemegenskaper för den virtuella datorn
Nu är du redo att ställa in den virtuella datorns operativsystemegenskaper med cmdletet [Set-AzVMOperatingSystem.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)

- Ange typ av operativsystem som Windows.
- Kräv att [den virtuella datorn agenten](../../extensions/agent-windows.md) ska installeras.
- Ange att cmdleten aktiverar automatisk uppdatering.
- Ange de variabler som du tidigare initierade för namnet på den virtuella datorn, datornamnet och autentiseringsuppgifterna.

Kör den här cmdleten för att ange operativsystemegenskaperna för den virtuella datorn.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Lägga till nätverksgränssnittet på den virtuella datorn
Använd sedan cmdleten [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) för att lägga till nätverksgränssnittet med hjälp av variabeln som du definierade tidigare.

Kör den här cmdleten för att ställa in nätverksgränssnittet för den virtuella datorn.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ange blob-lagringsplats för disken som ska användas av den virtuella datorn
Ange sedan blob-lagringsplatsen för den virtuella datorns disk med hjälp av de variabler som du definierade tidigare.

Kör den här cmdleten för att ange blob-lagringsplatsen.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ange egenskaper för operativsystemdisken för den virtuella datorn
Ange sedan operativsystemdiskegenskaperna för den virtuella datorn med cmdleten [Set-AzVMOSDisk.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 

- Ange att operativsystemet för den virtuella datorn ska komma från en avbildning.
- Ange cachelagring till skrivskyddad (eftersom SQL Server installeras på samma disk).
- Ange de variabler som du tidigare initierade för VM-namnet och operativsystemdisken.

Kör den här cmdleten för att ange operativsystemdiskegenskaperna för den virtuella datorn.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Ange plattformsavbildning för den virtuella datorn
Det sista konfigurationssteget är att ange plattformsavbildningen för den virtuella datorn. Den här självstudien använder du den senaste CTP-avbildningen för SQL Server 2016. Använd [cmdlet set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) för att använda den här avbildningen med de variabler som du definierade tidigare.

Kör den här cmdleten för att ange plattformsavbildningen för den virtuella datorn.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Skapa den virtuella SQL-datorn
Nu när du har slutfört konfigurationsstegen är du redo att skapa den virtuella datorn. Använd [cmdleten New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) för att skapa den virtuella datorn med hjälp av de variabler som du har definierat.

> [!TIP]
> Det kan ta några minuter att skapa den virtuella datorn.

Kör den här cmdleten för att skapa din virtuella dator.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Den virtuella datorn skapas.

> [!NOTE]
> Om du får ett felmeddelande om startdiagnostik kan du ignorera det. Ett standardlagringskonto skapas för startdiagnostik eftersom det angivna lagringskontot för den virtuella datorns disk är ett premiumlagringskonto.

## <a name="install-the-sql-iaas-agent"></a>Installera SQL Iaas-agenten
Virtuella SQL Server-datorer stöder automatiska hanteringsfunktioner med [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md). Om du vill installera agenten på den nya virtuella datorn och registrera den hos resursprovidern kör du kommandot [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) när den virtuella datorn har skapats. Ange licenstypen för din VIRTUELLA SQL Server och välj mellan antingen användningsbaserad betalning eller bring-your-own-licens via [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Mer information om licensiering finns i [licensieringsmodellen](virtual-machines-windows-sql-ahb.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Stoppa eller ta bort en virtuell dator

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Följande kommando stoppar den virtuella datorn men lämnar den tillgänglig för framtida bruk.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn med kommandot **Remove-AzResourceGroup**. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet.

## <a name="example-script"></a>Exempelskript
Följande skript innehåller det fullständiga PowerShell-skriptet för den här självstudien. Det förutsätter att du redan har konfigurerat Azure-prenumerationen som ska användas med kommandona **Connect-AzAccount** och **Select-AzSubscription.**

```powershell
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
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Nästa steg
När den virtuella datorn har skapats kan du:

- Ansluta till den virtuella datorn med RDP
- Konfigurera SQL Server-inställningar i portalen för den virtuella datorn, inklusive:
   - [Lagringsinställningar](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatiserade hanteringsuppgifter](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurera anslutning](virtual-machines-windows-sql-connect.md)
- Ansluta klienter och program till den nya SQL Server-instansen

