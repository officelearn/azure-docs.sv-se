---
title: "Aktivera säkerhetskopiering för Azure-stacken med PowerShell | Microsoft Docs"
description: "Aktivera infrastruktur tillbaka tjänsten med Windows PowerShell så att Azure-stacken kan återställas om det uppstår ett fel."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: b4f48b7fd07c5fb590b6989e04e9084c86142d2a
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Aktivera säkerhetskopiering för Azure-stacken med PowerShell

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Aktivera infrastruktur tillbaka tjänsten med Windows PowerShell så att Azure-stacken kan återställas om det uppstår ett fel. Du kan öppna PowerShell-cmdlets för att aktivera säkerhetskopiering, starta säkerhetskopiering och få säkerhetskopierad information via hanteringsslutpunkten operator.

## <a name="download-azure-stack-tools"></a>Hämta Azure Stack-verktyg

Installera och konfigurerade PowerShell för Azure-stacken och Azure Stack-verktyg. Se [komma igång med PowerShell i Azure-stacken](https://review.docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart).

##  <a name="load-the-connect-and-infrastructure-modules"></a>Läsa in modulerna Connect och infrastruktur

Öppna Windows PowerShell med en förhöjd behörighet och kör följande kommandon:

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Installationsprogrammet Rm-miljö och logg i operatorn management slutpunkten

Redigera följande PowerShell-skript i samma PowerShell-session genom att lägga till variabler för din miljö. Kör skriptet uppdaterade för att konfigurera RM-miljö och logga in på hanteringsslutpunkten operator.

| Variabel    | Beskrivning |
|---          |---          |
| $TenantName | Azure Active Directory-klientnamn. |
| Operatorn kontonamn        | Azure-stacken operatorn namnet på ditt konto. |
| Azure Resource Manager-slutpunkt | URL till Azure Resource Manager. |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Generera en ny krypteringsnyckel

Kör följande kommandon i samma PowerShell-session:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Ange reservnyckel för resursen, autentiseringsuppgifter och kryptering för att aktivera säkerhetskopiering

Redigera följande PowerShell-skript i samma PowerShell-session genom att lägga till variabler för din miljö. Kör skriptet uppdaterade för att förse infrastruktur säkerhetskopieringstjänsten reservnyckel resursen, autentiseringsuppgifter och kryptering.

| Variabel        | Beskrivning   |
|---              |---                                        |
| $username       | Typ av **användarnamn** med domänen och användarnamnet för platsen för delad enhet. Till exempel `Contoso\administrator`. |
| $password       | Typ av **lösenord** för användaren. |
| $sharepath      | Ange sökvägen till den **säkerhetskopiera lagringsplats**. Du måste använda en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs som finns på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. Enheten måste vara i en annan plats för att säkerställa tillgängligheten för säkerhetskopierade data. |

   ```powershell
   $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

Set-AzSBackupShare -Location $location -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey 

   ```
##  <a name="confirm-backup-settings"></a>Bekräfta inställningarna för säkerhetskopiering

Kör följande kommandon i samma PowerShell-session:

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
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
