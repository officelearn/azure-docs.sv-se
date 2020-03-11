---
title: Kryptering på Server sidan av Azure Managed Disks – Azure CLI
description: Azure Storage skyddar dina data genom att kryptera dem i vila innan du sparar dem i lagrings kluster. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina hanterade diskar, eller så kan du använda Kundhanterade nycklar för att hantera kryptering med dina egna nycklar.
author: roygara
ms.date: 01/13/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 495bdcfb619ff17a4a4b074fa673c5d2fb185730
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970532"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Kryptering på Server sidan av Azure Managed disks

Azure Managed disks krypterar automatiskt dina data som standard när de sparas i molnet. Kryptering på Server sidan skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data i Azure Managed disks krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel.   

Kryptering påverkar inte prestandan hos hanterade diskar. Krypteringen kostar inget extra.

Mer information om de kryptografiska modulerna underliggande Azure Managed disks finns i [Cryptography-API: nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Du kan förlita dig på plattforms hanterade nycklar för kryptering av din hanterade disk, eller så kan du hantera kryptering med hjälp av dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla data i Managed disks. 

I följande avsnitt beskrivs de olika alternativen för nyckel hantering i större detalj.

## <a name="platform-managed-keys"></a>Plattforms hanterade nycklar

Som standard använder hanterade diskar plattforms hanterade krypterings nycklar. Från och med den 10 juni 2017 krypteras alla nya hanterade diskar, ögonblicks bilder, bilder och nya data som skrivs till befintliga hanterade diskar automatiskt i vila med plattforms hanterade nycklar. 

## <a name="customer-managed-keys"></a>Kundhanterade nycklar

Du kan välja att hantera kryptering på nivån för varje hanterad disk med dina egna nycklar. Kryptering på Server sidan för Managed disks med Kundhanterade nycklar ger en integrerad upplevelse med Azure Key Vault. Du kan antingen importera [dina RSA-nycklar](../../key-vault/key-vault-hsm-protected-keys.md) till Key Vault eller generera nya RSA-nycklar i Azure Key Vault. Azure Managed disks hanterar kryptering och dekryptering på ett helt transparent sätt med hjälp av [Kuvert kryptering](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Den krypterar data med hjälp av en [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Du måste bevilja åtkomst till hanterade diskar i Key Vault för att kunna använda dina nycklar för att kryptera och dekryptera DEK. Detta gör att du får fullständig kontroll över dina data och nycklar. Du kan när som helst inaktivera dina nycklar eller återkalla åtkomsten till hanterade diskar. Du kan också granska krypterings nyckel användningen med Azure Key Vault övervakning så att endast hanterade diskar eller andra betrodda Azure-tjänster får åtkomst till dina nycklar.

Följande diagram visar hur hanterade diskar använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med hjälp av kundhanterad nyckel:

![Arbets flöde för hanterade diskar och Kundhanterade nycklar. En administratör skapar en Azure Key Vault, skapar en disk krypterings uppsättning och konfigurerar disk krypterings uppsättningen. Uppsättningen är kopplad till en virtuell dator, vilket gör att disken kan använda Azure AD för att autentisera](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


I följande lista förklaras diagrammet i ännu mer detalj:

1. En Azure Key Vault administratör skapar nyckel valvs resurser.
1. Key Vault-administratören importerar antingen sina RSA-nycklar för att Key Vault eller generera nya RSA-nycklar i Key Vault.
1. Administratören skapar en instans av en resurs för disk krypterings uppsättning och anger ett Azure Key Vault-ID och en nyckel-URL. Disk krypterings uppsättning är en ny resurs som introducerats för att förenkla nyckel hanteringen för hanterade diskar. 
1. När en disk krypterings uppsättning skapas skapas en [systemtilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) i Azure Active Directory (AD) och associeras med disk krypterings uppsättningen. 
1. Azure Key Vault-administratören ger sedan den hanterade identiteten behörighet att utföra åtgärder i nyckel valvet.
1. En VM-användare skapar diskar genom att associera dem med disk krypterings uppsättningen. Den virtuella dator användaren kan också aktivera kryptering på Server sidan med Kundhanterade nycklar för befintliga resurser genom att associera dem med disk krypterings uppsättningen. 
1. Managed disks använder den hanterade identiteten för att skicka begär anden till Azure Key Vault.
1. Vid läsning eller skrivning av data skickar Managed disks begär anden till Azure Key Vault att kryptera (omslutning) och dekryptera (unwrap) data krypterings nyckeln för att utföra kryptering och dekryptering av data. 

Information om hur du återkallar åtkomst till Kundhanterade nycklar finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i lagrings kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Storage.

### <a name="supported-regions"></a>Regioner som stöds

Det finns för närvarande endast stöd för följande regioner:

- Tillgängligt som ett GA-erbjudande i regionerna östra USA, västra USA 2 och södra centrala USA.
- Tillgängligt som en offentlig för hands version i regionerna västra centrala USA, östra USA 2, centrala Kanada och Europa, norra.

### <a name="restrictions"></a>Begränsningar

För närvarande har Kundhanterade nycklar följande begränsningar:

- Endast ["mjuka" och "hårda" RSA-nycklar](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) med storleken 2080 stöds, inga andra nycklar eller storlekar.
- Diskar som har skapats från anpassade avbildningar som krypteras med kryptering på Server sidan och Kundhanterade nycklar måste krypteras med samma Kundhanterade nycklar och måste finnas i samma prenumeration.
- Ögonblicks bilder som har skapats från diskar som är krypterade med kryptering på Server sidan och Kundhanterade nycklar måste vara krypterade med samma Kundhanterade nycklar.
- Anpassade avbildningar som krypteras med kryptering på Server sidan och Kundhanterade nycklar kan inte användas i det delade avbildnings galleriet.
- Alla resurser som är relaterade till dina Kundhanterade nycklar (Azure Key Vaults, disk krypterings uppsättningar, virtuella datorer, diskar och ögonblicks bilder) måste finnas i samma prenumeration och region.
- Diskar, ögonblicks bilder och bilder som är krypterade med Kundhanterade nycklar kan inte flyttas till en annan prenumeration.
- Om du använder Azure Portal för att skapa disk krypterings uppsättningen kan du inte använda ögonblicks bilder för tillfället.
- Hanterade diskar som krypterats med Kundhanterade nycklar kan inte också krypteras med Azure Disk Encryption.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Konfigurera din Azure Key Vault och DiskEncryptionSet

1. Kontrol lera att du har installerat den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och loggat till ett Azure-konto i med [AZ-inloggning](/cli/azure/reference-index).

1. Skapa en instans av Azure Key Vault och krypterings nyckel.

    När du skapar Key Vault-instansen måste du aktivera mjuk borttagning och tömning av skydd. Mjuk borttagning garanterar att Key Vault innehåller en borttagen nyckel för en viss kvarhållningsperiod (90 dag standard). Med rensnings skyddet går det inte att ta bort en borttagen nyckel förrän kvarhållningsperioden upphörde. Dessa inställningar skyddar dig från att förlora data på grund av oavsiktlig borttagning. De här inställningarna är obligatoriska när du använder en Key Vault för att kryptera hanterade diskar.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.  Skapa en instans av en DiskEncryptionSet. 
    
    ```azurecli
    keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)

    keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

    az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
    ```

1.  Ge DiskEncryptionSet-resursen åtkomst till nyckel valvet. 

    > [!NOTE]
    > Det kan ta några minuter för Azure att skapa identiteten för din DiskEncryptionSet i din Azure Active Directory. Om du får ett fel meddelande som "det går inte att hitta Active Directory-objektet" när du kör följande kommando, väntar du några minuter och försöker igen.

    ```azurecli
    desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

    az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get

    az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en virtuell dator med en Marketplace-avbildning, kryptera operativ system och data diskar med Kundhanterade nycklar

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>Kryptera befintliga frånkopplade hanterade diskar 

Dina befintliga diskar får inte anslutas till en virtuell dator som körs för att du ska kunna kryptera dem med följande skript:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en skalnings uppsättning för virtuella datorer med en Marketplace-avbildning, kryptera operativ system och data diskar med Kundhanterade nycklar

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Skapa en tom disk som är krypterad med kryptering på Server sidan med Kundhanterade nycklar och koppla den till en virtuell dator

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till hanterade diskar till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till hanterade diskar till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kryptering på Server sidan jämfört med Azure Disk Encryption

[Azure Disk Encryption för virtuella datorer och skalnings uppsättningar för virtuella datorer](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utnyttjar [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -funktionen i Windows och [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt) funktionen i Linux för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn.  Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.

## <a name="next-steps"></a>Nästa steg

- [Utforska Azure Resource Manager mallar för att skapa krypterade diskar med Kundhanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Replikera datorer med Kundhanterade nycklar aktiverade diskar](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Konfigurera katastrof återställning av virtuella VMware-datorer till Azure med PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Konfigurera katastrof återställning till Azure för virtuella Hyper-V-datorer med PowerShell och Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
