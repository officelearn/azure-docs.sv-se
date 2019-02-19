---
title: Skapa en SQL Server Windows-VM med Azure PowerShell | Microsoft Docs
description: Den här kursen visar hur du skapar virtuell Windows SQL Server 2017-dator med Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 24dfc9602f7329b4ea56db2257f29f5711510d22
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977804"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Snabbstart: Skapa en virtuell Windows SQL Server-dator med Azure PowerShell

Den här snabbstarten beskriver hur du skapar en virtuell SQL Server-dator med Azure PowerShell.

> [!TIP]
> I snabbstarten finns en sökväg för snabb etablering och anslutning till en SQL-VM. Mer information om andra Azure PowerShell-alternativ för att skapa virtuella SQL-datorer finns i [Etableringsguide för virtuella SQL Server-datorer med Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Skaffa en Azure-prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a id="powershell"></a> Skaffa Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Konfigurera PowerShell

1. Öppna PowerShell och upprätta åtkomst till ditt Azure-konto genom att köra kommandot **Connect-AzAccount**.

   ```PowerShell
   Connect-AzAccount
   ```

1. Du bör se en skärm där du kan ange dina autentiseringsuppgifter. Använd samma e-postadress och lösenord som du använder för att logga in på Azure Portal.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Definiera en variabel med ett unikt resursgruppsnamn. För att förenkla resten av snabbstarten används i återstående kommandon det här namnet som grund för andra resursnamn.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Definiera en plats för en Azure-region som är mål för alla VM-resurser.

   ```PowerShell
   $Location = "East US"
   ```

1. Skapa resursgruppen.

   ```PowerShell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Konfigurera nätverksinställningar

1. Skapa ett virtuellt nätverk, undernät och offentlig IP-adress. Dessa resurser används för att skapa nätverksanslutning till den virtuella datorn och ansluta den till internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Skapa en nätverkssäkerhetsgrupp. Konfigurera regler för att tillåta anslutning till fjärrskrivbord (RDP) och SQL Server-anslutningar.

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Skapa nätverksgränssnittet.

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Skapa den virtuella SQL-datorn

1. Definiera dina autentiseringsuppgifter för att logga in på den virtuella datorn. Användarnamnet är ”azureadmin”. Se till att ändra \<lösenordet> innan du kör kommandot.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Skapa ett konfigurationsobjekt för den virtuella datorn och sedan skapa den virtuella datorn. Följande kommando skapar en SQL Server 2017 Developer Edition VM på Windows Server 2016.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 | `
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Det tar flera minuter att skapa den virtuella datorn.

## <a name="install-the-sql-iaas-agent"></a>Installera SQL Iaas-agenten

För att få portalintegrering och SQL VM-funktioner måste du installera [tillägget SQL Server IaaS-agent](virtual-machines-windows-sql-server-agent-extension.md). När den nya virtuella datorn har skapats kan du installera agenten på den genom att köra följande kommando.

   ```PowerShell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Fjärrskrivbord på den virtuella datorn

1. Använd följande kommando för att hämta den offentliga IP-adressen för den nya virtuella datorn.

   ```PowerShell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Skicka sedan den returnerade IP-adressen som kommandoradsparameter till **mstsc** för att starta en fjärrskrivbordssession på den nya virtuella datorn.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Ange autentiseringsuppgifter för ett annat konto när du tillfrågas om autentiseringsuppgifter. Ange användarnamnet med ett föregående omvänt snedstreck (till exempel `\azureadmin`), och det lösenord som du angav tidigare i den här snabbstarten.

## <a name="connect-to-sql-server"></a>Ansluta till SQL Server

1. När du har loggat in i fjärrskrivbordssessionen kan du starta **SQL Server Management Studio 2017** från Start-menyn.

1. Behåll standardvärden i dialogrutan **Anslut till server**. Servernamnet är namnet på den virtuella datorn. Autentisering har angetts till **Windows-autentisering**. Välj **Anslut**.

Du är nu ansluten till SQL Server lokalt. Om du vill ansluta via en fjärranslutning måste du [konfigurera anslutningen](virtual-machines-windows-sql-connect.md) från portalen eller manuellt.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Följande kommando stoppar den virtuella datorn men lämnar den tillgänglig för framtida bruk.

```PowerShell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn med kommandot **Remove-AzResourceGroup**. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet.

## <a name="next-steps"></a>Nästa steg

Med den här snabbstarten skapade du en virtuell SQL Server 2017-dator med Azure PowerShell. Mer information om hur du migrerar data till den nya SQL-servern finns i följande artikel.

> [!div class="nextstepaction"]
> [Migrera en databas till en virtuell SQL-dator](virtual-machines-windows-migrate-sql.md)
