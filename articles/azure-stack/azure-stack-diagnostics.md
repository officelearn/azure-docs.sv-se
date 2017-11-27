---
title: Diagnostik i Azure Stack
description: "Hur du samlar in loggfiler för diagnostik i Azure-stacken"
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/22/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 8afde912ca48297ae60eb7d05aa624a1d72c1637
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack diagnosverktyg

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*
 
Azure stacken är ett stort antal komponenter som arbetar tillsammans och interagera med varandra. Alla dessa komponenter genererar sina egna unika loggar. Detta kan göra diagnostisera problem utmaning, särskilt för fel som kommer från flera interagerar Azure Stack-komponenter. 

Vår diagnosverktyg säkerställer mekanism för logg-samling är enkelt och effektivt. Följande diagram visar logga hur samling verktyg i Azure-stacken arbete:

![Diagnostikverktyg för Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Spåra insamlaren
 
Spåra insamlaren är aktiverad som standard. Den kontinuerligt körs i bakgrunden och samlar in alla ETW Event Tracing for Windows ()-loggar från Komponenttjänster på Azure-stacken och lagrar dem på en gemensam lokal resurs. 

Följande är viktiga saker att veta om Trace insamlaren:
 
* Spåra insamlaren kör kontinuerligt med standard storleksgränser. Standard maximala storleken som tillåts för varje fil (200 MB) är **inte** klara storlek. En storlek kontroll inträffar regelbundet (för närvarande varannan minut) och om den aktuella filen är > = 200 MB sparas så skapas en ny fil. Det finns också en 8 GB (konfigureras) gräns på den totala filstorleken genereras per händelsesessionen. När den här gränsen har nåtts, raderas de äldsta filerna när nya skapas.
* Det finns en åldersgräns på 5 dagar på loggarna. Den här gränsen kan också konfigureras. 
* Varje komponent definierar konfigurationsegenskaper spårning via en JSON-fil. JSON-filerna lagras i **C:\TraceCollector\Configuration**. Dessa filer kan redigeras om du vill ändra ålder och storlek gränserna för loggar som samlats in om det behövs. Ändringar i dessa filer kräver en omstart av den *Microsoft Azure Stack Trace Collector* tjänsten för att ändringarna ska börja gälla.

Följande exempel är en trace konfigurationens JSON-fil för FabricRingServices åtgärder från XRP VM: 

```json
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**. Denna parameter styr ålder filerna ska sparas. Äldre loggfiler tas bort.
* **MaxSizeInMB**. Denna parameter styr tröskelvärdet för databasstorleken för en enskild fil. Om storleken uppnås, skapas en ny etl-fil.
* **TotalSizeInMB**. Den här parametern styr den totala storleken på etl-filer som skapas från en händelsesessionen. Om den totala filstorleken är större än det här parametervärdet tas äldre filer bort.
  
## <a name="log-collection-tool"></a>Loggen samling verktyget
 
PowerShell-kommandot **Get-AzureStackLog** kan användas för att samla in loggar från alla komponenter i en Azure-Stack-miljö. Det sparar dem i zip-filer i en användardefinierad plats. Om vår tekniska support måste loggarna för att felsöka ett problem, de kan bli ombedd att köra det här verktyget.

> [!CAUTION]
> Loggfilerna kan innehålla personligt identifierbar information (PII). Ta hänsyn innan du publicerar offentligt alla loggfiler.
 
Följande är några exempel loggen typer som samlas in:
*   **Azure Stack-distributionsloggar**
*   **Windows-händelseloggar**
*   **Panther loggar**
*   **Kluster-loggar**
*   **Diagnostikloggar för lagring**
*   **ETW-loggar**

Filerna som samlas in av spårning insamlaren och lagras i en resurs från var **Get-AzureStackLog** hämtar dem.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Att köra Get-AzureStackLog på ett system med Azure Stack Development Kit (ASDK)
1. Logga in som **AzureStack\CloudAdmin** på värden.
2. Öppna ett PowerShell-fönster som administratör.
3. Kör den **Get-AzureStackLog** PowerShell-cmdlet.

   **Exempel**

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

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>Integrerat system för att köra Get-AzureStackLog på en Azure-stacken

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
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | ADFS                    | ASAppGateway           | BareMetal          |
   | BRP                     | CERTIFIKATUTFÄRDARE                     | CPI                |
   | CERTIFIKATREGISTRERINGSPLATS                     | DeploymentMachine      | DHCP               |
   | Domän                  | FN                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | Gateway                 | HealthMonitoring       | HRP                |   
   | IBC                     | InfraServiceController |KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | SRP                     | Lagring                | StorageController  |
   | URP                     | UsageBridge            | virtuella datorer    |  
   | VAR                     | WASPUBLIC              | WDS                |


### <a name="additional-considerations"></a>Annat som är bra att tänka på

* Kommandot tar tid att köra baserat på vilka roller som samlar in loggarna. Bidragande faktorer också innefatta varaktighet som angetts för Logginsamling och antal noder i Azure Stack-miljö.
* När Logginsamling är klar kontrollerar du den nya mappen skapas i den **OutputPath** parameter har angetts i kommandot.
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
