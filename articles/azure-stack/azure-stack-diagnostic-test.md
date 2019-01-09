---
title: Använd verktyget för Azure Stack-verifiering | Microsoft Docs
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
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 63a198b082c7486de2392153291a11be5bcb2f9e
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103231"
---
# <a name="validate-azure-stack-system-state"></a>Verifiera Azure Stack-system-tillstånd

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Som Azure Stack-operatör är det viktigt att ha möjlighet att veta hälsotillstånd och status för ditt system på begäran. Verktyget för Azure Stack-validering (**Test-AzureStack**) är en PowerShell-cmdlet som du kan köra en serie tester i systemet för att identifiera fel. Du kommer vanligtvis bli ombedd att köra det här verktyget den [privilegierad slutpunkt (program)](azure-stack-privileged-endpoint.md) när du kontaktar Microsofts kundsupport tjänster (CSS) med ett problem. Med systemomfattande hälsotillstånd och statusinformation till hands, CSS samla in och analysera detaljerade loggar kan fokusera på området där felet uppstod och arbetar med att lösa problemet.

## <a name="running-the-validation-tool-and-accessing-results"></a>Kör verktyget verifiering och komma åt resultaten

Som tidigare nämnts bör köra verktyget verifiering via detta program. Varje test returnerar en **PASS/FAIL** status i PowerShell-fönstret. Dessutom skapas en detaljerad HTML-rapport som senare kan nås under [insamling](azure-stack-diagnostics.md). Här är en översikt över slutpunkt till slutpunkt-processen för valideringstestning: 

1. Åtkomst till privilegierad slutpunkt (program). Kör följande kommandon för att upprätta en session med program:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > För att komma åt detta program på en värddator för ASDK, använder du AzS-ERCS01 för - ComputerName.

2. När du använder detta program kan köra: 

   ```powershell
   Test-AzureStack
   ```

   Referera till den [parametern överväganden](azure-stack-diagnostic-test.md#parameter-considerations) och [fallet baseras på exempel](azure-stack-diagnostic-test.md#use-case-examples) avsnitt för mer information.

3. Om någon testar rapporten **MISSLYCKAS**, kör:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   Cmdleten samlar in loggar som genereras av Test-AzureStack. Läs mer om diagnostikloggar [Azure Stack diagnosverktyg](azure-stack-diagnostics.md). Du bör inte samla in loggar eller kontakta CSS om testar rapporten **WARN**.

4. Om du uppmanades att köra verktyget Verifiering av CSS, begär CSS-representant loggarna som du samlade in om du vill fortsätta att felsöka ditt problem.

## <a name="tests-available"></a>Tester som är tillgängliga

Verktyget verifiering kan du köra en serie systemnivå tester och basic cloud-scenarier som ger dig insikter till aktuellt tillstånd och att kontrollera problem i systemet.

### <a name="cloud-infrastructure-tests"></a>Tester för moln-infrastruktur

Låg inverkan känslighetsgrad arbetar med en infrastrukturnivå och ger information om olika komponenter och funktioner. Testerna är för närvarande är grupperade i följande kategorier:

| Test-kategori                                        | Argumentet för - omfattar och - ignorera |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack Aviseringssammanfattning                            | AzsAlertSummary                   |
| Azure Stack Säkerhetskopieringsresursen hjälpmedel sammanfattning       | AzsBackupShareAccessibility       |
| Azure Stack kontroll plan sammanfattning                    | AzsControlPlane                   |
| Azure Stack Defender sammanfattning                         | AzsDefenderSummary                |
| Azure Stack som är värd för infrastruktur Firmware sammanfattning  | AzsHostingInfraFWSummary          |
| Azure Stack-moln som är värd för infrastruktur-sammanfattning     | AzsHostingInfraSummary            |
| Azure Stack-moln som är värd för användning av infrastruktur | AzsHostingInfraUtilization        |
| Kapacitet för Azure Stack-infrastruktur                  | AzsInfraCapacity                  |
| Prestanda för Azure Stack-infrastruktur               | AzsInfraPerformance               |
| Rollsammanfattning för Azure Stack-infrastruktur              | AzsInfraRoleSummary               |
| Sammanfattning av Azure Stack                           | AzsInfraUpdateSummary             |
| Azure Stack-portalen och API-översikten                   | AzsPortalAPISummary               |
| Azure Stack Skalningshändelser enhet VM                     | AzsScaleUnitEvents                |
| Azure Stack skala enhet VM-resurser                  | AzsScaleUnitResources             |
| Azure Stack SDN Verifieringssammanfattning                   | AzsSDNValidation                  |
| Azure Stack Service Fabric-Rollsammanfattning              | AzsSFRoleSummary                  |
| Azure Stack BMC-sammanfattning                              | AzsStampBMCSummary                |
| Sammanfattning för Azure Stack Storage-tjänster                 | AzsStorageSvcsSummary             |
| Azure Stack SQL Store sammanfattning                        | AzsStoreSummary                   |
| Azure Stack VM Placement Summary                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Molnet scenariot tester

Utöver infrastrukturen testerna ovan har du också möjlighet att köra cloud scenariot tester för att kontrollera funktioner för infrastrukturkomponenter. Molnautentiseringsuppgifter krävs för att köra dessa tester som de inkluderar resursdistributionen. 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

Följande scenarier för molnet testas av verktyget verifiering:
- Du skapar en resursgrupp   
- Skapa en plan              
- Skapa en erbjudande            
- Lagringskontot har skapats   
- Skapa en virtuell dator 
- BLOB storage-åtgärden   
- Queue storage åtgärden  
- Tabellåtgärd för lagring  

## <a name="parameter-considerations"></a>Parametern-överväganden

- Parametern **lista** kan användas för att visa alla tillgängliga test-kategorier.

- Parametrarna **inkludera** och **Ignorera** kan användas för att inkludera eller exkludera test kategorier. Se den [tester som är tillgängliga](azure-stack-diagnostic-test.md#tests-available) för mer information om snabbformat som ska användas med de här argumenten.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- En virtuell dator distribueras som en del av en klient testar för scenariot med molnet. Du kan använda **DoNotDeployTenantVm** att inaktivera det här. 

- Måste du ange den **ServiceAdminCredential** parameter för att köra tester för cloud-scenario som beskrivs i den [fallet baseras på exempel](azure-stack-diagnostic-test.md#use-case-examples) avsnittet.

- **BackupSharePath** och **BackupShareCredential** används när du testar inställningar för säkerhetskopiering av infrastruktur, enligt den [fallet baseras på exempel](azure-stack-diagnostic-test.md#use-case-examples) avsnittet.

- Verifiering verktyget stöder också vanliga PowerShell-parametrar: Utförlig, felsökning, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable, och OutVariable. Mer information finns i [om gemensamma parametrar](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Använd case-exemplen

### <a name="run-validation-without-cloud-scenarios"></a>Köra verifieringen utan cloud-scenarier

Kör verktyget Verifiering utan den **ServiceAdminCredential** parametern för att hoppa över aktiva molnet scenariot test: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Köra verifiering med cloud-scenarier

Med verktyget verifiering med den **ServiceAdminCredentials** parametern kör tester för cloud-scenario som standard: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Om du vill köra endast molnet scenarier utan att köra resten av testerna kan du använda den **inkludera** parametern för att göra det: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Molnet administratörens användarnamn måste anges i UPN-format: serviceadmin@contoso.onmicrosoft.com (Azure AD). När du uppmanas, skriver du lösenordet för administratörskontot för molnet.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>Kör verifieringen verktyget för att testa systemberedskap innan du installerar uppdatering eller snabbkorrigering

Innan du påbörjar installationen av en uppdatering eller snabbkorrigering bör du köra verktyget verifiering för att kontrollera status för Azure Stack:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Kör verifieringen verktyget för att testa inställningar för säkerhetskopiering av infrastruktur

*Innan du* Konfigurera infrastruktur för säkerhetskopiering kan du testa sökvägen till säkerhetskopierade och autentiseringsuppgifter med hjälp av den **AzsBackupShareAccessibility** testa: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Efter* Konfigurera säkerhetskopiering, kan du köra **AzsBackupShareAccessibility** att verifiera resursen är tillgänglig från ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Om du vill testa nya autentiseringsuppgifter med den konfigurera säkerhetskopieringsresursen, kör du: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Stack diagnosverktyg och problemet loggning i [Azure Stack diagnosverktyg](azure-stack-diagnostics.md).

Mer information om felsökning finns [felsökning av Microsoft Azure Stack](azure-stack-troubleshooting.md).