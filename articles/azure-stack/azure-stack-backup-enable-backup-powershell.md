---
title: Aktivera säkerhetskopiering för Azure Stack med PowerShell | Microsoft Docs
description: Aktivera tjänsten infrastruktur för säkerhetskopiering med Windows PowerShell så att Azure Stack kan återställas om det uppstår ett fel.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4fb40904e59e78e416d4598472a6adeb498e49f4
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968892"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Aktivera säkerhetskopiering för Azure Stack med PowerShell

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Aktivera tjänsten infrastruktur för säkerhetskopiering med Windows PowerShell så gör regelbundna säkerhetskopieringar av:
 - Intern identitetscertifikat för tjänsten och rot
 - Användaravtal, erbjudanden och prenumerationer
 - Keyvault-hemligheter
 - Användaren RBAC-roller och principer

Du kan komma åt PowerShell-cmdletar för att aktivera säkerhetskopiering av, starta säkerhetskopiering och få säkerhetskopierad information via operatorn hanteringsslutpunkten.

## <a name="prepare-powershell-environment"></a>Förbereda PowerShell-miljö

Anvisningar om hur du konfigurerar PowerShell-miljö finns i [installera PowerShell för Azure Stack ](azure-stack-powershell-install.md). Om du vill logga in på Azure Stack, se [konfigurera operator-miljö och logga in på Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Ange säkerhetskopieringsnyckel för resursen, autentiseringsuppgifter och kryptering för att aktivera säkerhetskopiering

Redigera följande PowerShell-skript i samma PowerShell-session genom att lägga till variabler för din miljö. Kör det uppdaterade skriptet för att ge säkerhetskopieringstjänsten infrastruktur säkerhetskopieringsnyckel resursen, autentiseringsuppgifter och kryptering.

| Variabel        | Beskrivning   |
|---              |---                                        |
| $username       | Skriv den **användarnamn** med domänen och användarnamnet för den delade enhetsplatsen med tillräcklig behörighet att läsa och skriva filer. Till exempel `Contoso\backupshareuser`. |
| $key            | Skriv den **krypteringsnyckeln** används för att kryptera varje säkerhetskopiering. |
| $password       | Skriv den **lösenord** för användaren. |
| $sharepath      | Ange sökvägen till den **lagringsplats för säkerhetskopiering**. Du måste använda en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. För att säkerställa tillgängligheten för säkerhetskopierade data, måste enheten vara i en separat plats. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Bekräfta inställningar för säkerhetskopiering

Kör följande kommandon i samma PowerShell-session:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

Resultatet bör se ut som följande:

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>Nästa steg

 - Lär dig att köra en säkerhetskopiering, se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Lär dig att verifiera att säkerhetskopieringen har körts, se [bekräfta säkerhetskopieringen slutfördes i administrationsportalen](azure-stack-backup-back-up-azure-stack.md ).
