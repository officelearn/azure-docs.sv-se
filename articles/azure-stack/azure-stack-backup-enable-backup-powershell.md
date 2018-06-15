---
title: Aktivera säkerhetskopiering för Azure-stacken med PowerShell | Microsoft Docs
description: Aktivera tjänsten infrastruktur säkerhetskopiering med Windows PowerShell så att Azure-stacken kan återställas om det uppstår ett fel.
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
ms.openlocfilehash: 5fab656734d0984cf44a9fe1f29fd73530bd9aa8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259863"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Aktivera säkerhetskopiering för Azure-stacken med PowerShell

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Aktivera tjänsten infrastruktur säkerhetskopiering med Windows PowerShell så ta periodiska säkerhetskopieringar av:
 - Internt identitetscertifikat för tjänsten och rot
 - Användarplaner, erbjudanden, prenumerationer
 - Keyvault hemligheter
 - RBAC användarroller och principer

Du kan öppna PowerShell-cmdlets för att aktivera säkerhetskopiering, starta säkerhetskopiering och få säkerhetskopierad information via hanteringsslutpunkten operator.

## <a name="prepare-powershell-environment"></a>Förbereda PowerShell-miljö

Instruktioner om hur du konfigurerar PowerShell-miljö finns i [installera PowerShell för Azure-stacken ](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Generera en ny krypteringsnyckel

Installera och konfigurerade PowerShell för Azure-stacken och Azure Stack-verktyg.
 - Se [komma igång med PowerShell i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - Se [hämta Azure Stack-verktyg från GitHub](azure-stack-powershell-download.md)

Öppna Windows PowerShell med en förhöjd behörighet och kör följande kommandon:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
Kör följande kommandon i samma PowerShell-session:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Du måste använda AzureStack-verktyg för att generera nyckeln.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Ange reservnyckel för resursen, autentiseringsuppgifter och kryptering för att aktivera säkerhetskopiering

Redigera följande PowerShell-skript i samma PowerShell-session genom att lägga till variabler för din miljö. Kör skriptet uppdaterade för att förse infrastruktur säkerhetskopieringstjänsten reservnyckel resursen, autentiseringsuppgifter och kryptering.

| Variabel        | Beskrivning   |
|---              |---                                        |
| $username       | Typ av **användarnamn** med domänen och användarnamnet för den delade enhetsplatsen med behörighet att läsa och skriva filer. Till exempel `Contoso\backupshareuser`. |
| $password       | Typ av **lösenord** för användaren. |
| $sharepath      | Ange sökvägen till den **säkerhetskopiera lagringsplats**. Du måste använda en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs som finns på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. Enheten måste vara i en annan plats för att säkerställa tillgängligheten för säkerhetskopierade data. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Bekräfta inställningarna för säkerhetskopiering

Kör följande kommandon i samma PowerShell-session:

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
   ```

Resultatet bör se ut som följande JSON-utdata:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Nästa steg

 - Lär dig att köra en säkerhetskopiering, se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Lär dig att kontrollera att din säkerhetskopieringen, se [bekräfta säkerhetskopieringen har slutförts i administrationsportalen](azure-stack-backup-back-up-azure-stack.md ).
