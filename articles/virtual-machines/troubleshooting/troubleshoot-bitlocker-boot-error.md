---
title: Felsökning av BitLocker startfel på en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker BitLocker startfel i en Azure VM
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
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 1cfba4bf824d381e1b40ea0228ee902a1d6b5a9a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763222"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker startfel på en Azure VM

 Den här artikeln beskriver BitLocker-fel som kan uppstå när du startar en Windows virtuell dator (VM) i Microsoft Azure.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="symptom"></a>Symtom

 En virtuell Windows-dator startar inte. När du checkar skärmbilderna den [Startdiagnostik](../windows/boot-diagnostics.md) fönstret du ser något av följande felmeddelanden:

- Plugin-USB-drivrutin med BitLocker-nyckel

- Du komma åt! Ange återställningsnyckeln för att komma igång igen (tangentbordslayout: USA) fel inloggningsinformation har angetts för många gånger, så att din dator har låsts för att skydda din integritet. Om du vill hämta den, går du till http://windows.microsoft.com/recoverykeyfaq från en annan dator eller mobil enhet. Om du behöver den, nyckeln ID: T är XXXXXXX. Eller så kan du återställa din dator.

- Ange lösenordet för att låsa upp den här enheten [] tryck på Infoga för att se lösenordet när du skriver.
- Ange återställningsnyckeln belastningen återställningsnyckeln från USB-enhet.

## <a name="cause"></a>Orsak

Det här problemet kan uppstå om den virtuella datorn går inte att hitta nyckeln för BitLocker-återställning (BEK)-fil för att dekryptera krypterade disken.

## <a name="solution"></a>Lösning

Lös problemet, stoppa och frigör den virtuella datorn och starta om den. Den här åtgärden tvingar den virtuella datorn att hämta filen BEK från Azure Key Vault och placera den i den krypterade disken. 

Om den här metoden har inte Lös problemet, följer du stegen nedan för att återställa filen BEK manuellt:

1. Ta en ögonblicksbild av systemdisken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla systemdisken till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md) som krypteras med BitLocker. Detta krävs för att köra den [hantera bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) kommando som är endast tillgänglig på den BitLocker-krypterade virtuella datorn.

    När du ansluter en hanterad disk, kan det hända att felmeddelandet ”innehåller krypteringsinställningar och därför inte användas som en datadisk”. Kör följande skript och försök igen att ansluta disken i den här situationen:

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
     Du kan inte koppla en hanterad disk till en virtuell dator som har återställts från en avbildning av en blob.

3. När disken är ansluten kan du se en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn så att du kan köra vissa Azure PowerShell-skript. Se till att du har den [senaste versionen av Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) installerad på den Virtuella återställningsdatorn.

4. Öppna en upphöjd Azure PowerShell-session (Kör som administratör). Kör följande kommandon för att logga in på Azure-prenumeration:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Kör följande skript för att kontrollera namnet på filen BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Följande är exempel på utdata. Leta upp BEK filnamnet för den anslutna disken. I det här fallet förutsätter vi att enhetsbeteckningen för den anslutna disken är F och BEK-filen är EF7B2F5A - 50C 6-4637-9F13-7F599C12F85C. BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Om du ser två dubbla volymer, är den volym som innehåller den nya tidsstämpeln den aktuella BEK-filen som används av den Virtuella återställningsdatorn.

    Om den **innehållstyp** värdet är **omslutna BEK**går du till den [nyckel kryptering nyckel (KEK) scenarier](#key-encryption-key-scenario).

    Nu när du har namnet på filen BEK för enheten som du behöver skapa hemlighet-filnamn. BEK fil att låsa upp enheten. 

6.  Ladda ned filen BEK till återställningsdisk. I följande exempel sparar BEK-filen till mappen C:\BEK. Se till att den `C:\BEK\` sökvägen finns innan du kör skripten.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Kör följande kommando för att låsa upp den anslutna disken med hjälp av BEK-filen:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. se till att du använder rätt enhetsbeteckning. 

    - Om disken har låsts upp med BEK-nyckel. Vi kan du överväga att BItLocker problemet lösas. 

    - Om med BEK-nyckel inte låser upp disken, kan du använda inaktivera skydd för att tillfälligt inaktivera BitLocker genom att köra följande kommando
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Om du ska återskapa den virtuella datorn med hjälp av dytem disk måste du fullständigt kryptera enheten. Gör detta genom att köra följande kommando:

        ```powershell
        manage-bde -off F:
        ```
8.  Ta bort disken från den Virtuella återställningsdatorn och bifoga disken till den berörda virtuella datorn som en systemdisk igen. Mer information finns i [felsöka en virtuell Windows-dator genom att koppla OS-disk till virtuell återställningsdator](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Krypteringsnyckel scenario

Följ dessa steg för ett scenario med krypteringsnyckel:

1. Se till att det inloggade användarkontot kräver behörigheten ”packats upp” i Key Vault åtkomstprinciper i den **användare | Nyckelbehörigheter | Kryptografiska åtgärder | Packa upp nyckeln**.
2. Spara följande skript i en. Ps1-fil:

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
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
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
    $authResult = $authContext.AcquireToken($resourceAppIdURI, $clientId, $redirectUri, "Auto")
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
3. Ange parametrarna. Skriptet ska bearbeta KEK hemligheten för att skapa nyckeln BEK och spara den till en lokal mapp på den Virtuella återställningsdatorn.

4. När skriptet börjar ser du följande utdata:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...

    När skriptet har slutförts kan du se följande utdata:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Kör följande kommando för att låsa upp den anslutna disken med hjälp av BEK-filen:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. se till att du använder rätt enhetsbeteckning. 

    - Om disken har låsts upp med BEK-nyckel. Vi kan du överväga att BItLocker problemet lösas. 

    - Om med BEK-nyckel inte låser upp disken, kan du använda inaktivera skydd för att tillfälligt inaktivera BitLocker genom att köra följande kommando
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Om du ska återskapa den virtuella datorn med hjälp av dytem disk måste du fullständigt kryptera enheten. Gör detta genom att köra följande kommando:

        ```powershell
        manage-bde -off F:
        ```

6. Ta bort disken från den Virtuella återställningsdatorn och bifoga disken till den berörda virtuella datorn som en systemdisk igen. Mer information finns i [felsöka en virtuell Windows-dator genom att koppla OS-disk till virtuell återställningsdator](troubleshoot-recovery-disks-windows.md).
