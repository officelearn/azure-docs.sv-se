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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 38ab7b80e2f03176c3bedfd98a2d0e20fc02592b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865900"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Aktivera säkerhetskopiering för Azure Stack med PowerShell

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Aktivera tjänsten infrastruktur för säkerhetskopiering med Windows PowerShell så gör regelbundna säkerhetskopieringar av:
 - Intern identitetscertifikat för tjänsten och rot
 - Användaravtal, erbjudanden och prenumerationer
 - Beräkning, lagring och nätverk användarkvoter
 - Användaren Key vault-hemligheter
 - Användaren RBAC-roller och principer
 - Storage-konton för användare

Du kan komma åt PowerShell-cmdletar för att aktivera säkerhetskopiering av, starta säkerhetskopiering och få säkerhetskopierad information via operatorn hanteringsslutpunkten.

## <a name="prepare-powershell-environment"></a>Förbereda PowerShell-miljö

Anvisningar om hur du konfigurerar PowerShell-miljö finns i [installera PowerShell för Azure Stack](azure-stack-powershell-install.md). Om du vill logga in på Azure Stack, se [konfigurera operator-miljö och logga in på Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Ange säkerhetskopieringsnyckel för resursen, autentiseringsuppgifter och kryptering för att aktivera säkerhetskopiering

Redigera följande PowerShell-skript i samma PowerShell-session genom att lägga till variabler för din miljö. Kör det uppdaterade skriptet för att ge säkerhetskopieringstjänsten infrastruktur säkerhetskopieringsnyckel resursen, autentiseringsuppgifter och kryptering.

| Variabel        | Beskrivning   |
|---              |---                                        |
| $username       | Skriv den **användarnamn** med domänen och användarnamnet för den delade enhetsplatsen med tillräcklig behörighet att läsa och skriva filer. Till exempel `Contoso\backupshareuser`. |
| $password       | Skriv den **lösenord** för användaren. |
| $sharepath      | Ange sökvägen till den **lagringsplats för säkerhetskopiering**. Du måste använda en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. För att säkerställa tillgängligheten för säkerhetskopierade data, måste enheten vara i en separat plats. |
| $frequencyInHours | Hur ofta i timmar bestämmer hur ofta säkerhetskopiering skapas. Standardvärdet är 12. Scheduler har stöd för upp till 12 och minst 4.|
| $retentionPeriodInDays | Kvarhållningsperiod i dagar Anger hur många dagar säkerhetskopior bevaras på den externa platsen. Standardvärdet är 7. Scheduler har stöd för upp till 14 och minst 2. Säkerhetskopieringar som är äldre än kvarhållningsperioden automatiskt tas bort från den externa platsen.|
| $encryptioncertpath | Kryptering certifikatsökväg anger sökvägen till den. CER-fil med offentlig nyckel som används för kryptering av data. |
|     |     |

```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
   
##  <a name="confirm-backup-settings"></a>Bekräfta inställningar för säkerhetskopiering

Kör följande kommandon i samma PowerShell-session:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
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

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Resultatet bör se ut som följande Exempelutdata:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

###<a name="azure-stack-powershell"></a>Azure Stack PowerShell 
PowerShell-cmdlet för att konfigurera infrastruktur för säkerhetskopiering är Set-AzsBackupConfiguration. I tidigare versioner kunde cmdleten Set-AzsBackupShare. Denna cmdlet kräver att tillhandahålla ett certifikat. Om infrastruktur för säkerhetskopiering är konfigurerad med en krypteringsnyckel, kan du uppdatera krypteringsnyckeln eller visar egenskapen. Du måste använda Admin PowerShell-version 1.6. 

Om infrastruktur för säkerhetskopiering har konfigurerats innan du uppdaterar till 1901, kan du använda version 1.6 av Admin PowerShell för att ställa in och visa krypteringsnyckeln. Version 1.6 kan inte uppdatera från krypteringsnyckeln till en certifikatfil.
Referera till [installera Azure Stack PowerShell](azure-stack-powershell-install.md) för mer information om hur du installerar rätt version av modulen. 


## <a name="next-steps"></a>Nästa steg

Lär dig att köra en säkerhetskopiering, se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Lär dig att verifiera att säkerhetskopieringen har körts, se [bekräfta säkerhetskopieringen slutfördes i administrationsportalen](azure-stack-backup-back-up-azure-stack.md)
