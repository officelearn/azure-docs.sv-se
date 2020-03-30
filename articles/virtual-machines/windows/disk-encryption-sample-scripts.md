---
title: Exempelskript för Azure Disk Encryption
description: Den här artikeln är bilagan till virtuella datorer för Microsoft Azure Disk Encryption for Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266733"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Exempelskript för Azure Disk Encryption 

Den här artikeln innehåller exempelskript för att förbereda förkrypterade virtuella hårddiskar och andra uppgifter.

 

## <a name="list-vms-and-secrets"></a>Lista virtuella datorer och hemligheter

Lista alla krypterade virtuella datorer i din prenumeration:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Lista alla diskkrypteringshemligheter som används för att kryptera virtuella datorer i ett nyckelvalv:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Azure Disk Kryptering förutsättningar skript
Om du redan är bekant med förutsättningarna för Azure Disk Encryption kan du använda [Azure Disk Encryption förutsättningar PowerShell-skriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Ett exempel på hur du använder det här PowerShell-skriptet finns i [Snabbstarten kryptera en virtuell dator](disk-encryption-powershell-quickstart.md). Du kan ta bort kommentarerna från ett avsnitt i skriptet, med början på rad 211, för att kryptera alla diskar för befintliga virtuella datorer i en befintlig resursgrupp. 

I följande tabell visas vilka parametrar som kan användas i PowerShell-skriptet: 

|Parameter|Beskrivning|Obligatoriska?|
|------|------|------|
|$resourceGroupName| Namnet på den resursgrupp som KeyVault tillhör.  En ny resursgrupp med det här namnet skapas om det inte finns någon.| True|
|$keyVaultName|Namn på keyvault där krypteringsnycklar ska placeras. Ett nytt valv med det här namnet skapas om det inte finns något nytt valv.| True|
|$location|Plats för KeyVault. Kontrollera att KeyVault och virtuella datorer som ska krypteras finns på samma plats. Hämta en innehållsplatslista med `Get-AzLocation`.|True|
|$subscriptionId|Identifierare för Azure-prenumerationen som ska användas.  Du kan hämta ditt prenumerations-ID med `Get-AzSubscription`.|True|
|$aadAppName|Namnet på Azure AD-programmet som ska användas för att skriva hemligheter till KeyVault. Om det inte redan finns ett program med det namnet skapas ett nytt. Om den här appen redan finns skickar du parametern aadClientSecret till skriptet.|False|
|$aadClientSecret|Klienthemligheten för Azure AD-programmet som skapades tidigare.|False|
|$keyEncryptionKeyName|Namn på valfri nyckelkrypteringsnyckel i KeyVault. En ny nyckel med det här namnet skapas om det inte finns någon.|False|

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Kryptera eller dekryptera virtuella datorer utan en Azure AD-app

- [Aktivera diskkryptering på en befintlig eller körande Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Inaktivera kryptering på en windows-virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Kryptera eller dekryptera virtuella datorer med en Azure AD-app (tidigare version) 
 
- [Aktivera diskkryptering på en befintlig eller körande Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Inaktivera kryptering på en windows-virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Skapa en ny krypterad hanterad disk från en förkrypterad VHD/storage-blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Skapar en ny krypterad hanterad disk som en förkrypterad virtuell hårddisk och motsvarande krypteringsinställningar

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Förbereda en förkrypterad Windows VHD
De avsnitt som följer är nödvändiga för att förbereda en förkrypterad Windows VHD för distribution som en krypterad virtuell hårddisk i Azure IaaS. Använd informationen för att förbereda och starta en ny Virtuell Windows-dator (VHD) på Azure Site Recovery eller Azure. Mer information om hur du förbereder och laddar upp en virtuell hårddisk finns i [Ladda upp en generaliserad virtuell hårddisk och använda den för att skapa nya virtuella datorer i Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Uppdatera grupprincipen för att tillåta icke-TPM för OS-skydd
Konfigurera bitlockergrupprincipinställningen **BitLocker-diskkryptering**, som du hittar under**Administrativa mallar** > för **lokal datorprincipdatorkonfiguration** > **Computer Configuration** > **Windows-komponenter**. Ändra den här inställningen till **Operativsystemenheter** > **Kräv ytterligare autentisering vid start** > **Tillåt BitLocker utan kompatibel TPM,** vilket visas i följande bild:

![Microsoft Antimalware i Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installera BitLocker-funktionskomponenter
För Windows Server 2012 och senare använder du följande kommando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Använd följande kommando för Windows Server 2008 R2:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Förbereda OS-volymen för BitLocker med hjälp av`bdehdcfg`
Om du vill komprimera OS-partitionen och förbereda datorn för BitLocker kör du [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) om det behövs:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Skydda OS-volymen med BitLocker
Använd [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) kommandot för att aktivera kryptering på startvolymen med hjälp av ett externt nyckelskydd. Placera även den externa nyckeln (.bek-filen) på den externa enheten eller volymen. Kryptering är aktiverat på systemet / startvolymen efter nästa omstart.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Förbered den virtuella datorn med en separat virtuell dator för data/resurs för att hämta den externa nyckeln med bitlocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Ladda upp krypterad virtuell hårddisk till ett Azure-lagringskonto
När DM-Crypt-kryptering har aktiverats måste den lokala krypterade virtuella hårddisken laddas upp till ditt lagringskonto.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Ladda upp hemligheten för den förkrypterade virtuella datorn till ditt nyckelvalv
Diskkrypteringshemligheten som du tidigare har fått måste överföras som en hemlighet i nyckelvalvet.  Detta kräver att du beviljar den hemliga behörigheten och behörigheten wrapkey till kontot som överför hemligheterna.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Diskkrypteringshemlighet inte krypterad med en KEK
Om du vill konfigurera hemligheten i nyckelvalvet använder du [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Lösenfrasen kodas som en base64-sträng och överförs sedan till nyckelvalvet. Kontrollera dessutom att följande taggar ställs in när du skapar hemligheten i nyckelvalvet.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Använd `$secretUrl` i nästa steg för [att ansluta OS-disken utan att använda KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Diskkrypteringshemlighet krypterad med en KEK
Innan du laddar upp hemligheten till nyckelvalvet kan du kryptera den med hjälp av en nyckelkrypteringsnyckel. Använd wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) för att först kryptera hemligheten med hjälp av nyckelkrypteringsnyckeln. Utdata för den här figursättningen är en base64 URL-kodad sträng, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) som du sedan kan ladda upp som en hemlighet med hjälp av cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Använd `$KeyEncryptionKey` `$secretUrl` och i nästa steg för [att koppla OS-disken med KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Ange en hemlig URL när du bifogar en OS-disk

###  <a name="without-using-a-kek"></a>Utan att använda en KEK
När du ansluter OS-disken måste du `$secretUrl`passera . WEBBADRESSEN genererades i avsnittet "Diskkrypteringshemlighet som inte är krypterad med en KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Använda en KEK
När du ansluter OS-disken passerar du `$KeyEncryptionKey` och `$secretUrl`. WEBBADRESSEN genererades i avsnittet "Diskkrypteringshemlig krypterad med en KEK".The URL was generated in the "Disk encryption secret encrypted with a KEK" section.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
