---
title: Felsöka bitlockerstartfel på en virtuell Azure-dator | Microsoft-dokument
description: Lär dig hur du felsöker BitLocker-startfel i en Virtuell Azure-dator
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.openlocfilehash: 80fd91106530c0150a85d508b24041b2263da925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250015"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker-startfel på en Virtuell Azure-dator

 I den här artikeln beskrivs BitLocker-fel som kan uppstå när du startar en virtuell Windows-dator (VM) i Microsoft Azure.

 

## <a name="symptom"></a>Symptom

 En Virtuell Windows-dator startar inte. NÃ¤1s ã¤skontroll av skärmbilderna i fönstret [Starta diagnostik](../windows/boot-diagnostics.md) visas nÃ¤ringar av fÃ¶ringar:

- Koppla in USB-drivrutinen som har BitLocker-tangenten

- Du är utelåst! Ange återställningsnyckeln för att komma igång igen (Tangentbordslayout: USA) Fel inloggningsinformation har angetts för många gånger, så din dator låstes för att skydda din integritet. Om du vill hämta https://windows.microsoft.com/recoverykeyfaq återställningsnyckeln går du till från en annan dator eller mobil enhet. Om du behöver det är nyckel-ID XXXXXXX. Du kan också återställa datorn.

- Ange lösenordet för att låsa upp den här enheten [ ] Tryck på Infoga tangenten för att se lösenordet medan du skriver.
- Ange återställningsnyckeln Ladda återställningsnyckeln från en USB-enhet.

## <a name="cause"></a>Orsak

Det hÃ¤r problemet kan uppstÃ¥ om den virtuella datorn inte kan hitta BEK-filen (BitLocker Recovery Key) fÃ¥r för att dekryptera den krypterade disken.

## <a name="solution"></a>Lösning

Lös problemet genom att stoppa och frigöra den virtuella datorn och sedan starta om den. Den här åtgärden tvingar den virtuella datorn att hämta BEK-filen från Azure Key Vault och sedan lägga den på den krypterade disken. 

Om den här metoden inte löser problemet gör du så här för att återställa BEK-filen manuellt:

1. Ta en ögonblicksbild av systemdisken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla systemdisken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md). Om du vill köra [kommandot manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) i steg 7 måste **funktionen BitLocker-diskkryptering** vara aktiverad i återställningsdatorn.

    När du ansluter en hanterad disk kan felmeddelandet "innehåller krypteringsinställningar och därför inte kan användas som en datadisk". I det här fallet kör du följande skript för att försöka igen för att ansluta disken:

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
     Du kan inte koppla en hanterad disk till en virtuell dator som återställdes från en blob-avbildning.

3. När disken har anslutits gör du en fjärrskrivbordsanslutning till återställningsdatorn så att du kan köra vissa Azure PowerShell-skript. Kontrollera att du har den [senaste versionen av Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) installerad på återställningsdatorn.

4. Öppna en förhöjd Azure PowerShell-session (Kör som administratör). Kör följande kommandon för att logga in på Azure-prenumeration:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Kör följande skript för att kontrollera namnet på BEK-filen:

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

    Följande är ett urval av utdata. Leta reda på BEK-filnamnet för den bifogade disken. I det här fallet antar vi att enhetsbeteckningen för den bifogade disken är F och BEK-filen är EF7B2F5A-50C6-4637-9F13-7F599C12F85C. Bek.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Om du ser två dubblerade volymer är volymen som har den nyare tidsstämpeln den aktuella BEK-filen som används av återställningsdatorn.

    Om värdet **för innehållstyp** är **inslaget BEK**går du till [KEK-scenarierna (Key Encryption Key Key- och nyckelkrypteringsnyckeln).](#key-encryption-key-scenario)

    Nu när du har namnet på BEK-filen för enheten måste du skapa det hemliga filnamnet. BEK-fil för att låsa upp enheten.

6.  Hämta BEK-filen till återställningsdisken. I följande exempel sparas BEK-filen i mappen C:\BEK. Kontrollera att `C:\BEK\` sökvägen finns innan du kör skripten.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Om du vill låsa upp den bifogade disken med hjälp av BEK-filen kör du följande kommando.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. Kontrollera att du använder rätt enhetsbeteckning. 

8. När disken har låsts upp med hjälp av BEK-nyckeln kopplar du bort disken från återställningsdatorn och återskapar sedan den virtuella datorn med den här nya OS-disken.

    > [!NOTE]
    > Att byta OS-disk stöds inte för virtuella datorer med diskkryptering.

9. Om den nya virtuella datorn fortfarande inte kan starta normalt kan du prova något av följande steg när du har låst upp enheten:

    - Pausa skyddet för att tillfälligt stänga av BitLocker OFF genom att köra följande:

                    manage-bde -protectors -disable F: -rc 0
           
    - Dekryptera enheten helt. Gör detta genom att köra följande kommando:

                    manage-bde -off F:

### <a name="key-encryption-key-scenario"></a>Scenario för nyckelkrypteringsnyckel

SÃ¥Ã¤r ett nyckelkrypteringsnyckelscenario:

1. Kontrollera att det inloggade användarkontot kräver behörigheten "oförpackade" i principerna för åtkomst till Key Vault i **USER| Viktiga behörigheter| Kryptografiska operationer| Packa upp nyckel**.
2. Spara följande skript i en . PS1-fil:

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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
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
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
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
3. Ställ in parametrarna. Skriptet bearbetar KEK-hemligheten för att skapa BEK-nyckeln och sparar den sedan i en lokal mapp på återställningsdatorn. Om du får felmeddelanden när du kör skriptet läser du felsökningsavsnittet för [skriptet.](#script-troubleshooting)

4. Följande utdata visas när skriptet börjar:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    När skriptet är klart visas följande utdata:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Om du vill låsa upp den bifogade disken med hjälp av BEK-filen kör du följande kommando:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. Kontrollera att du använder rätt enhetsbeteckning. 

6. När disken har låsts upp med hjälp av BEK-nyckeln kopplar du bort disken från återställningsdatorn och återskapar sedan den virtuella datorn med den här nya OS-disken. 

    > [!NOTE]
    > Att byta OS-disk stöds inte för virtuella datorer med diskkryptering.

7. Om den nya virtuella datorn fortfarande inte kan starta normalt kan du prova något av följande steg när du har låst upp enheten:

    - Pausa skyddet för att tillfälligt aktivera BitLocker OFF genom att köra följande kommando:

             manage-bde -protectors -disable F: -rc 0
           
    - Dekryptera enheten helt. Gör detta genom att köra följande kommando:

                    manage-bde -off F:
## <a name="script-troubleshooting"></a>Felsökning av skript

**Fel: Det gick inte att läsa in filen eller sammansättningen**

Det här felet beror på att sökvägarna för ADAL-sammansättningarna är felaktiga. Om AZ-modulen bara är installerad för den aktuella användaren `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>`finns ADAL-sammansättningarna i .

Du kan också `Az.Accounts` söka efter mapp för att hitta rätt sökväg.

**Fel: Get-AzKeyVaultSecret eller Get-AzKeyVaultSecret känns inte igen som namnet på en cmdlet**

Om du använder den gamla AZ PowerShell-modulen måste `Get-AzureKeyVaultSecret` `Get-AzureKeyVaultSecret`du ändra de två kommandona till och .

**Exempel på parametrar**

| Parametrar  | Exempel på värde  |Kommentarer   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Namnet på nyckeln Arkiv som lagrar nyckeln |
|$kekName   |Mykey   | Namnet på nyckeln som används för att kryptera den virtuella datorn|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Namnet på hemligheten med vm-nyckeln|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. Bek |Sökvägen för att skriva BEK-fil.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN eller GUID för din Azure Active Directory som är värd för nyckelvalvet |
