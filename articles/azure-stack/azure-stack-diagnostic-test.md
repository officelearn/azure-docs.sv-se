---
title: Kör ett verifieringstest i Azure Stack | Microsoft Docs
description: Så här att samla in loggfiler för diagnostik i Azure Stack.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 11/02/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 1726b7d1ac6a4c39e7a8dab733644d01e5f3381e
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957516"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Kör ett verifieringstest för Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*
 
Du kan verifiera statusen för din Azure Stack. När du har ett problem, kontaktar du Microsoft Support för tjänster. Support ber dig att köra **Test AzureStack** från din management-nod. Verifieringstest isolerar felet. Stöd kan sedan analysera detaljerade loggar, fokusera på området där felet uppstod och arbeta med dig för att lösa problemet.

## <a name="run-test-azurestack"></a>Kör Test-AzureStack

När du har ett problem kontaktar Microsoft Support för tjänster och kör sedan **kör Test-AzureStack**.

1. Du har ett problem.
2. Kontakta Microsoft kundsupport Services Support.
3. Kör **Test AzureStack** från Privilegierade slutpunkten.
    1. Åtkomst till privilegierad slutpunkt. Anvisningar finns i [med hjälp av privilegierad slutpunkt i Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Logga in på ASDK på management-värden som **AzureStack\CloudAdmin**.  
    På ett integrerat system behöver du använda IP-adressen för privilegierad--slutpunkten för hantering som du fått av maskinvaruleverantören OEM.
    3. Öppna PowerShell som administratör.
    4. Kör: `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. Kör: `Test-AzureStack`
4. Om någon testar rapporten **MISSLYCKAS**, kör: `Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath “<path>” -OutputShareCredential $cred` cmdleten samlar in loggarna från Test-AzureStack. Läs mer om diagnostikloggar [Azure Stack diagnosverktyg](azure-stack-diagnostics.md). Du bör inte samla in loggar eller kontakta Microsofts kundsupport tjänster (CSS) om testar rapporten **WARN**.
5. Skicka den **SeedRing** loggar till Microsoft kundsupport för tjänster. Microsofts kundsupport tjänster hjälper dig att lösa problemet.

## <a name="reference-for-test-azurestack"></a>Referens för Test-AzureStack

Det här avsnittet innehåller en översikt för cmdleten Test-AzureStack och en sammanfattning av verifieringsrapporten.

### <a name="test-azurestack"></a>Test-AzureStack

Kontrollerar status för Azure Stack. Cmdlet: en rapporterar status för dina Azure Stack-maskinvara och programvara. Supportpersonalen kan använda den här rapporten för att minska tiden för att lösa supportärenden för Azure Stack.

> [!Note]  
> **Test-AzureStack** kan upptäcka fel som inte leder till avbrott i molnet, t.ex. Det gick inte att en enskild disk eller en enda fysisk värd nodfel.

#### <a name="syntax"></a>Syntax

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parametrar

| Parameter               | Värde           | Krävs | Standard |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | Sträng    | Nej       | FALSKT   |
| DoNotDeployTenantVm     | SwitchParameter | Nej       | FALSKT   |
| AdminCredential         | PSCredential    | Nej       | Ej tillämpligt      |
| Visa lista                    | SwitchParameter | Nej       | FALSKT   |
| Ignorera                  | Sträng          | Nej       | Ej tillämpligt      |
| Ta med                 | Sträng          | Nej       | Ej tillämpligt      |
| BackupSharePath         | Sträng          | Nej       | Ej tillämpligt      |
| BackupShareCredential   | PSCredential    | Nej       | Ej tillämpligt      |


Cmdleten Test-AzureStack har stöd för parametrarna: utförlig, felsökning, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable och OutVariable. Mer information finns i [om gemensamma parametrar](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Exempel på Test-AzureStack

I följande exempel förutsätter att du har loggat in som **CloudAdmin** och komma åt den privilegierade slutpunkten (program). Anvisningar finns i [med hjälp av privilegierad slutpunkt i Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Kör Test-AzureStack interaktivt utan cloud-scenarier

I en program-session kör du:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Kör Test-AzureStack med cloud-scenarier

Du kan använda **Test AzureStack** att köra cloud scenarier mot din Azure Stack. Några vanliga scenarier:

 - Skapa resursgrupper
 - Skapa planer
 - Skapa erbjudanden
 - Skapa lagringskonton
 - Skapa en virtuell dator
 - Utföra blobåtgärder med storage-konto som skapats i Testscenario
 - Utföra åtgärder för kön med hjälp av storage-konto som skapats i Testscenario
 - Utföra åtgärder för tabeller med hjälp av storage-konto som skapats i Testscenario

Cloud-scenarier kräver administratörsbehörighet i molnet. 
> [!Note]  
> Du kan inte köra cloud-scenarier med autentiseringsuppgifter för Active Directory Federation Services (AD FS). Den **Test AzureStack** cmdlet kan endast nås via detta program. Men detta program stöder inte AD FS-autentiseringsuppgifter.

Skriv administratörsanvändarnamnet för molnet i UPN-format serviceadmin@contoso.onmicrosoft.com (Azure AD). När du uppmanas, skriver du lösenordet för administratörskontot för molnet.

I en program-session kör du:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Kör Test-AzureStack utan cloud-scenarier

I en program-session kör du:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Lista över tillgängliga test-scenarier:

I en program-session kör du:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Köra ett angivet test

I en program-session kör du:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Att undanta specifika test:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>Kör Test-AzureStack om du vill testa inställningar för säkerhetskopiering av infrastruktur

Innan du konfigurerar säkerhetskopiering av infrastruktur, kan du testa sökvägen till säkerhetskopierade och autentiseringsuppgifter med hjälp av den **AzsBackupShareAccessibility** testa.

I en program-session kör du:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
När du har konfigurerat säkerhetskopiering, kan du köra AzsBackupShareAccessibility att verifiera resursen är tillgänglig från ERCS från en session för program som kör:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

Om du vill testa kör nya autentiseringsuppgifter med den konfigurera säkerhetskopieringsresursen från en program-session du:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>Verifieringstest

I följande tabell sammanfattas verifieringstester som körs av **Test-AzureStack**.

| Namn                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure Stack-moln som är värd för infrastruktur-sammanfattning                                                                                  |
| Sammanfattning för Azure Stack Storage-tjänster                                                                                              |
| Azure Stack-infrastruktur Rollsammanfattning instans                                                                                  |
| Azure Stack-moln som är värd för användning av infrastruktur                                                                              |
| Kapacitet för Azure Stack-infrastruktur                                                                                               |
| Azure Stack-portalen och API-översikten                                                                                                |
| Azure Stack Certifikatöversikt för Azure Resource Manager                                                                                               |
| Infrastruktur för baskort, nätverksstyrenheten, lagringstjänster och privilegierad slutpunkt Infrastrukturroller          |
| Infrastruktur för baskort, nätverksstyrenheten, lagringstjänster och privilegierad slutpunkt Rollinstanser för infrastruktur |
| Azure Stack-infrastruktur Rollsammanfattning                                                                                           |
| Azure Stack Cloud Service Fabric-tjänster                                                                                         |
| Prestanda för Azure Stack-infrastruktur rollen instanser                                                                              |
| Azure Stack Molnöversikt värden prestanda                                                                                        |
| Azure Stack-tjänsten Resurssammanfattning förbrukning                                                                                  |
| Azure Stack skala enhet kritiska händelser (senaste 8 timmar)                                                                             |
| Sammanfattning för Azure Stack Storage tjänster fysiska diskar                                                                               |
|Azure Stack Säkerhetskopieringsresursen hjälpmedel sammanfattning                                                                                     |

## <a name="next-steps"></a>Nästa steg

 - Läs mer om Azure Stack diagnosverktyg och problemet loggning i [ Azure Stack diagnosverktyg](azure-stack-diagnostics.md).
 - Mer information om felsökning finns [felsökning av Microsoft Azure Stack](azure-stack-troubleshooting.md)
