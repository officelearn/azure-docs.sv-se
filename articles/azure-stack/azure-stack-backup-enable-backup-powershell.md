---
title: Aktivera säkerhetskopiering för Azure Stack med PowerShell | Microsoft Docs
description: Aktivera tjänsten infrastruktur för säkerhetskopiering med Windows PowerShell så att Azure Stack kan återställas om det uppstår ett fel.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 8fe7f0ddd630cfca0242af6cc1d728bdef163352
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058827"
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
| $password       | Skriv den **lösenord** för användaren. |
| $sharepath      | Ange sökvägen till den **lagringsplats för säkerhetskopiering**. Du måste använda en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. För att säkerställa tillgängligheten för säkerhetskopierade data, måste enheten vara i en separat plats. |
| $frequencyInHours | Hur ofta i timmar bestämmer hur ofta säkerhetskopiering skapas. Standardvärdet är 12. Scheduler har stöd för upp till 12 och minst 4.|
| $retentionPeriodInDays | Kvarhållningsperiod i dagar Anger hur många dagar säkerhetskopior bevaras på den externa platsen. Standardvärdet är 7. Scheduler har stöd för upp till 14 och minst 2. Säkerhetskopieringar som är äldre än kvarhållningsperioden automatiskt tas bort från den externa platsen.|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Bekräfta inställningar för säkerhetskopiering

Kör följande kommandon i samma PowerShell-session:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

Resultatet bör se ut som följande Exempelutdata:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Uppdatera inställningar för säkerhetskopiering
Du kan uppdatera standardvärdena för kvarhållningsperiod och frekvens för säkerhetskopiering i samma PowerShell-session. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Resultatet bör se ut som följande Exempelutdata:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>Nästa steg

 - Lär dig att köra en säkerhetskopiering, se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Lär dig att verifiera att säkerhetskopieringen har körts, se [bekräfta säkerhetskopieringen slutfördes i administrationsportalen](azure-stack-backup-back-up-azure-stack.md ).
