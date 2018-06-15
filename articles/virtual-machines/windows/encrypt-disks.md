---
title: Kryptera diskar på en Windows-dator i Azure | Microsoft Docs
description: Kryptera virtuella diskar på en Windows-VM för ökad säkerhet med hjälp av Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 442ff942150af8a8dec89164fbc017a9e6f360e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603750"
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Kryptera virtuella diskar på en Windows VM
För förbättrad virtuell dator (VM) säkerhet och efterlevnad krypteras virtuella diskar i Azure. Diskar krypteras med kryptografiska nycklar som skyddas i ett Azure Key Vault. Du styr dessa kryptografiska nycklar och granska deras användning. Den här artikeln beskrivs hur du krypterar virtuella diskar på en virtuell Windows-dator med hjälp av Azure PowerShell. Du kan också [kryptera en Linux VM som använder Azure CLI 2.0](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på virtuella Windows-datorer krypteras vilande med Bitlocker. Det är gratis för kryptering av virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med skydd av programvara eller du kan importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) certifierade FIPS 140-2 level 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behålla kontrollen över dessa kryptografiska nycklar och granska deras användning. Ett Azure Active Directory-tjänstens huvudnamn ger en säker mekanism för att utfärda de kryptografiska nycklarna som virtuella datorer är igång på och stänga av.

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i en Azure Key Vault.
2. Konfigurera den kryptografiska nyckeln som kan användas för att kryptera diskar.
3. Om du vill läsa den kryptografiska nyckeln från Azure Key Vault, skapa en Azure Active Directory service principal med rätt behörighet.
4. Utfärda kommandot för att kryptera din virtuella diskar, ange den Azure Active Directory service principal och lämpligt kryptografiska nyckeln som ska användas.
5. Azure Active Directory-tjänstens huvudnamn begär den kryptografiska nyckeln som krävs från Azure Key Vault.
6. De virtuella diskarna krypteras med den angivna kryptografiska nyckeln.

## <a name="encryption-process"></a>Krypteringsprocessen
Kryptering är beroende av följande ytterligare komponenter:

* **Azure Key Vault** – används för att skydda krypteringsnycklar och hemligheter som används för kryptering/dekryptering processen disk. 
  * Om det finns ett kan du använda en befintlig Azure Key Vault. Du har inte kryptera diskar för ett Nyckelvalv.
  * Du kan skapa ett dedikerat Nyckelvalv för att avgränsa administrativa gränser och viktiga synlighet.
* **Azure Active Directory** -hanterar säker utbyte av kryptografiska nycklar som krävs och autentisering för begärda åtgärder. 
  * Vanligtvis kan du använda en befintlig instans av Azure Active Directory för ditt program.
  * Tjänstens huvudnamn ger säker möjlighet att begära och utfärdas lämpliga kryptografiska nycklar. Du utvecklar inte ett verkligt program som integreras med Azure Active Directory.

## <a name="requirements-and-limitations"></a>Krav och begränsningar
Krav för diskkryptering och scenarier som stöds:

* Aktivera kryptering på den nya virtuella Windows-datorer från Azure Marketplace-bilder eller anpassade VHD-avbildningen.
* Aktivera kryptering på befintliga virtuella Windows-datorer i Azure.
* Aktivera kryptering på virtuella Windows-datorer som har konfigurerats med hjälp av lagringsutrymmen.
* Om du inaktiverar kryptering på Operativsystemet och enheter för Windows-datorer.
* Alla resurser (till exempel Nyckelvalv lagringskonto och VM) måste finnas i samma Azure-region och prenumeration.
* Standardnivån virtuella datorer, till exempel A, D, DS, G och GS-serien virtuella datorer.

Diskkryptering stöds inte i följande scenarier:

* Grundnivån virtuella datorer.
* Virtuella datorer skapas med den klassiska distributionsmodellen.
* Uppdaterar de kryptografiska nycklarna på en redan krypterade VM.
* Integrering med lokal nyckelhanteringstjänst.

## <a name="create-azure-key-vault-and-keys"></a>Skapa Azure Key Vault och nycklar
Innan du börjar bör du kontrollera att den senaste versionen av Azure PowerShell-modulen har installerats. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Ersätt alla exempel parametrar med egna namn, plats och nyckelvärden under hela kommandoexempel. I följande exempel används en konvention för *myResourceGroup*, *myKeyVault*, *myVM*osv.

Det första steget är att skapa en Azure Key Vault för att lagra kryptografiska nycklar. Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör det möjligt att implementera dem på ett säkert sätt i dina program och tjänster. För virtuell diskkryptering, kan du skapa ett Nyckelvalv för att lagra en krypteringsnyckel som används för att kryptera eller dekryptera din virtuella diskar. 

Aktivera Azure Key Vault-providern i din Azure-prenumeration med [registrera AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den *östra USA* plats:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste finnas i samma region. Skapa ett Azure Key Vault med [ny AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) och aktivera Nyckelvalvet för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyVaultName* på följande sätt:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara säkerhet modellen (HSM)-skydd. Använda en HSM kräver en premium Key Vault. Det finns en extra kostnad för att skapa en premium Key Vault snarare än standard Key Vault som lagrar programvara-skyddade nycklar. När du skapar en premium Key Vault i föregående steg till den *- Sku ”Premium-* parametrar. I följande exempel används programvaruskyddad nycklar eftersom vi har skapat ett Nyckelvalv som standard. 

För båda modellerna skydd måste Azure-plattformen beviljas åtkomst för att begära kryptografiska nycklar när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en kryptografisk nyckel i ditt Nyckelvalv med [Lägg till AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). I följande exempel skapas en nyckel som heter *MinNyckel*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Skapa Azure Active Directory huvudnamn för tjänsten
När virtuella diskar krypteras och dekrypteras, anger du ett konto för att hantera autentisering och byta ut kryptografiska nycklar från Nyckelvalvet. Det här kontot, tjänstens huvudnamn för ett Azure Active Directory kan Azure-plattformen begära lämpliga kryptografiska nycklar för den virtuella datorn. En standardinstans för Azure Active Directory finns i din prenumeration, även om många organisationer har särskilda Azure Active Directory-kataloger.

Skapa ett huvudnamn för tjänsten i Azure Active Directory med [ny AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Så här anger du ett säkert lösenord, den [lösenordsprinciper och begränsningar i Azure Active Directory](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

För att kunna kryptera eller dekryptera virtuella diskar, måste behörigheter på den kryptografiska nyckel som lagras i Nyckelvalvet anges för att tillåta Azure Active Directory-tjänstens huvudnamn att läsa nycklarna. Ange behörigheter för ditt Nyckelvalv med [Set AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Om du vill testa krypteringsprocessen, skapa en virtuell dator med [ny AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Windows Server 2016 Datacenter* bild. När du tillfrågas om autentiseringsuppgifter, ange användarnamn och lösenord som ska användas för den virtuella datorn:

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
Om du vill kryptera virtuella diskar samordnar du tidigare komponenter:

1. Ange tjänstens huvudnamn för Azure Active Directory och lösenord.
2. Ange Nyckelvalv för att lagra metadata för krypterade diskarna.
3. Ange de kryptografiska nycklarna som ska användas för den faktiska kryptering och dekryptering.
4. Ange om du vill kryptera OS-disk, datadiskar eller alla.

Kryptera din virtuella dator med [Set AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) med Azure Key Vault-nyckel och autentiseringsuppgifter för Azure Active Directory-UPN. I följande exempel hämtas alla viktig information och krypterar den virtuella datorn med namnet *myVM*:

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

Acceptera uppmaningen om att fortsätta med VM-kryptering. Den virtuella datorn startas om under processen. När krypteringsprocessen har slutförts och den virtuella datorn har startats om, granska krypteringsstatus med [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

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
* Mer information om hur du hanterar Azure Key Vault finns [konfigurera Key Vault för virtuella datorer](key-vault-setup.md).
* Läs mer om diskkryptering, till exempel förbereda en krypterad anpassade VM att överföra till Azure, [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
