---
title: Kryptering på serversidan av Azure Managed Disks – Azure CLI
description: Azure Storage skyddar dina data genom att kryptera dem i vila innan den sparas i lagringskluster. Du kan lita på Microsoft-hanterade nycklar för kryptering av dina hanterade diskar, eller så kan du använda kundhanterade nycklar för att hantera kryptering med dina egna nycklar.
author: roygara
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: f50115732940eab14db30842be85b47cb4a552e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299537"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Kryptering på serversidan av Hanterade Azure-diskar

Azure-hanterade diskar krypterar automatiskt dina data som standard när de sparas i molnet. Kryptering på serversidan skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Data i Azure-hanterade diskar krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), ett av de starkaste blockchiffer som finns och är FIPS 140-2-kompatibel.   

Kryptering påverkar inte prestanda för hanterade diskar. Det finns ingen extra kostnad för krypteringen.

Mer information om kryptografiska moduler som ligger till grund för Azure-hanterade diskar finns i [Kryptografi-API: Nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnyckel

Du kan lita på plattformshanterade nycklar för kryptering av den hanterade disken eller hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla data i hanterade diskar. 

I följande avsnitt beskrivs vart och ett av alternativen för nyckelhantering mer i detalj.

## <a name="platform-managed-keys"></a>Plattformshanterade nycklar

Som standard använder hanterade diskar plattformshanterade krypteringsnycklar. Från och med den 10 juni 2017 krypteras automatiskt alla nya hanterade diskar, ögonblicksbilder, bilder och nya data som skrivs till befintliga hanterade diskar i vila med plattformshanterade nycklar.

## <a name="customer-managed-keys"></a>Kundhanterade nycklar

Du kan välja att hantera kryptering på nivån för varje hanterad disk, med dina egna nycklar. Serverbaserad kryptering för hanterade diskar med kundhanterade nycklar ger en integrerad upplevelse med Azure Key Vault. Du kan antingen importera [dina RSA-nycklar](../../key-vault/key-vault-hsm-protected-keys.md) till key vault eller generera nya RSA-nycklar i Azure Key Vault. Azure-hanterade diskar hanterar kryptering och dekryptering på ett helt transparent sätt med hjälp av [kuvertkryptering](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Den krypterar data med hjälp av en [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-baserad datakrypteringsnyckel (DEK), som i sin tur är skyddad med hjälp av dina nycklar. Du måste bevilja åtkomst till hanterade diskar i key vault för att använda nycklarna för att kryptera och dekryptera DEK. Detta ger dig full kontroll över dina data och nycklar. Du kan när som helst inaktivera dina nycklar eller återkalla åtkomsten till hanterade diskar. Du kan också granska krypteringsnyckelanvändningen med Azure Key Vault-övervakning för att säkerställa att endast hanterade diskar eller andra betrodda Azure-tjänster kommer åt dina nycklar.

För premium-SSD-enheter, standard-SSD-enheter och vanliga hårddiskar: När du inaktiverar eller tar bort nyckeln stängs alla virtuella datorer med diskar med den nyckeln automatiskt av. Därefter kan inte de virtuella datorerna användas om nyckeln är aktiverad igen eller om du tilldelar en ny nyckel.

När du inaktiverar eller tar bort en nyckel stängs inte alla virtuella datorer med ultradiskar som använder nyckeln automatiskt av när du inaktiverar eller tar bort en nyckel. När du deallocate och starta om de virtuella datorerna då diskarna kommer att sluta använda nyckeln och sedan virtuella datorer kommer inte att komma tillbaka online. Om du vill ansluta de virtuella datorerna måste du tilldela en ny nyckel eller aktivera den befintliga nyckeln.

Följande diagram visar hur hanterade diskar använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med den kundhanterade nyckeln:

![Arbetsflöde för hanterade disk- och kundhanterade nycklar. En administratör skapar ett Azure Key Vault och skapar sedan en diskkrypteringsuppsättning och ställer in diskkrypteringsuppsättningen. Uppsättningen är associerad med en virtuell dator, vilket gör att disken kan använda Azure AD för att autentisera](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


I följande lista förklaras diagrammet ännu mer i detalj:

1. En Azure Key Vault-administratör skapar nyckelvalvresurser.
1. Nyckelvalvsadministratören importerar antingen sina RSA-nycklar till Key Vault eller genererar nya RSA-nycklar i Key Vault.
1. Administratören skapar en instans av diskkrypteringsuppsättningsresursen och anger ett Azure Key Vault-ID och en nyckel-URL. Diskkrypteringsuppsättning är en ny resurs som introduceras för att förenkla nyckelhanteringen för hanterade diskar. 
1. När en diskkrypteringsuppsättning skapas skapas en [systemtilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) i Azure Active Directory (AD) och associeras med diskkrypteringsuppsättningen. 
1. Azure-nyckelvalvsadministratören beviljar sedan den hanterade identitetsbehörigheten för att utföra åtgärder i nyckelvalvet.
1. En VM-användare skapar diskar genom att associera dem med diskkrypteringsuppsättningen. Den virtuella datorn användaren kan också aktivera server-side kryptering med kundhanterade nycklar för befintliga resurser genom att associera dem med diskkrypteringsuppsättningen. 
1. Hanterade diskar använder den hanterade identiteten för att skicka begäranden till Azure Key Vault.
1. För att läsa eller skriva data skickar hanterade diskar begäranden till Azure Key Vault för att kryptera (radbryta) och dekryptera (packa upp) datakrypteringsnyckeln för att utföra kryptering och dekryptering av data. 

Information om hur du återkallar åtkomst till kundhanterade nycklar finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Om du återkallar åtkomst blockeras åtkomsten till alla data i lagringskontot, eftersom krypteringsnyckeln inte är tillgänglig för Azure Storage.

### <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Begränsningar

För tillfället har kundhanterade nycklar följande begränsningar:

- Om den här funktionen är aktiverad för disken kan du inte inaktivera den.
    Om du behöver komma runt detta måste du [kopiera alla data](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) till en helt annan hanterad disk som inte använder kundhanterade nycklar.
- Endast ["mjuka" och "hårda" RSA-nycklar](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) i storlek 2080 stöds, inga andra nycklar eller storlekar.
- Diskar som skapas från anpassade avbildningar som krypteras med kryptering på serversidan och kundhanterade nycklar måste krypteras med samma kundhanterade nycklar och måste finnas i samma prenumeration.
- Ögonblicksbilder som skapas från diskar som krypteras med kryptering på serversidan och kundhanterade nycklar måste krypteras med samma kundhanterade nycklar.
- Anpassade avbildningar som krypterats med kryptering på serversidan och kundhanterade nycklar kan inte användas i det delade bildgalleriet.
- Alla resurser som är relaterade till dina kundhanterade nycklar (Azure Key Vaults, diskkrypteringsuppsättningar, virtuella datorer, diskar och ögonblicksbilder) måste finnas i samma prenumeration och region.
- Diskar, ögonblicksbilder och bilder som krypterats med kundhanterade nycklar kan inte flyttas till en annan prenumeration.
- Om du använder Azure-portalen för att skapa diskkrypteringsuppsättningen kan du inte använda ögonblicksbilder för tillfället.
- Hanterade diskar krypterade med kundhanterade nycklar kan inte också krypteras med Azure Disk Encryption.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Konfigurera ditt Azure Key Vault och DiskEncryptionSet

1. Kontrollera att du har installerat den senaste [Azure CLI](/cli/azure/install-az-cli2) och loggat in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

1. Skapa en instans av Azure Key Vault och krypteringsnyckel.

    När du skapar Key Vault-instansen måste du aktivera skydd för mjuk borttagning och rensning. Mjuk borttagning säkerställer att Key Vault har en borttagen nyckel för en viss kvarhållningsperiod (90 dagars standard). Rensningsskyddet säkerställer att en borttagen nyckel inte kan tas bort permanent förrän kvarhållningsperioden har förfallit. Dessa inställningar skyddar dig från att förlora data på grund av oavsiktlig borttagning. Dessa inställningar är obligatoriska när du använder ett Key Vault för kryptering av hanterade diskar.

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

1.    Skapa en instans av en DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Bevilja diskEncryptionSet-resursen åtkomst till nyckelvalvet. 

        > [!NOTE]
        > Det kan ta några minuter för Azure att skapa identiteten för din DiskEncryptionSet i din Azure Active Directory. Om du får ett felmeddelande som "Det går inte att hitta Active Directory-objektet" när du kör följande kommando väntar du några minuter och försöker igen.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en virtuell dator med en Marketplace-avbildning, kryptera operativsystemet och datadiskarna med kundhanterade nycklar

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


#### <a name="encrypt-existing-unattached-managed-disks"></a>Kryptera befintliga obundna hanterade diskar 

Dina befintliga diskar får inte vara kopplade till en virtuell dator som körs för att du ska kunna kryptera dem med följande skript:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en skalningsuppsättning för virtuella datorer med en Marketplace-avbildning, kryptera operativsystemet och datadiskarna med kundhanterade nycklar

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Skapa en tom disk krypterad med kryptering på serversidan med kundhanterade nycklar och koppla den till en virtuell dator

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
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar kundhanterade nycklar tilldelas en hanterad identitet automatiskt till dina resurser under skalen. Om du sedan flyttar prenumerationen, resursgruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med hanterade diskar till den nya klienten, så kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [Överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar kundhanterade nycklar tilldelas en hanterad identitet automatiskt till dina resurser under skalen. Om du sedan flyttar prenumerationen, resursgruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med hanterade diskar till den nya klienten, så kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [Överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kryptering på serversidan jämfört med Kryptering av Azure-disk

[Azure Disk Encryption för virtuella datorer och virtuella datorskalauppsättningar](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utnyttjar [BitLocker-funktionen](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) i Windows och [DM-Crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att kryptera hanterade diskar med kundhanterade nycklar inom gästdatorn.  Serverbaserad kryptering med kundhanterade nycklar förbättras på ADE genom att du kan använda alla os-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagringstjänsten.

## <a name="next-steps"></a>Nästa steg

- [Utforska Azure Resource Manager-mallarna för att skapa krypterade diskar med kundhanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Replikera datorer med kundhanterade nycklar aktiverade diskar](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Konfigurera haveriberedskap av virtuella virtuella datorer med VMware till Azure med PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Konfigurera haveriberedskap till Azure för virtuella hyper-v-datorer med PowerShell och Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
