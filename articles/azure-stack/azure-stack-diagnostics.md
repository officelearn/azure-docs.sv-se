---
title: Diagnostik i Azure Stack
description: Att samla in loggfiler för diagnostik i Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/13/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: f9a7ae76f2d52b3439bfb33f306e164bb81549eb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623986"
---
# <a name="azure-stack-diagnostics-tools"></a>Verktyg för Azure Stack-diagnostik

Azure Stack är ett stort antal komponenter fungerar tillsammans och interagera med varandra. Alla dessa komponenter genererar sina egna unika loggar. Detta kan göra diagnostisera problem med en utmaning, särskilt för fel som kommer från flera interagera Azure Stack-komponenterna. 

Med våra verktyg för diagnostik kan kontrollera mekanismen för log-samling är enkelt och effektivt. Följande diagram visar logga hur samling verktyg i Azure Stack fungerar:

![Diagnostiska verktyg för Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Spåra insamlaren
 
Spårningen insamlaren är aktiverad som standard och körs kontinuerligt i bakgrunden för att samla in alla för Windows ETW (Event Tracing) loggar från Azure Stack Komponenttjänster. ETW-loggar lagras i en gemensam lokal resurs med fem dagars Åldersgräns. När den här gränsen har nåtts, raderas de äldsta filerna när nya skapas. Den standard maximala tillåtna storleken för varje fil är 200 MB. En storlek på kontrollen görs varannan minut, och om den aktuella filen är > = 200 MB, sparas och en ny fil skapas. Det finns också en gräns på 8 GB på den totala filstorleken som genereras per händelsesessionen. 

## <a name="log-collection-tool"></a>Samling av loggverktyget
 
PowerShell-cmdleten **Get-AzureStackLog** kan användas för att samla in loggar från alla komponenter i en Azure Stack-miljö. De sparas i zip-filer i en användardefinierad plats. Om Azure Stack teknisk support-teamet behöver dina loggar för att felsöka ett problem, de kan bli ombedd att köra det här verktyget.

> [!CAUTION]
> Dessa loggfiler kan innehålla personligt identifierbar information (PII). Hänsyn tas det innan du publicerar offentligt alla loggfiler.
 
Här följer några exempel loggtyper som samlas in:
*   **Azure Stack-distributionsloggar**
*   **Windows-händelseloggar**
*   **Panther loggar**
*   **Kluster-loggar**
*   **Storage-diagnostikloggar**
*   **ETW-loggar**

Dessa filer som samlas in och sparas på en resurs Trace-insamlaren. Den **Get-AzureStackLog** PowerShell-cmdleten kan sedan användas för att samla in dem vid behov.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Om du vill köra Get-AzureStackLog på Azure Stack integrerade system 
Du måste ha åtkomst till privilegierad slutpunkt (program) om du vill köra verktyget log samling på ett integrerat system. Här är ett exempelskript som du kan köra med hjälp av detta program att samla in loggar på ett integrerat system:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```
- Parametrarna **OutputSharePath** och **OutputShareCredential** används för att lagra loggar i en användardefinierad plats.
- Den **FromDate** och **ToDate** parametrar kan användas för att samla in loggar för en viss tidsperiod. Om dessa parametrar inte anges, insamlade loggar för de senaste fyra timmarna som standard.


### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Att köra Get-AzureStackLog på ett Azure Stack Development Kit (ASDK)
1. Logga in som **AzureStack\CloudAdmin** på värden.
2. Öppna ett PowerShell-fönster som administratör.
3. Kör den **Get-AzureStackLog** PowerShell-cmdlet.

**Exempel:**

  Samla in alla loggar för alla roller:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  Samla in loggar från virtuella datorer och BareMetal roller:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  Samla in loggar från virtuella datorer och BareMetal, med Datumfiltrering för loggfiler för de senaste 8 timmarna:
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Samla in loggar från virtuella datorer och BareMetal, med Datumfiltrering för loggfiler för tidsperioden mellan 8 timmar sedan och 2 timmar sedan:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Parametern överväganden för både ASDK och integrerade system

- Om den **FromDate** och **ToDate** parametrar har angetts, loggar samlas in för de senaste fyra timmarna som standard.
- Använd den **FilterByNode** parameter för att filtrera loggar efter datornamn. Exempel:

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```
- Använd den **FilterByLogType** parameter för att filtrera loggar av typen. Du kan välja att filtrera efter fil, resurs eller WindowsEvent. Exempel:

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByLogType File
    ```
- Du kan använda den **TimeOutInMinutes** parametern ange tidsgränsen för Logginsamling. Den är inställd på 150 (2,5 timmar) som standard.
- Dump filinsamling loggen är inaktiverad som standard. Aktivera det genom att använda den **IncludeDumpFile** växla parametern. 
- För närvarande kan du kan använda den **FilterByRole** parameter för att filtrera Logginsamling av följande roller:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS                   |CacheService                   |IBC                            |OEM|
 |ACSDownloadService    |Compute                        |InfraServiceController         |OnboardRP|
 |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
 |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
 |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
 |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
 |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices|
 |ACSSettingsService    |FN                            |KeyVaultNamingService          |SLB|
 |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
 |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
 |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage|
 |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
 |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
 |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
 |AzureBridge           |FRP                            |MonRP                          |virtuella datorer   |
 |AzureMonitor          |Gateway                        |NC                             |VAR|
 |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
 |BRP                   |HintingServiceV2               |NRP                            |   |
 |CA                    |HRP                            |OboService                     |   |
 |   |   |   |    |

### <a name="additional-considerations"></a>Annat som är bra att tänka på

* Kommandot tar lite tid att köra baserat på vilka roller som samlar in loggarna. Bidragande faktorer kan även innehålla tidslängd som angetts för Logginsamling och antalet noder i Azure Stack-miljön.
* Eftersom logga samling körningar, kontrollera den nya mappen som skapas i den **OutputSharePath** parameter har angetts i kommandot.
* Varje roll har dess loggar i enskilda zip-filer. Beroende på storleken på de insamlade loggarna kanske en roll loggar delas upp i att flera zip-filer. Använd ett verktyg som kan packa upp i grupp (till exempel 7zip) för en roll, om du vill ha alla loggfiler som packat i till en enda mapp. Välj alla komprimerade filer för rollen och **extrahera här**. Detta packar alla loggfiler för rollen i en enda sammanfogade mapp.
* En fil som heter **Get-AzureStackLog_Output.log** skapas också i mappen som innehåller de komprimerade loggfilerna. Den här filen är en logg över kommandoutdata, som kan användas vid felsökning av problem vid Logginsamling av. Ibland loggfilen innehåller `PS>TerminatingError` poster som kan ignoreras, såvida inte förväntade loggfiler saknas efter logga samling körningar.
* Om du vill undersöka specifika fel kan loggar behövas från fler än en komponent.
    -   System- och händelseloggarna för alla infrastrukturens virtuella datorer samlas in i den *VirtualMachines* roll.
    -   System- och händelseloggarna för alla värdar har samlats in i den *BareMetal* roll.
    -   Redundansklustret och Hyper-V-händelseloggar finns samlade på den *Storage* roll.
    -   ACS-loggarna samlas i den *Storage* och *ACS* roller.

> [!NOTE]
> Begränsningar för storlek och ålder tillämpas på loggarna som samlas in eftersom det är viktigt att säkerställa effektiv användning av ditt lagringsutrymme för att se till att den inte hämta svämmar över med loggar. Men vid diagnostisering av ett problem behöver du ibland loggar som inte finns längre på grund av dessa begränsningar. Därför är det **rekommenderas starkt** du avlasta loggarna till en extern lagringsutrymme (ett lagringskonto i Azure, en ytterligare en lokal lagringsenhet osv) var 8 och 12: e timme och håll dem det för 1 – 3 månader, beroende på din krav. Kontrollera också att den här lagringsplatsen är krypterad.

## <a name="next-steps"></a>Nästa steg
[Felsökning av Microsoft Azure Stack](azure-stack-troubleshooting.md)

