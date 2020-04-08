---
title: ta med fil
description: ta med fil
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: f2133524f57576c4646de9d4bf7f37b573a77e6d
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813746"
---
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

*Om du redan har en resursgrupp kan du hoppa till [Skapa ett nyckelvalv](#create-a-key-vault).*

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

Skapa en resursgrupp med kommandot [Az-gruppen create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI, kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell eller från [Azure-portalen](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

*Om du redan har ett nyckelvalv kan du hoppa till [Ange avancerade åtkomstprinciper för nyckelvalvet](#set-key-vault-advanced-access-policies).*

Skapa ett nyckelvalv med kommandot [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI, kommandot [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell, [Azure-portalen](https://portal.azure.com)eller en [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> För att säkerställa att krypteringshemligheter inte korsar regionala gränser kräver Azure Disk Encryption nyckelvalvet och de virtuella datorerna att vara samlokala i samma region och samma prenumeration. Skapa och använd ett nyckelvalv som finns i samma region som de virtuella datorer som ska krypteras. 

Varje Key Vault måste ha ett unikt namn. Ersätt <ditt unika nyckelval-namn> med namnet på nyckelvalvet i följande exempel.

### <a name="azure-cli"></a>Azure CLI

När du skapar ett nyckelvalv med Azure CLI lägger du till flaggan "--enabled-for-disk-encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

När du skapar ett nyckelvalv med Azure PowerShell lägger du till flaggan "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-mall

Du kan också skapa ett nyckelvalv med hjälp av [resource manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klicka på Distribuera till **Azure**i snabbstartsmallen i Azure .
2. Välj prenumeration, resursgrupp, resursgruppsplats, Key Vault-namn, Objekt-ID, juridiska termer och avtal och klicka sedan på **Inköp**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Ställa in avancerade åtkomstprinciper för nyckelvalvet

Azure-plattformen behöver åtkomst till krypteringsnycklarna eller hemligheterna i nyckelvalvet för att göra dem tillgängliga för den virtuella datorn för att starta och dekryptera volymerna. 

Om du inte har aktiverat nyckelvalvet för diskkryptering, distribution eller malldistribution vid tidpunkten för skapandet (vilket visas i föregående steg) måste du uppdatera dess avancerade åtkomstprinciper.  

### <a name="azure-cli"></a>Azure CLI

Använd [az keyvault-uppdatering](/cli/azure/keyvault#az-keyvault-update) för att aktivera diskkryptering för nyckelvalvet. 

 - **Aktivera Key Vault för diskkryptering:** Aktiverad-för-disk-kryptering krävs. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution om det behövs:** Gör det möjligt för Microsoft.Compute-resursprovidern att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till när du skapar en virtuell dator.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault för malldistribution om det behövs:** Tillåt att Resurshanteraren hämtar hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Använd nyckelvalvet PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att aktivera diskkryptering för nyckelvalvet.

  - **Aktivera Key Vault för diskkryptering:** EnabledForDiskEncryption krävs för Azure Disk-kryptering.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution om det behövs:** Gör det möjligt för Microsoft.Compute-resursprovidern att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till när du skapar en virtuell dator.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Aktivera Key Vault för malldistribution om det behövs:** Gör det möjligt för Azure Resource Manager att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras i en malldistribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Välj nyckelvalv, gå till **Åtkomstprinciper**och **Klicka för att visa avancerade åtkomstprinciper**.
2. Välj rutan **Aktivera åtkomst till Azure Disk Encryption för volymkryptering**.
3. Välj **Aktivera åtkomst till virtuella Azure-datorer för distribution** och/eller Aktivera åtkomst till Azure Resource Manager för **malldistribution**, om det behövs. 
4. Klicka på **Spara**.

    ![Avancerade åtkomstprinciper för Azure key vault](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Konfigurera en nyckelkrypteringsnyckel (KEK)

Om du vill använda en nyckelkrypteringsnyckel (KEK) för ett extra säkerhetslager för krypteringsnycklar lägger du till en KEK i nyckelvalvet. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption den nyckeln för att radbrytas krypteringshemligheterna innan du skriver till Key Vault.

Du kan generera en ny KEK med kommandot Azure CLI [az keyvault key create,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet eller [Azure-portalen](https://portal.azure.com/). Du måste generera en RSA-nyckeltyp. Azure Disk Encryption stöder ännu inte elliptic curve-nycklar.

Du kan i stället importera en KEK från din lokala nyckelhantering HSM. Mer information finns i Dokumentation för [Nyckelvalv](/azure/key-vault/key-vault-hsm-protected-keys).

Kek-url:erna för nyckelvalv måste vara versionsinrerade. Azure tillämpar den här begränsningen av versionshantering. För giltiga hemliga och KEK-url:er finns i följande exempel:

* Exempel på en giltig hemlig URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exempel på en giltig KEK-URL:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption stöder inte att ange portnummer som en del av nyckelvalvshemligheter och KEK-url:er. Exempel på url:er som inte stöds och stöds av nyckelvalv finns i följande exempel:

  * Url för godtagbart nyckelvalv:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Url för nyckelvalvet för oacceptabla nyckelvalv:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Använd azure CLI [az keyvault-nyckeln skapa](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) kommandot för att generera en ny KEK och lagra den i ditt nyckelvalv.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

Du kan i stället importera en privat nyckel med kommandot Azure CLI [az keyvault key import:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

I båda fallen anger du namnet på din KEK till Azure CLI [az vm-kryptering aktivera](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key parameter. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Använd cmdleten Azure PowerShell [Add-AzKeyVaultKey för](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) att generera en ny KEK och lagra den i nyckelvalvet.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Du kan i stället importera en privat nyckel med kommandot Azure [PowerShell az keyvault key import.](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

I båda fallen anger du ID:n för ditt KEK-nyckelvalv och url:en för kek till azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId och -KeyEncryptionKeyUrl parametrar. Observera att det här exemplet förutsätter att du använder samma nyckelvalv för både diskkrypteringsnyckeln och KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
