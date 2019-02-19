---
title: Självstudier – Skydda en Windows-webbserver med SSL-certifikat i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du använder Azure PowerShell för att skydda en virtuell Windows-dator som kör IIS-webbservern med SSL-certifikat som lagras i Azure Key Vault.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c6f51164904ca51e66b9ce112cf9aec4324812c9
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113965"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>Självstudier: Skydda en webbserver på en virtuell Windows-dator i Azure med SSL-certifikat som lagras i Key Vault

När du ska skydda dina webbservrar kan du använda ett SSL-certifikat (Secure Sockets Layer) för att kryptera webbtrafik. SSL-certifikat går att lagra i Azure Key Vault och tillåter säker distribuering av certifikat till virtuella Windows-datorer i Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera eller ladda upp ett certifikat till Key Vault
> * Skapa en virtuell dator och installera IIS-webbservern
> * Mata in certifikatet i den virtuella datorn och konfigurera IIS med en SSL-bindning

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="overview"></a>Översikt
Azure Key Vault skyddar kryptografiska nycklar och hemligheter, som certifikat och lösenord. Key Vault förenklar certifikathanteringen och låter dig behålla kontrollen över nycklar som kommer åt certifikaten. Du kan skapa ett självsignerat certifikat i Key Vault eller ladda upp ett befintligt, betrott certifikat som du redan äger.

Istället för att använda en anpassad VM-avbildning med inbyggda certifikat matar du in certifikat till en virtuell dator som körs. Den här processen garanterar att de mest uppdaterade certifikaten är installerade på en webbserver under distributionen. Om du förnyar eller ersätter ett certifikat behöver du inte skapa en ny anpassad VM-avbildning. De senaste certifikaten matas ut automatiskt när du skapar fler virtuella datorer. Under hela processen lämnar certifikaten aldrig Azure-plattformen och exponeras inte i något skript, kommandoradshistorik eller mall.


## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault
Innan du kan skapa ett Key Vault och certifikat skapar du en resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupSecureWeb* på platsen *East US* (Östra USA):

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Skapa sedan ett Key Vault med [New-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault). För varje Key Vault krävs ett unikt namn som ska skrivas med gemener. Ersätt `mykeyvault` i följande exempel med ditt eget unika Key Vault-namn:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generera ett certifikat och lagra det i Key Vault
För produktion bör du importera ett giltigt certifikat som är signerat av en betrodd provider med [Import-AzureKeyVaultCertificate](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). För den här självstudien visar följande exempel hur du kan generera ett självsignerat certifikat med [Add-AzureKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) som använder standardcertifikatprincipen från [New-AzureKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```azurepowershell-interactive
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
Ange ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nu kan du skapa den virtuella datorn med [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVM* på platsen *EastUS* (Östra USA). Stödnätverksresurser skapas, om de inte redan finns. För att tillåta säker webbtrafik öppnar cmdleten även port *443*.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Det tar några minuter att skapa den virtuella datorn. I det sista steget används tillägget för anpassat skript i Azure för att installera IIS-webbservern med [Set-AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Lägga till ett certifikat till en virtuell dator från Key Vault
Om du vill lägga till certifikatet från Key Vault till en virtuell dator hämtar du ID:t för certifikatet med [Get-AzureKeyVaultSecret](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Lägg till certifikatet till den virtuella datorn med [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurera IIS för att använda certifikatet
Använda tillägget för anpassat skript igen med [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) för att uppdatera IIS-konfigurationen. Den här uppdateringen tillämpar certifikatet som matats in från Key Vault till ISS och konfigurerar webbindningen:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testa den säkra webbappen
Hämta den offentliga IP-adressen för den virtuella datorn med [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). I följande exempel hämtas IP-adressen för `myPublicIP` som skapades tidigare:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Nu kan du öppna en webbläsare och ange `https://<myPublicIP>` i adressfältet. Om du använde ett självsignerat certifikat och vill acceptera säkerhetsvarningen väljer du **Information** och sedan **Fortsätt till webbsidan**:

![Acceptera webbläsarens säkerhetsvarning](./media/tutorial-secure-web-server/browser-warning.png)

Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

![Visa skyddad IIS-webbplats som körs](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skyddat en IIS-webbserver med ett SSL-certifikat som lagras i Azure Key Vault. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera eller ladda upp ett certifikat till Key Vault
> * Skapa en virtuell dator och installera IIS-webbservern
> * Mata in certifikatet i den virtuella datorn och konfigurera IIS med en SSL-bindning

Klicka på den här länken om du vill se inbyggda skriptexempel för virtuella datorer.

> [!div class="nextstepaction"]
> [Skriptexempel för virtuella Windows-datorer](./powershell-samples.md)
