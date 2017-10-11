---
title: Skydda IIS med SSL-certifikat i Azure | Microsoft Docs
description: "Lär dig att skydda IIS-webbserver med SSL-certifikat på en Windows-dator i Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6567853e9ef3cad63595dc0afe7a793bdc5d972c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Skydda IIS-webbserver med SSL-certifikat på en Windows-dator i Azure
Ett certifikat senare SSL (Secure Sockets) kan användas för kryptering av webbtrafik säker webbserver. Dessa SSL-certifikat kan lagras i Azure Key Vault och tillåta säker distribution av certifikat till Windows-datorer (VM) i Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera och överföra ett certifikat till Nyckelvalvet
> * Skapa en virtuell dator och installera IIS-webbservern
> * Mata in certifikatet i den virtuella datorn och konfigurera IIS med en SSL-bindning

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).


## <a name="overview"></a>Översikt
Azure Key Vault skyddar kryptografiska nycklar och hemligheter, dessa certifikat eller lösenord. Key Vault hjälper till att förenkla hanteringen för certifikatet och gör att du kan behålla kontrollen över nycklar som har åtkomst till dessa certifikat. Du kan skapa ett självsignerat certifikat i Nyckelvalvet eller ladda upp en befintlig, betrodda certifikat som du redan äger.

I stället för att använda en anpassad VM-avbildning som inkluderar certifikat inbyggd-modulen Certifikat Injicera i en aktiv virtuell dator. Den här processen säkerställer att de senaste certifikat installeras på en webbserver under distributionen. Om du vill förnya eller ersätta ett certifikat, behöver du också skapa en ny anpassad VM-avbildning. Senaste certifikaten är automatiskt matas in när du skapar ytterligare virtuella datorer. Under hela processen certifikat aldrig lämna Azure-plattformen eller exponeras i ett skript, kommandoradsverktyget historik eller mall.


## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault
Innan du kan skapa ett Nyckelvalv och certifikat, skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupSecureWeb* i den *östra USA* plats:

```powershell
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Skapa sedan ett Nyckelvalv med [ny AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Varje Key Vault kräver ett unikt namn och bör vara alla gemen. Ersätt `<mykeyvault>` i följande exempel med dina egna unika Key Vault-namn:

```powershell
$keyvaultName="<mykeyvault>"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generera ett certifikat och lagra i Key Vault
För produktion, ska du importera ett giltigt certifikat som signerats av en betrodd provider med [importera AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). Den här självstudiekursen i följande exempel visas hur du kan skapa ett självsignerat certifikat med [Lägg till AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) som använder standardprincipen för certifikat från [ Nya AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```powershell
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Ange en administratör användarnamn och lösenord för den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Nu kan du skapa den virtuella datorn med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapar krävs virtuella nätverkskomponenter, OS-konfigurationen och sedan skapar en virtuell dator med namnet *myVM*:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myVnet" `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleRDP"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 443
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleWWW"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name "myNic" `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2" | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2016-Datacenter" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Det tar några minuter för den virtuella datorn skapas. Det sista steget använder tillägget för Azure anpassat skript för att installera IIS-webbserver med [Set AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Lägga till ett certifikat till en virtuell dator från Nyckelvalvet
Om du vill lägga till certifikatet från Nyckelvalvet till en virtuell dator, Hämta ID för certifikatet med [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Lägga till certifikatet till den virtuella datorn med [Lägg till AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```powershell
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurera IIS att använda certifikat
Använda tillägget för anpassat skript igen med [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) att uppdatera konfigurationen av IIS. Den här uppdateringen gäller certifikatet matas in från Nyckelvalvet till IIS och konfigurerar web-bindning:

```powershell
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testa det säkra webbprogrammet
Hämta den offentliga IP-adressen på den virtuella datorn med [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtar IP-adressen för `myPublicIP` skapade tidigare:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIP" | select "IpAddress"
```

Nu kan du öppna en webbläsare och ange `https://<myPublicIP>` i adressfältet. Om du vill acceptera säkerhetsmeddelande om du använder ett självsignerat certifikat, Välj **information** och sedan **går du vidare till webbsidan**:

![Acceptera web Säkerhetsvarning för webbläsare](./media/tutorial-secure-web-server/browser-warning.png)

Skyddad IIS-webbplatsen visas som i följande exempel:

![Visa körs säker IIS-webbplats](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skyddas en IIS-webbserver med ett SSL-certifikat som lagras i Azure Key Vault. Du har lärt dig hur till:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera och överföra ett certifikat till Nyckelvalvet
> * Skapa en virtuell dator och installera IIS-webbservern
> * Mata in certifikatet i den virtuella datorn och konfigurera IIS med en SSL-bindning

Den här länken om du vill se förskapad virtuella skriptexempel.

> [!div class="nextstepaction"]
> [Windows skriptexempel för virtuell dator](./powershell-samples.md)