---
title: Skapa och konfigurera ett nyckel valv för Azure Disk Encryption
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 90306c55d976670f432d146d94764c4d90b8667d
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828502"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckel valv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter.  Läs mer om nyckelvalv [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md) och [säkra ditt nyckelvalv](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md) .

Att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption omfattar tre steg:

1. Skapa en resurs grupp, om det behövs.
2. Skapar ett nyckel valv. 
3. Ställer in avancerade åtkomst principer för nyckel valvet.

Dessa steg illustreras i följande snabb starter:

- [Skapa och kryptera en virtuell Linux-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Skapa och kryptera en virtuell Linux-dator med Azure PowerShell](disk-encryption-cli-quickstart.md)

Du kan också, om du vill, skapa eller importera en nyckel krypterings nyckel (KEK).

> [!Note]
> Stegen i den här artikeln är automatiserade i [skripten för Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) och [Azure Disk Encryption krav på PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och Anslut till Azure

Stegen i den här artikeln kan utföras med [Azure CLI](/cli/azure/), [Azure PowerShell AZ-modulen](/powershell/azure/overview)eller [Azure Portal](https://portal.azure.com). 

Även om portalen är tillgänglig via webbläsaren kan Azure CLI och Azure PowerShell kräva lokal installation. Se [Azure Disk Encryption for Linux: Installera verktyg @ no__t-0 om du vill ha mer information.

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör detta genom att [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)eller ange dina autentiseringsuppgifter till Azure Portal när du uppmanas till det.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

*Om du redan har en resurs grupp kan du gå vidare till [skapa ett nyckel valv](#create-a-key-vault).*

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

Skapa en resurs grupp med hjälp av [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI Command, kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell eller från [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

*Om du redan har ett nyckel valv kan du gå vidare till [Ange avancerade åtkomst principer för Key Vault](#set-key-vault-advanced-access-policies).*

Skapa ett nyckel valv med hjälp [av AZ](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) -kommandot för att skapa Azure CLI, kommandot [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure PowerShell, [Azure Portal](https://portal.azure.com)eller en [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> För att säkerställa att krypterings hemligheter inte korsar regionala gränser, kräver Azure Disk Encryption Key Vault och de virtuella datorerna ska samplaceras i samma region. Skapa och Använd en Key Vault som finns i samma region som de virtuella datorer som ska krypteras. 

Varje Key Vault måste ha ett unikt namn. Ersätt < ditt-unika-nyckel-valv > med namnet på nyckel valvet i följande exempel.

### <a name="azure-cli"></a>Azure CLI

När du skapar ett nyckel valv med Azure CLI lägger du till flaggan "--enabled for Disk Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

När du skapar ett nyckel valv med Azure PowerShell lägger du till flaggan "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-mall

Du kan också skapa ett nyckel valv med hjälp av [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klicka på Azure snabbstarts-mall **distribuera till Azure**.
2. Välj prenumeration, resurs grupp, resurs grupp plats, Key Vault namn, objekt-ID, juridiska villkor och avtal och klicka sedan på **köp**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Ange avancerade åtkomst principer för Key Vault

Azure-plattformen behöver åtkomst till krypteringsnycklar och hemligheter i ditt nyckelvalv och gör dem tillgängliga för den virtuella datorn för start och dekryptera volymerna. 

Om du inte har aktiverat nyckel valvet för disk kryptering, distribution eller mall distribution vid tidpunkten för skapandet (som visas i föregående steg) måste du uppdatera dess avancerade åtkomst principer.  

### <a name="azure-cli"></a>Azure CLI

Använd [az keyvault update](/cli/azure/keyvault#az-keyvault-update) vill aktivera diskkryptering för key vault. 

 - **Aktivera Key Vault för disk kryptering:** Enabled-for-Disk-Encryption krävs. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution vid behov:** Gör att Microsoft. Compute Resource-providern kan hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till när en resurs skapas, till exempel när en virtuell dator skapas.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault vid distribution av mallar, om det behövs:** Tillåt Resource Manager att hämta hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Använd Key Vault PowerShell-cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att aktivera disk kryptering för nyckel valvet.

  - **Aktivera Key Vault för disk kryptering:** EnabledForDiskEncryption krävs för Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution vid behov:** Gör att Microsoft. Compute Resource-providern kan hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till när en resurs skapas, till exempel när en virtuell dator skapas.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Aktivera Key Vault vid distribution av mallar, om det behövs:** Gör det möjligt för Azure Resource Manager att hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till i en mall distribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Välj ditt nyckel valv, gå till **åtkomst principer**och **Klicka om du vill visa avancerade åtkomst principer**.
2. Markera rutan **ge åtkomst till Azure Disk Encryption för volymkryptering**.
3. Välj **Aktivera åtkomst till Azure Virtual Machines för distribution av** och/eller **Aktivera åtkomst till Azure Resource Manager för malldistribution**, om det behövs. 
4. Klicka på **Spara**.

    ![Azure-nyckelvalv avancerade åtkomstprinciper](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Konfigurera en nyckel krypterings nyckel (KEK)

Om du vill använda en krypteringsnyckel nyckel (KEK) för ett extra lager av säkerhet för krypteringsnycklar, lägger du till en KEK till ditt nyckelvalv. När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault.

Du kan generera en ny KEK med hjälp av kommandot för att skapa en Azure CLI- [AZ](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , Azure PowerShell [Add-AzKeyVaultKey-](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet eller [Azure Portal](https://portal.azure.com/). Du måste generera en RSA-nyckel typ. Azure Disk Encryption har ännu inte stöd för att använda Elliptic kurv nycklar.

Du kan istället importera en KEK från din lokala nyckel hanterings HSM. Mer information finns i [dokumentation om Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). 

KEK-URL: er för nyckel valvet måste ha versions hantering. Azure tillämpar den här begränsningen för versionshantering. Giltigt hemlighet och KEK URL: er finns i följande exempel:

* Exempel på en giltig hemlig URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exempel på en giltig KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption stöder inte att ange portnummer som en del av key vault-hemligheter och KEK URL: er. Exempel på URL: er inte stöds och stöds key vault finns i följande exempel:

  * Acceptabel URL för nyckel valv: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Oacceptabel Key Vault-URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Använd Azure CLI- [AZ](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) för att skapa en ny KEK och lagra den i ditt nyckel valv.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Du kan istället importera en privat nyckel med hjälp av Azure CLI- [AZ för nyckel import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

I båda fallen anger du namnet på din KEK till Azure CLI- [AZ VM-kryptering Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-Encryption-Key-parameter. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Använd cmdleten Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) för att generera en ny KEK och lagra den i ditt nyckel valv.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Du kan istället importera en privat nyckel med hjälp av kommandot Azure PowerShell [AZ Key Vault Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

I båda fallen ska du ange ID: t för ditt KEK Key Vault och URL: en för din KEK till parametrarna Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId och-KeyEncryptionKeyUrl. Observera att det här exemplet förutsätter att du använder samma nyckel valv för både disk krypterings nyckeln och KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Lär dig [Azure Disk Encryption scenarier på virtuella Linux-datorer](disk-encryption-linux.md)
- Lär dig hur du [felsöker Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Läs [Azure Disk Encryption exempel skript](disk-encryption-sample-scripts.md)
