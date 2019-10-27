---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cffb9efaf828b3793133143e97c0fc87f840df42
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966299"
---
Azure Managed disks krypterar automatiskt dina data som standard när de sparas i molnet. Kryptering på Server sidan skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data i Azure Managed disks krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel.   

Kryptering påverkar inte prestandan hos hanterade diskar. Krypteringen kostar inget extra.

Mer information om de kryptografiska modulerna underliggande Azure Managed disks finns i [Cryptography-API: nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Du kan lita på plattforms hanterade nycklar för kryptering av din hanterade disk, eller så kan du hantera kryptering med hjälp av dina egna nycklar (offentlig för hands version). Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla data i Managed disks. 

I följande avsnitt beskrivs de olika alternativen för nyckel hantering i större detalj.

## <a name="platform-managed-keys"></a>Plattforms hanterade nycklar

Som standard använder hanterade diskar plattforms hanterade krypterings nycklar. Från och med den 10 juni 2017 krypteras alla nya hanterade diskar, ögonblicks bilder, bilder och nya data som skrivs till befintliga hanterade diskar automatiskt i vila med plattforms hanterade nycklar. 

## <a name="customer-managed-keys-public-preview"></a>Kundhanterade nycklar (offentlig för hands version)

Du kan välja att hantera kryptering på nivån för varje hanterad disk med dina egna nycklar. Kryptering på Server sidan för Managed disks med Kundhanterade nycklar ger en integrerad upplevelse med Azure Key Vault. Du kan antingen importera [dina RSA-nycklar](../articles/key-vault/key-vault-hsm-protected-keys.md) till Key Vault eller generera nya RSA-nycklar i Azure Key Vault. Azure Managed disks hanterar kryptering och dekryptering på ett helt transparent sätt med hjälp av [Kuvert kryptering](../articles/storage/common/storage-client-side-encryption.md#encryption-via-the-envelope-technique). Den krypterar data med hjälp av en [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Du måste bevilja åtkomst i Key Vault för att kunna använda dina nycklar för att kryptera och dekryptera DEK. Detta gör att du får fullständig kontroll över dina data och nycklar. Du kan när som helst inaktivera dina nycklar eller återkalla åtkomsten till hanterade diskar. Du kan också granska krypterings nyckel användningen med Azure Key Vault övervakning så att endast hanterade diskar eller andra betrodda Azure-tjänster får åtkomst till dina nycklar.

Följande diagram visar hur hanterade diskar använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med hjälp av kundhanterad nyckel:

![Hanterade diskar kund hanterade nycklar arbets flöde](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


I följande lista beskrivs de numrerade stegen i diagrammet:

1. En Azure Key Vault administratör skapar nyckel valvs resurser.
1. Key Vault-administratören importerar antingen sina RSA-nycklar för att Key Vault eller generera nya RSA-nycklar i Key Vault.
1. Administratören skapar en instans av en resurs för disk krypterings uppsättning och anger ett Azure Key Vault-ID och en nyckel-URL. Disk krypterings uppsättning är en ny resurs som introducerats för att förenkla nyckel hanteringen för hanterade diskar. 
1. När en disk krypterings uppsättning skapas, skapas en [systemtilldelad hanterad identitet](../articles/active-directory/managed-identities-azure-resources/overview.md) i Azure Active Directory (AD) och associeras med disk krypterings uppsättningen. 
1. Azure Key Vault-administratören ger sedan den hanterade identiteten behörighet att utföra åtgärder i nyckel valvet.
1. En VM-användare skapar diskar genom att associera dem med disk krypterings uppsättningen. Den virtuella dator användaren kan också aktivera kryptering på Server sidan med Kundhanterade nycklar för befintliga resurser genom att associera dem med disk krypterings uppsättningen. 
1. Managed disks använder den hanterade identiteten för att skicka begär anden till Azure Key Vault.
1. Vid läsning eller skrivning av data skickar Managed disks begär anden till Azure Key Vault att kryptera (omslutning) och dekryptera (unwrap) data krypterings nyckeln för att utföra kryptering och dekryptering av data. 

Information om hur du återkallar åtkomst till Kundhanterade nycklar finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i lagrings kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Storage.

### <a name="supported-scenarios-and-restrictions"></a>Scenarier och begränsningar som stöds

Under för hands versionen stöds endast följande scenarier:

- Skapa en virtuell dator (VM) från en Azure Marketplace-avbildning och kryptera OS-disken med kryptering på Server sidan med Kundhanterade nycklar.
- Skapa en anpassad avbildning som är krypterad med kryptering på Server sidan och Kundhanterade nycklar.
- Skapa en virtuell dator från en anpassad avbildning och kryptera OS-disken med kryptering på Server sidan och Kundhanterade nycklar.
- Skapa data diskar som har krypterats med kryptering på Server sidan och Kundhanterade nycklar.
- Skapa ögonblicks bilder som är krypterade med kryptering på Server sidan och Kundhanterade nycklar.
- Skapa skalnings uppsättningar för virtuella datorer som är krypterade med kryptering på Server sidan och Kundhanterade nycklar.

Förhands granskningen har också följande begränsningar:

- Endast tillgängligt i USA, västra centrala.
- Diskar som har skapats från anpassade avbildningar som krypteras med kryptering på Server sidan och Kundhanterade nycklar måste krypteras med samma Kundhanterade nycklar och måste finnas i samma prenumeration.
- Ögonblicks bilder som har skapats från diskar som är krypterade med kryptering på Server sidan och Kundhanterade nycklar måste vara krypterade med samma Kundhanterade nycklar.
- Anpassade avbildningar som krypteras med kryptering på Server sidan och Kundhanterade nycklar kan inte användas i det delade avbildnings galleriet.
- Ditt Key Vault måste vara i samma prenumeration och region som dina Kundhanterade nycklar.
- Diskar, ögonblicks bilder och bilder som är krypterade med Kundhanterade nycklar kan inte flyttas till en annan prenumeration.

### <a name="setting-up-your-azure-key-vault"></a>Konfigurera din Azure Key Vault

1.  Skapa en instans av Azure Key Vault och krypterings nyckel.

    När du skapar Key Vault-instansen måste du aktivera mjuk borttagning och tömning av skydd. Mjuk borttagning garanterar att Key Vault innehåller en borttagen nyckel för en viss kvarhållningsperiod (90 dag standard). Med rensnings skyddet går det inte att ta bort en borttagen nyckel förrän kvarhållningsperioden upphörde. Dessa inställningar skyddar dig från att förlora data på grund av oavsiktlig borttagning. De här inställningarna är obligatoriska när du använder en Key Vault för att kryptera hanterade diskar.

    ```powershell
    $keyVault = New-AzKeyVault -Name myKeyVaultName ` 
    -ResourceGroupName myRGName ` 
    -Location westcentralus ` 
    -EnableSoftDelete ` 
    -EnablePurgeProtection 
     
    $key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName ` 
    -Name myKeyName ` 
    -Destination Software `  
    ```

1.  Skapa en instans av en DiskEncryptionSet. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myRGName ` 
      -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" ` 
      -diskEncryptionSetName "myDiskEncryptionSet1" ` 
      -keyVaultId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.KeyVault/vaults/myKeyVaultName" ` 
      -keyVaultKeyUrl "https://myKeyVaultName.vault.azure.net/keys/myKeyName/403445136dee4a57af7068cab08f7d42" ` 
      -region "WestCentralUS"
    ```

1.  Ge DiskEncryptionSet-resursen åtkomst till nyckel valvet.

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy ` 
        -VaultName $keyVault.VaultName ` 
        -ObjectId $identity.Id ` 
        -PermissionsToKeys wrapkey,unwrapkey,get 
     
    New-AzRoleAssignment ` 
        -ObjectId $identity.Id ` 
        -RoleDefinitionName "Reader" ` 
        -ResourceName $keyVault.VaultName ` 
        -ResourceType "Microsoft.KeyVault/vaults" ` 
        -ResourceGroupName myRGName `  
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys-via-a-resource-manager-template"></a>Skapa en virtuell dator med en Marketplace-avbildning, kryptera operativ system och data diskar med Kundhanterade nycklar via en Resource Manager-mall

```
$password=ConvertTo-SecureString -String "myVMPassword" `
  -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json" `
  -virtualMachineName "myVMName" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.Compute/diskEncryptionSets/myDiskEncryptionSet1" `
  -region "westcentralus" 
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Skapa en tom disk som är krypterad med kryptering på Server sidan med Kundhanterade nycklar och koppla den till en virtuell dator

```PowerShell
$vmName = "yourVMName"
$rgName = "yourRGName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = "30"
$diskEncryptionSetId = "/subscriptions/<subscriptionID>/resourceGroups/yourRGName/providers/Microsoft.Compute/diskEncryptionSets/<yourDiskEncryptionSetName>"
$region = "westcentralus"
$diskLUN = 1

New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json" `
  -diskName $diskName `
  -diskSkuName $diskSKU `
  -dataDiskSizeInGb $diskSizeinGiB `
  -diskEncryptionSetId $diskEncryptionSetId `
  -region $region 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
```


> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till hanterade diskar till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../articles/active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kryptering på Server sidan jämfört med Azure Disk Encryption

[Azure Disk Encryption](../articles/security/fundamentals/azure-disk-encryption-vms-vmss.md) utnyttjar [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -funktionen i Windows och funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn.  Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
