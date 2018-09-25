---
title: Kryptera diskar på en Windows-dator i Azure | Microsoft Docs
description: Kryptera virtuella diskar på en virtuell Windows-dator för förbättrad säkerhet med hjälp av Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: cynthn
ms.openlocfilehash: 20d3568fa3f583c190f087de861d857fe3e793a9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985457"
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Kryptera virtuella diskar på en virtuell Windows-dator
För förbättrad virtuell dator (VM) säkerhet och efterlevnad, kan virtuella diskar i Azure krypteras. Diskar krypteras med hjälp av kryptografiska nycklar som skyddas i ett Azure Key Vault. Du kontrollerar dessa kryptografiska nycklar och kan granska deras användning. Den här artikeln beskriver hur du krypterar virtuella diskar på en Windows-VM med Azure PowerShell. Du kan också [kryptera en Linux VM med Azure CLI](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på Windows virtuella datorer krypteras i vila med Bitlocker. Det är kostnadsfritt för att kryptera virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av software protection eller kan du importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du kan behålla kontrollen över dessa kryptografiska nycklar och kan granska deras användning. En Azure Active Directory-tjänstens huvudnamn är en säker mekanism för att utfärda dessa kryptografiska nycklar som virtuella datorer tillhandahålls av och sätts på.

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i ett Azure Key Vault.
2. Konfigurera den kryptografiska nyckeln kan användas för att kryptera diskar.
3. För att läsa den kryptografiska nyckeln från Azure Key Vault, skapa en Azure Active Directory-tjänstens huvudnamn med rätt behörighet.
4. Utfärda kommandot för att kryptera dina virtuella diskar, ange Azure Active Directory-tjänsten huvudnamn och lämpliga kryptografiska nyckel som ska användas.
5. Azure Active Directory-tjänstobjekt begär den nödvändiga kryptografinyckeln från Azure Key Vault.
6. Virtuella diskar krypteras med hjälp av den angivna kryptografiska nyckeln.

## <a name="encryption-process"></a>Krypteringsprocessen
Diskkryptering är beroende av följande ytterligare komponenter:

* **Azure Key Vault** – används för att skydda kryptografiska nycklar och hemligheter som används för att disk kryptering/dekryptering. 
  * Om en sådan finns, kan du använda ett befintligt Azure Key Vault. Du behöver inte dedikera en Key Vault för kryptering av diskar.
  * Du kan skapa ett dedikerat Nyckelvalv för att avgränsa administrativa gränser och viktiga synlighet.
* **Azure Active Directory** -hanterar säker byte av obligatoriska kryptografiska nycklar och autentisering för begärda åtgärder. 
  * Du kan normalt använda en befintlig Azure Active Directory-instans för ditt program.
  * Tjänstens huvudnamn är en säker mekanism för att begära och utfärdas giltiga kryptografiska nycklar. Du utvecklar inte ett verkligt program som kan integreras med Azure Active Directory.

## <a name="requirements-and-limitations"></a>Krav och begränsningar
Krav för diskkryptering och scenarier som stöds:

* Aktiverar kryptering på den nya virtuella Windows-datorer från Azure Marketplace-avbildningar eller anpassade VHD-avbildning.
* Aktiverar kryptering på den befintliga virtuella Windows-datorer i Azure.
* Aktivera kryptering på Windows virtuella datorer som har konfigurerats med hjälp av lagringsutrymmen.
* Inaktivera kryptering på operativsystem och enheter för Windows-datorer.
* Alla resurser (till exempel Key Vault, lagringskontot och virtuella datorer) måste finnas i samma Azure-region och prenumeration.
* Standard-nivån virtuella datorer, till exempel A, D, DS, G och GS-serien virtuella datorer.

Diskkryptering stöds inte för närvarande i följande scenarier:

* Basic-nivån virtuella datorer.
* Virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen.
* Uppdaterar de kryptografiska nycklarna på en redan krypterad virtuell dator.
* Integrering med en lokal nyckelhanteringstjänst.

## <a name="create-azure-key-vault-and-keys"></a>Skapa Azure Key Vault och nycklar
Innan du börjar bör du kontrollera att den senaste versionen av Azure PowerShell-modulen har installerats. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Under hela kommandoexempel ersätter du alla exempel parametrar med namn, plats och nyckelvärden. I följande exempel används en konvention för *myResourceGroup*, *Mittkeyvault*, *myVM*osv.

Det första steget är att skapa ett Azure Key Vault för att lagra kryptografiska nycklar. Azure Key Vault kan lagra nycklar, hemligheter eller lösenord så att du kan implementera dem på ett säkert sätt i dina program och tjänster. För virtuell diskkryptering, kan du skapa ett Nyckelvalv för att lagra en krypteringsnyckel som används för att kryptera eller dekryptera din virtuella diskar. 

Aktivera Azure Key Vault-providern i Azure-prenumerationen med [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den *USA, östra* plats:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste finnas i samma region. Skapa ett Azure Key Vault med [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) och aktivera Key Vault för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyVaultName* på följande sätt:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara Security Model (HSM)-skydd. Använda en HSM kräver en premium Key Vault. Det finns en ytterligare kostnad för att skapa en premium Key Vault i stället för standard Key Vault som lagrar programvaruskyddade nycklar. När du skapar en premium Key Vault i föregående steg till den *- Sku ”Premium”* parametrar. I följande exempel används programvaruskyddade nycklar eftersom vi har skapat ett Nyckelvalv som standard. 

För båda modellerna för skydd måste Azure-plattformen beviljas åtkomst för att begära krypteringsnycklarna när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en krypteringsnyckel i Key Vault med [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). I följande exempel skapas en nyckel med namnet *myKey*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Skapa tjänstens huvudnamn för Azure Active Directory
När virtuella diskar krypteras och dekrypteras, anger du ett konto för att hantera autentiseringen och utbyta kryptografiska nycklar från Key Vault. Det här kontot, ett Azure Active Directory-tjänstobjekt kan Azure-plattformen för att begära giltiga kryptografiska nycklar för den virtuella datorn. En standardinstans för Azure Active Directory är tillgängligt i din prenumeration, även om många organisationer har dedikerade Azure Active Directory-kataloger.

Skapa ett huvudnamn för tjänsten i Azure Active Directory med [New AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Så här anger du ett säkert lösenord, den [lösenordsprinciper och begränsningar i Azure Active Directory](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Kryptera eller dekryptera virtuella diskar, måste du ange behörigheter på den kryptografiska nyckel som lagras i Key Vault för att tillåta Azure Active Directory-huvudnamn för tjänsten att läsa nycklarna. Ange behörigheter för Key Vault med [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Testa krypteringsprocessen genom att skapa en virtuell dator med [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Windows Server 2016 Datacenter* bild. När du tillfrågas om autentiseringsuppgifter, anger du användarnamnet och lösenordet som ska användas för den virtuella datorn:

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Kryptera en virtuell dator
För att kryptera virtuella diskar kan samla du alla tidigare komponenter:

1. Ange Azure Active Directory-tjänstobjekt och lösenord.
2. Ange Key Vault för att lagra metadata för dina krypterade diskar.
3. Ange de kryptografiska nycklarna som ska användas för den faktiska kryptering och dekryptering.
4. Ange om du vill kryptera OS-disken, datadiskar eller alla.

Kryptera din virtuella dator med [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) med hjälp av Azure Key Vault-nyckeln och autentiseringsuppgifter för Azure Active Directory tjänstens huvudnamn. I följande exempel hämtar all viktig information och sedan krypterar den virtuella datorn med namnet *myVM*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Acceptera uppmaningen att fortsätta med VM-kryptering. Den virtuella datorn startas om under processen. När krypteringsprocessen har slutförts och den virtuella datorn har startats om, granska krypteringsstatus med [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Utdata ser ut ungefär så här:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du hanterar Azure Key Vault finns i [konfigurera Key Vault för virtuella datorer](key-vault-setup.md).
* Läs mer om diskkryptering, till exempel förbereda en krypterad anpassad virtuell dator att överföra till Azure, [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
