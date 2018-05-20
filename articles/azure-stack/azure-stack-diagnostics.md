---
title: Diagnostik i Azure Stack
description: Hur du samlar in loggfiler för diagnostik i Azure-stacken
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 04/27/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 28e1939d3c9cb5a9b9080e60230ad5600ad8a6a3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack diagnosverktyg

Azure stacken är ett stort antal komponenter som arbetar tillsammans och interagera med varandra. Alla dessa komponenter genererar sina egna unika loggar. Detta kan göra diagnostisera problem utmaning, särskilt för fel som kommer från flera interagerar Azure Stack-komponenter. 

Vår diagnosverktyg säkerställer mekanism för logg-samling är enkelt och effektivt. Följande diagram visar logga hur samling verktyg i Azure-stacken arbete:

![Diagnostikverktyg för Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Spåra insamlaren
 
Spåra insamlaren är aktiverad som standard och körs kontinuerligt i bakgrunden för att samla in alla ETW Event Tracing for Windows ()-loggar från Azure-stacken Komponenttjänster. ETW-loggfilerna lagras i en gemensam lokal resurs med en övre gräns ålder fem dagar. När den här gränsen har nåtts, raderas de äldsta filerna när nya skapas. Den standard maximala tillåtna storleken för varje fil är 200 MB. En storlek kontrollen görs varannan minut, och om den aktuella filen är > = 200 MB sparas så skapas en ny fil. Det finns också en 8 GB gräns på den totala filstorleken genereras per händelsesessionen. 

## <a name="log-collection-tool"></a>Loggen samling verktyget
 
PowerShell-cmdleten **Get-AzureStackLog** kan användas för att samla in loggar från alla komponenter i en Azure-Stack-miljö. Det sparar dem i zip-filer i en användardefinierad plats. Om den tekniska supportteamet för Azure-stacken måste loggarna för att felsöka ett problem, de kan bli ombedd att köra det här verktyget.

> [!CAUTION]
> Loggfilerna kan innehålla personligt identifierbar information (PII). Ta hänsyn innan du publicerar offentligt alla loggfiler.
 
Följande är några exempel loggen typer som samlas in:
*   **Azure Stack-distributionsloggar**
*   **Windows-händelseloggar**
*   **Panther loggar**
*   **Kluster-loggar**
*   **Diagnostikloggar för lagring**
*   **ETW-loggar**

Dessa filer samlas in och sparas på en resurs av insamlaren för spårning. Den **Get-AzureStackLog** PowerShell-cmdlet kan användas för att samla in dem vid behov.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Att köra Get-AzureStackLog på ett system med Azure Stack Development Kit (ASDK)
1. Logga in som **AzureStack\CloudAdmin** på värden.
2. Öppna ett PowerShell-fönster som administratör.
3. Kör den **Get-AzureStackLog** PowerShell-cmdlet.

**Exempel:**

  Samla in alla loggar för alla roller:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Samla in loggar från virtuella datorer och BareMetal roller:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Samla in loggar från virtuella datorer och BareMetal roller med Datumfiltrering för loggfiler för de senaste 8 timmarna:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Samla in loggar från virtuella datorer och BareMetal roller med Datumfiltrering för loggfiler för tidsperioden mellan 8 timmar sedan och 2 timmar sedan:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1804-and-later"></a>Integrerat system version 1804 och senare för att köra Get-AzureStackLog på Azure-stacken

Om du vill köra verktyget log samlingen på ett integrerat system som du behöver ha åtkomst till Privilegierade slutpunkt (program). Här är ett exempelskript som du kan köra detta program kan samla in loggar på ett integrerat system:

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

- Parametrarna **OutputSharePath** och **OutputShareCredential** används för att ladda upp loggar till en extern delad mapp.
- Som visas i föregående exempel är den **FromDate** och **ToDate** parametrar kan användas för att samla in loggar för en viss tidsperiod. Det kan finnas i praktiska för scenarier som insamling av loggar när ett uppdateringspaket på ett integrerat system.


### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1803-and-earlier"></a>Integrerat system version 1803 och tidigare för att köra Get-AzureStackLog på Azure-stacken

Om du vill köra verktyget log samlingen på ett integrerat system som du behöver ha åtkomst till Privilegierade slutpunkt (program). Här är ett exempelskript som du kan köra detta program kan samla in loggar på ett integrerat system:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- När du samlar in loggar från detta program, ange den **OutputPath** parameter ska vara en plats på datorn maskinvara livscykel värden (HLH). Kontrollera också att platsen är krypterad.
- Parametrarna **OutputSharePath** och **OutputShareCredential** är valfria och används när du överför loggar till en extern delad mapp. Använd de här parametrarna *dessutom* till **OutputPath**. Om **OutputPath** anges verktyget log samlingen använder program VM systemenhet för lagring. Detta kan orsaka skriptet misslyckas eftersom diskutrymme som är begränsad.
- Som visas i föregående exempel är den **FromDate** och **ToDate** parametrar kan användas för att samla in loggar för en viss tidsperiod. Det kan finnas i praktiska för scenarier som insamling av loggar när ett uppdateringspaket på ett integrerat system.


### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Parametern överväganden för både ASDK och integrerat system

- Om den **FromDate** och **ToDate** parametrar har angetts, loggarna har samlats in under de senaste fyra timmarna som standard.
- Du kan använda den **TimeOutInMinutes** parametern ange tidsgränsen för Logginsamling. Den är inställd på 150 (2,5 timmar) som standard.

- För närvarande kan du använda den **FilterByRole** parameter till filter Logginsamling av följande roller:

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | NC                         |
   | ACSBlob                | DiskRP                           | Nätverk                    |
   | ACSFabric              | Domän                           | NonPrivilegedAppGateway    |
   | ACSFrontEnd            | FN                              | NRP                        |
   | ACSMetrics             | ExternalDNS                      | OEM                        |
   | ACSMigrationService    | Fabric                           | PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | FRP                              | SLB                        |   
   | ACSTableServer         | Galleri                          | SlbVips                    |
   | ACSWac                 | Gateway                          | SQL                        |   
   | ADFS                   | HealthMonitoring                 | SRP                        |
   | ASAppGateway           | HRP                              | Storage                    |   
   | NCAzureBridge          | IBC                              | StorageAccounts            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | IDN: er                             | Klient                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | Infrastruktur                   | URP                        |
   | CA                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | Molnet                  | KeyVaultControlPlane             | virtuella datorer            |
   | Kluster                | KeyVaultDataPlane                | VAR                        |
   | Compute                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | CPI                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | CERTIFIKATREGISTRERINGSPLATS                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>Samla in loggar med ett grafiskt användargränssnitt
I stället för att tillhandahålla de obligatoriska parametrarna för cmdleten Get-AzureStackLog att hämta Azure Stack loggar kan också användas tillgängliga öppen källkod Azure Stack verktyg som finns i huvudsakliga Azure Stack verktyg GitHub verktyg databasen på http://aka.ms/AzureStackTools.

Den **ERCS_AzureStackLogs.ps1** PowerShell-skriptet lagras i databasen för GitHub-verktyg och uppdateras regelbundet. Att säkerställa att du har den senaste versionen, bör du hämta den direkt från http://aka.ms/ERCS. Starta från en administrativ PowerShell-session skriptet ansluter till Privilegierade slutpunkten och kör Get-AzureStackLog med angivna parametrar. Om inga parametrar har angetts standardvärden skriptet för att fråga efter parametrar via ett grafiskt användargränssnitt.

Om du vill veta mer om ERCS_AzureStackLogs.ps1 PowerShell-skriptet kan du titta på [en kort video](https://www.youtube.com/watch?v=Utt7pLsXEBc) eller visa skriptets [Readme-filen](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) finns i Azure-stacken verktyg GitHub-lagringsplatsen. 

### <a name="additional-considerations"></a>Annat som är bra att tänka på

* Kommandot tar tid att köra baserat på vilka roller som samlar in loggarna. Bidragande faktorer också innefatta varaktighet som angetts för Logginsamling och antal noder i Azure Stack-miljö.
* Som loggar samling körs, kontrollera mappen skapas i den **OutputSharePath** parameter har angetts i kommandot.
* Varje roll har loggar i enskilda zip-filer. Beroende på storleken på de insamlade loggarna kan en roll ha loggar dela med flera zip-filer. Använd ett verktyg som kan packa i grupp (till exempel 7zip) för en roll, om du vill ha alla loggfiler som uppackade i till en enda mapp. Välj de komprimerade filerna för rollen och välj **extrahera här**. Detta upp alla loggfiler för rollen i en enda kopplade mapp.
* En fil som heter **Get-AzureStackLog_Output.log** skapas också i den mapp som innehåller de komprimerade loggfilerna. Den här filen är en logg över kommandoutdata som kan användas vid felsökning av problem vid Logginsamling av.
* Loggar kan behövas från mer än en komponent för att undersöka ett specifikt fel.
    -   System och händelseloggarna för alla virtuella datorer som infrastrukturen har samlats in i den *VirtualMachines* roll.
    -   System och händelseloggarna för alla värdar har samlats in i den *BareMetal* roll.
    -   Händelseloggarna för failover-kluster och Hyper-V samlas i den *lagring* roll.
    -   ACS-loggarna samlas i den *lagring* och *ACS* roller.

> [!NOTE]
> Storlek och ålder begränsningar är framtvingade i loggarna samlas in som det är viktigt att säkerställa effektiv användning av ditt lagringsutrymme för att säkerställa att den inte hämta översvämmat med loggar. När diagnostisera problemet behöver du dock ibland loggar som inte finns längre på grund av dessa begränsningar. Därför är det **rekommenderas starkt** du omfördela loggarna till en extern lagringsutrymme (ett lagringskonto i Azure, en ytterligare lokal lagringsenhet etc.) var 8 och 12: e timme och låt dem vara det för 1-3 månader, beroende på din krav. Kontrollera också att den här lagringsplatsen är krypterad.

## <a name="next-steps"></a>Nästa steg
[Felsökning av Microsoft Azure-stacken](azure-stack-troubleshooting.md)

