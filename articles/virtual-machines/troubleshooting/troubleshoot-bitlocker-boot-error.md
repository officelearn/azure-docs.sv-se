---
title: Felsöka start fel i BitLocker på en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker start fel i BitLocker i en virtuell Azure-dator
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 27a675982711f8d8f0b36ea0cc2600de45e97a6e
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348452"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker-startfel på en virtuell Azure-dator

 I den här artikeln beskrivs BitLocker-fel som kan uppstå när du startar en virtuell Windows-dator (VM) i Microsoft Azure.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symtom

 En virtuell Windows-dator startar inte. När du kontrollerar skärm bilderna i fönstret [Boot Diagnostics](../windows/boot-diagnostics.md) visas något av följande fel meddelanden:

- Anslut USB-drivrutinen som har BitLocker-nyckeln

- Du är låst! Ange återställnings nyckeln för att komma igång igen (tangentbordslayout: US) fel inloggnings information har angetts för många gånger, så datorn var låst för att skydda din integritet. Hämta återställnings nyckeln genom att gå https://windows.microsoft.com/recoverykeyfaq till från en annan dator eller mobil enhet. Om du behöver det är nyckel-ID: t XXXXXXX. Du kan också återställa datorn.

- Ange lösen ordet för att låsa upp enheten [] Tryck på INSERT-tangenten för att se lösen ordet när du skriver.
- Ange återställnings nyckeln Läs in din återställnings nyckel från en USB-enhet.

## <a name="cause"></a>Orsak

Det här problemet kan inträffa om den virtuella datorn inte kan hitta filen med återställnings nyckeln för BitLocker (BEK) för att dekryptera den krypterade disken.

## <a name="solution"></a>Lösning

Lös problemet genom att stoppa och frigöra den virtuella datorn och sedan starta om den. Den här åtgärden tvingar den virtuella datorn att hämta BEK-filen från Azure Key Vault och placerar den på den krypterade disken. 

Om den här metoden inte löser problemet följer du dessa steg för att återställa BEK-filen manuellt:

1. Ta en ögonblicks bild av system disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla systemdisken till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md). Om du vill köra kommandot [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) i steg 7 måste **BitLocker-diskkryptering** funktionen vara aktive rad i den virtuella återställnings datorn.

    När du ansluter en hanterad disk kan du få ett fel meddelande om att "innehåller krypterings inställningar och inte kan användas som en data disk". I den här situationen kör du följande skript för att försöka ansluta disken:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Det går inte att ansluta en hanterad disk till en virtuell dator som har återställts från en BLOB-avbildning.

3. När disken är ansluten gör du en fjärr skrivbords anslutning till den virtuella återställnings datorn så att du kan köra vissa Azure PowerShell skript. Kontrol lera att du har den [senaste versionen av Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) installerad på den virtuella återställnings datorn.

4. Öppna en förhöjd Azure PowerShell-session (kör som administratör). Kör följande kommandon för att logga in på Azure-prenumerationen:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Kör följande skript för att kontrol lera namnet på filen BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Följande är exempel på utdata. Leta upp fil namnet BEK för den anslutna disken. I det här fallet förutsätter vi att enhets beteckningen för den anslutna disken är F och att BEK-filen är EF7B2F5A-50C6-4637-9F13-7F599C12F85C. Bek.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Om du ser två duplicerade volymer är volymen som har den nya tidsstämpeln den aktuella BEK-filen som används av den virtuella återställnings datorn.

    Om värdet för **innehålls typen** är **figursatt Bek**går du till [KEK-scenarierna (Key Encryption Key)](#key-encryption-key-scenario).

    Nu när du har namnet på BEK-filen för enheten måste du skapa hemlighet-File-name. BEK-fil för att låsa upp enheten.

6.  Hämta filen BEK till återställnings disken. I följande exempel sparas filen BEK i mappen C:\BEK. Kontrol lera att `C:\BEK\` sökvägen finns innan du kör skripten.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Om du vill låsa upp den anslutna disken med hjälp av BEK-filen kör du följande kommando.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. kontrol lera att du använder rätt enhets beteckning. 

    - Om disken har låsts upp med BEK-nyckeln. Vi kan överväga att BitLocker-problemet ska lösas. 

    - Om du inte låser upp disken med BEK-nyckeln kan du använda Suspend-skyddet för att tillfälligt stänga av BitLocker genom att köra följande kommando
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Om du ska återskapa den virtuella datorn med hjälp av dytem-disken måste du helt dekryptera enheten. Gör detta genom att köra följande kommando:

        ```powershell
        manage-bde -off F:
        ```
8.  Koppla bort disken från den virtuella återställnings datorn och återanslut sedan disken till den virtuella datorn som en system disk. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator för återställning](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Nyckel krypterings nyckel scenario

Följ dessa steg för ett scenario med nyckel krypterings nyckel:

1. Se till att det inloggade användar kontot kräver behörigheten "unwrap" i Key Vault åtkomst principer i **användaren | Nyckel behörigheter | Kryptografiska åtgärder | Unwrap-nyckel**.
2. Spara följande skript i en. PS1-fil:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Ange parametrarna. Skriptet bearbetar KEK-hemligheten för att skapa BEK-nyckeln och sparar den sedan i en lokal mapp på den virtuella återställnings datorn.

4. Följande utdata visas när skriptet börjar:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    När skriptet har slutförts visas följande utdata:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Om du vill låsa upp den anslutna disken med hjälp av BEK-filen kör du följande kommando:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. kontrol lera att du använder rätt enhets beteckning. 

    - Om disken har låsts upp med BEK-nyckeln. Vi kan överväga att BitLocker-problemet ska lösas. 

    - Om du inte låser upp disken med BEK-nyckeln kan du använda Suspend-skyddet för att tillfälligt stänga av BitLocker genom att köra följande kommando
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Om du ska återskapa den virtuella datorn med hjälp av dytem-disken måste du helt dekryptera enheten. Gör detta genom att köra följande kommando:

        ```powershell
        manage-bde -off F:
        ```

6. Koppla bort disken från den virtuella återställnings datorn och återanslut sedan disken till den virtuella datorn som en system disk. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator för återställning](troubleshoot-recovery-disks-windows.md).
