---
title: "Kör ett verifieringstest i Azure-stacken | Microsoft Docs"
description: "Hur du samlar in loggfiler för diagnostik i Azure-stacken"
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: mabrigg
ms.openlocfilehash: 4f86397d4db5a0e67b294befd92087166d6b8109
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Kör ett verifieringstest för Azure-Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*
 
Du kan verifiera statusen för din Azure-stacken. När du har ett problem kontaktar du Microsoft Support Services. Stöd för uppmanas du att köra testet AzureStack från management-nod. Verifieringstest isolerar felet. Stöd kan sedan analysera detaljerade loggar fokusera på området där felet uppstod och arbeta med dig för att lösa problemet.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

När du har ett problem kontaktar Microsoft Support Services och kör sedan **kör Test-AzureStack**.

1. Du har ett problem.
2. Kontakta Microsoft Customer Services Support.
3. Kör **Test AzureStack** från den privilegierade slutpunkten.
    1. Komma åt den privilegierade slutpunkten. Instruktioner finns i [med Privilegierade slutpunkten i Azure-stacken](azure-stack-privileged-endpoint.md). 
    2. Logga in som **AzureStack\CloudAdmin** på management-värden.
    3. Öppna PowerShell som administratör.
    4. Kör: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Kör: `Test-AzureStack`
4. Om alla tester rapportera misslyckas, kör: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` cmdlet samlar in loggar från Test AzureStack. Läs mer om diagnostikloggar [Azure Stack diagnosverktyg](azure-stack-diagnostics.md).
5. Skicka den **SeedRing** loggar till Microsofts kundsupport för tjänster. Microsofts kundsupport tjänster hjälper dig att lösa problemet.

## <a name="reference-for-test-azurestack"></a>Referens för Test-AzureStack

Det här avsnittet innehåller en översikt för cmdleten Test-AzureStack och en sammanfattning av verifieringsrapporten.

### <a name="test-azurestack"></a>Test-AzureStack

Kontrollerar statusen för Azure-stacken. Cmdlet rapporterar status för din Azure-stacken maskinvara och programvara. Supportpersonal kan använda den här rapporten för att minska tiden för att lösa Azure Stack Supportfall.

> [!Note]  
> Testa AzureStack kan identifiera fel som inte leder till avbrott i molnet, t.ex. Det gick inte att en enskild disk eller en enda fysisk värd nodfel.

#### <a name="syntax"></a>Syntax

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parametrar

| Parameter               | Värde           | Krävs | Standard |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | Nej       | FALSKT   |
| DoNotDeployTenantVm     | SwitchParameter | Nej       | FALSKT   |
| AdminCredential         | PSCredential    | Nej       | Ej tillämpligt      |
<!-- | StorageConnectionString | Sträng          | Nej       | Ej tillämpligt      | stöds inte i 1802-->
| Visa lista                    | SwitchParameter | Nej       | FALSKT   |
| Ignorera                  | Sträng          | Nej       | Ej tillämpligt      |
| Ta med                 | Sträng          | Nej       | Ej tillämpligt      |

Cmdleten Test-AzureStack stöder de gemensamma parametrarna: utförlig, felsökning, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable och OutVariable. Mer information finns i [om gemensamma parametrar](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Exempel på Test AzureStack

Följande exempel förutsätter att du har loggat in som **CloudAdmin** och komma åt den privilegierade slutpunkten (program). Instruktioner finns i [med Privilegierade slutpunkten i Azure-stacken](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Kör Test-AzureStack interaktivt utan scenarier

I en session med program, kör du:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Kör Test-AzureStack med scenarier

Du kan använda AzureStack Test för att köra scenarier mot Azure-stacken. Några vanliga scenarier:

 - Skapa resursgrupper
 - Skapa planer
 - Skapa erbjudanden
 - Skapa storage-konton
 - Skapar en virtuell dator
 - Utföra blob-åtgärder med hjälp av lagringskonto som skapats i Testscenario
 - Utföra åtgärder för kön med hjälp av lagringskonto som skapats i Testscenario
 - Utföra åtgärder för tabeller med hjälp av lagringskonto som skapats i Testscenario

Molnet scenarier kräver administratörsbehörighet för molnet. 
> [!Note]  
> Du kan inte köras i scenarier med hjälp av Active Directory Federation Services (ADFS) autentiseringsuppgifter. Den **Test AzureStack** cmdlet är endast tillgänglig via detta program. Men detta program har inte stöd för AD FS-autentiseringsuppgifter.

Skriv administratörsanvändarnamn för molnet i UPN-format serviceadmin@contoso.onmicrosoft.com (AAD). När du uppmanas, ange lösenordet för administratörskontot för molnet.

I en session med program, kör du:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Kör Test-AzureStack utan scenarier

I en session med program, kör du:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Lista över tillgängliga test-scenarier:

I en session med program, kör du:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Köra ett angivet test

I en session med program, kör du:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

För att utesluta specifika test:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Verifieringstest

I följande tabell sammanfattas verifieringstesterna kör genom att testa AzureStack.

| Namn                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure-stacken av molnbaserade värdtjänster infrastruktur sammanfattning                                                                                  |
| Azure Stack Storage Services Summary                                                                                              |
| Azure-stacken infrastruktur Rollsammanfattning instans                                                                                  |
| Azure-stacken av molnbaserade värdtjänster infrastruktur för användning                                                                              |
| Azure-stacken infrastrukturkapacitet                                                                                               |
| Stacken Azure Portal och API-översikten                                                                                                |
| Azure stacken Azure Resource Manager certifikat sammanfattning                                                                                               |
| Infrastruktur för baskort, nätverksstyrenheten, Storage-tjänster och Privilegierade endpoint Infrastrukturroller          |
| Infrastruktur för baskort, nätverksstyrenheten, Storage-tjänster och Privilegierade endpoint infrastruktur Rollinstanser |
| Azure-stacken infrastruktur Rollsammanfattning                                                                                           |
| Azure Stack Cloud Service Fabric Services                                                                                         |
| Azure-stacken infrastruktur rollen instans prestanda                                                                              |
| Prestanda för Azure-stacken molnet värden                                                                                        |
| Sammanfattning av Azure Stack tjänst resurs förbrukning                                                                                  |
| Azure-stacken skala enhet kritiska händelser (senaste 8 timmar)                                                                             |
| Sammanfattning av Azure Stack lagring tjänster fysiska diskar                                                                               |

## <a name="next-steps"></a>Nästa steg

 - Läs mer om Azure-stacken diagnosverktyg och problemet loggning i [ Azure Stack diagnosverktyg](azure-stack-diagnostics.md).
 - Mer information om felsökning finns [felsökning av Microsoft Azure-stacken](azure-stack-troubleshooting.md)