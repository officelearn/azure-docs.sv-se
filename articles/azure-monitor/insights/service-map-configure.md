---
title: Konfigurera Tjänstkarta i Azure | Microsoft Docs
description: Tjänstkarta är en lösning i Azure som automatiskt identifierar programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln innehåller information för att distribuera Service Map i din miljö och använder den på en mängd olika scenarier.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2019
ms.author: bwren
ms.openlocfilehash: 60c43475fc044b0847e5d9bd495c0d53b562114e
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822719"
---
# <a name="configure-service-map-in-azure"></a>Konfigurera Tjänstkarta i Azure
Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Du kan använda den för att visa dina servrar som du tänker på dem, sammankopplat system som levererar viktiga tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer utan konfiguration som krävs, än installation av en agent.

Den här artikeln innehåller information om att konfigurera agenter för Tjänstkarta och registrering. Information om hur du använder Service Map finns i [använder lösningen Tjänstkarta i Azure]( service-map.md).

## <a name="supported-azure-regions"></a>Azure-regioner som stöds
Tjänstkarta är nu tillgänglig i följande Azure-regioner:
- Östra USA
- Västra Europa
- Västra centrala USA
- Sydostasien

## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds
Följande avsnitt en lista över operativsystem som stöds för beroendeagenten på Windows. 

>[!NOTE]
>Tjänstkarta stöder endast 64-bitars-plattformar.
>

### <a name="windows-server"></a>Windows Server
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows-skrivbordet
- Windows 10-1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds
I följande avsnitt en lista över operativsystem som stöds för beroendeagenten på Red Hat Enterprise Linux, CentOS Linux och Oracle Linux (med RHEL Kernel).  

- Endast standardversioner och SMP Linux-kernelversioner stöds.
- Avvikande kernelversioner, som exempelvis PAE och Xen, stöds inte för någon Linux-distribution. Till exempel stöds ett system med release-sträng med ”2.6.16.21-0.8-xen” inte.
- Anpassade kernelversioner inklusive omkompileringar av standardkernelversioner, stöds inte.
- CentOSPlus-kernel stöds inte.
- Oracle Unbreakable Enterprise Kernel (UEK) beskrivs i ett senare avsnitt i den här artikeln.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operativsystemversion | Kernel-version |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Operativsystemversion | Kernel-version |
|:--|:--|
| Ubuntu 18.04 | Kernel 4.15. * |
| Ubuntu 16.04.3 | Kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 med Unbreakable Enterprise Kernel
| Operativsystemversion | Kernel-version
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 med Unbreakable Enterprise Kernel

| Operativsystemversion | Kernel-version
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

## <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Operativsystemversion | Kernel-version
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

## <a name="dependency-agent-downloads"></a>Hämtar beroendeagent

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="connected-sources"></a>Anslutna källor
Tjänstkarta hämtar data från Microsoft Dependency agenten. Beroendeagenten är beroende av Log Analytics-agenten för dess anslutningar till Log Analytics. Det innebär att en server måste ha Log Analytics-agenten installeras och konfigureras med beroendeagenten.  I följande tabell beskrivs de anslutna källor som har stöd för lösningen Tjänstkarta.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Service Map analyserar och samlar in data från Windows-datorer. <br><br>Förutom den [Log Analytics-agenten för Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-agenter kräver Microsoft Dependency agenten. Se vilka operativsystem som en fullständig lista över operativsystemversioner. |
| Linux-agenter | Ja | Service Map analyserar och samlar in data från Linux-datorer. <br><br>Förutom den [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md), kräver Microsoft Dependency agenten för Linux-agenter. Se vilka operativsystem som en fullständig lista över operativsystemversioner. |
| System Center Operations Manager-hanteringsgrupp | Ja | Service Map analyserar och samlar in data från Windows och Linux-agenter i en ansluten [System Center Operations Manager-hanteringsgrupp](../../azure-monitor/platform/om-agents.md). <br><br>En direktanslutning från System Center Operations Manager-agentdatorn till Log Analytics krävs. |
| Azure Storage-konto | Nej | Tjänstkarta samlar in data från agentdatorer, så det finns inga data från den för att samla in från Azure Storage. |

På Windows, Microsoft Monitoring Agent (MMA) används av både System Center Operations Manager och Log Analytics att samla in och skicka övervakningsdata. (Den här agenten kallas System Center Operations Manager-agenten, Log Analytics-agenten, MMA eller Direktagent, beroende på kontext.) System Center Operations Manager och Log Analytics tillhandahåller olika out-nyckelfärdig versioner av MMA. Båda dessa versioner kan rapportera till System Center Operations Manager, till Log Analytics eller till båda.  

På Linux, Log Analytics-agenten för Linux samlar och skickar övervakning av data till Log Analytics. Du kan använda Service Map på servrar med Log Analytics-agenter är anslutna direkt till tjänsten eller som rapporterar till en Operations Manager-hanteringsgrupp som är integrerat med Log Analytics.  

I den här artikeln ska vi ska referera till alla agenter om Linux eller Windows ansluten till en System Center Operations Manager-hanteringsgrupp eller direkt till Log Analytics, som den *Log Analytics-agenten*. 

Tjänstkarta-agenten överföra inte några data själva och det kräver inte ändringar i brandväggar eller portar. Data i Tjänstkarta överförs alltid genom Log Analytics-agenten till Log Analytics-tjänsten antingen direkt eller via Log Analytics-gateway.

![Tjänstkarta-agenter](media/service-map-configure/agents.png)

Om du är en System Center Operations Manager-kund med en hanteringsgrupp som är ansluten till Log Analytics:

- Om System Center Operations Manager-agenter kan komma åt Internet för att ansluta till Log Analytics, krävs ingen ytterligare konfiguration.  
- Om System Center Operations Manager-agenter inte kan komma åt Log Analytics via Internet, måste du konfigurera Log Analytics-gatewayen ska fungera med System Center Operations Manager.
  
Om din Windows- eller Linux-datorer inte kan ansluta direkt till tjänsten, måste du konfigurera Log Analytics-agenten för att ansluta till Log Analytics-arbetsytan med hjälp av gatewayen. Mer information om hur du distribuerar och konfigurerar Log Analytics-gateway finns [ansluter datorer utan Internetåtkomst med Log Analytics-gateway](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Hanteringspaket
När Tjänstkarta har aktiverats i Log Analytics-arbetsytan kan vidarebefordras ett hanteringspaket på 300 KB till alla Windows-servrar i den arbetsytan. Om du använder System Center Operations Manager-agenter i en [ansluten hanteringsgrupp](../../azure-monitor/platform/om-agents.md), Tjänstkarta-hanteringspaket distribueras från System Center Operations Manager. 

Hanteringspaketet heter Microsoft.IntelligencePacks.ApplicationDependencyMonitor. De skrivs till %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. Datakällan som hanteringspaketet använder är % Program files%\Microsoft övervakning Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="data-collection"></a>Datainsamling
Du kan förvänta dig varje agent så att sända ungefär 25 MB per dag, beroende på hur komplex din systemberoenden är. Varje agent skickar Service Map beroendedata var 15: e sekund.  

Beroendeagenten förbrukar vanligtvis 0,1 procent av systemminnet och 0,1 procent av systemprocessor.

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata
Microsoft samlar automatiskt in användnings- och data till din användning av tjänsten Tjänstkarta. Microsoft använder dessa data för att tillhandahålla och förbättra kvaliteten, säkerheten och integriteten för tjänsten Tjänstkarta. Data innehåller information om konfiguration, till exempel operativsystem och version. Den innehåller även IP-adress, DNS-namn och namn på arbetsstation för att tillhandahålla korrekta och effektiva funktioner för felsökning. Vi samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i den [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Installation

### <a name="azure-vm-extension"></a>Azure VM-tillägg
Det är ett tillägg för både Windows (DependencyAgentWindows) och Linux (DependencyAgentLinux) och du kan enkelt distribuera beroendeagenten på virtuella Azure-datorer med en [Azure VM-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Du kan distribuera beroendeagenten till Windows och Linux-datorer med hjälp av antingen ett PowerShell-skript eller direkt i den virtuella datorn med en Azure Resource Manager-mall med Azure VM-tillägg.  Om du distribuerar en agent för Azure VM-tillägg måste uppdateras automatiskt dina agenter till den senaste versionen.

Du kan använda följande exempel för att distribuera Azure VM-tillägget med PowerShell:

```PowerShell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Ett enklare sätt att kontrollera beroendeagenten är installerad på dina virtuella datorer är att inkludera agenten i Azure Resource Manager-mallen.  I följande JSON-kodexempel som kan läggas till i *resurser* i mallen.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Installera beroendeagenten på Microsoft Windows
Beroendeagenten kan installeras manuellt på Windows-datorer genom att köra `InstallDependencyAgent-Windows.exe`. Om du kör den här körbara filen utan några alternativ, startar en installationsguide som du kan följa för att installera interaktivt.  

>[!NOTE]
>Administratörsbehörighet krävs för att installera eller avinstallera agenten.

Använd följande steg för att installera beroendeagenten på varje Windows-dator:

1.  Installera agenten för Log Analytics för Windows följa en av metoderna som beskrivs i [översikt över Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md).
2.  Ladda ned Windows-agenten och kör den med hjälp av följande kommando: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Följ guiden för att installera agenten.
4.  Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. På Windows-agenter är loggkatalogen %Programfiles%\Microsoft beroende Agent\logs. 

#### <a name="windows-command-line"></a>Windows-kommandorad
Använd alternativen i följande tabell för att installera från en kommandorad. Om du vill se en lista med installationsflaggor kör du installationsprogrammet med hjälp av /? flagga enligt nedan.

    InstallDependencyAgent-Windows.exe /?

| Flagga | Beskrivning |
|:--|:--|
| /? | Hämta en lista med kommandoradsalternativ. |
| / S | Utför en tyst installation utan någon användarprompter. |

Filer för Windows beroendeagenten placeras i C:\Program Files\Microsoft Beroendeagenten som standard.

### <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten på Linux
Beroende-agenten är installerad på Linux-datorer från `InstallDependencyAgent-Linux64.bin`, ett kommandoskript med en självextraherande binärfilen. Du kan köra filen med hjälp av `sh` eller Lägg till körbehörighet till själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.

Använd följande steg för att installera beroendeagenten på varje Linux-dator:

1.  Installera Log Analytics-agenten följa en av metoderna som beskrivs i [översikt över Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md).
2.  Installera beroendeagenten för Linux som rot genom att köra följande kommando:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. På Linux-agenter är loggkatalogen /var/opt/microsoft/dependency-agent/log.

Se en lista över installationen-flaggor som kör installationen programmera med - hjälp flaggan på följande sätt.

    InstallDependencyAgent-Linux64.bin -help

| Flagga | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| – Kontrollera | Kontrollera behörigheter och operativsystemet, men installera inte agenten. |

Filer för beroendeagenten placeras i följande kataloger:

| Filer | Plats |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exempel på installationsskript
För att enkelt distribuera beroendeagent flera servrar samtidigt, tillhandahålls i följande exempel för skriptet att hämta och installera beroendeagenten i Windows eller Linux.

### <a name="powershell-script-for-windows"></a>PowerShell-skript för Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Kommandoskript för Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```
## <a name="desired-state-configuration"></a>Önskad tillståndskonfiguration
För att distribuera beroendeagenten med Desired State Configuration (DSC), kan du använda modulen xPSDesiredStateConfiguration med följande exempelkod:

```
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>Ta bort beroendeagenten
### <a name="uninstall-agent-on-windows"></a>Avinstallera agent på Windows
En administratör kan avinstallera beroendeagenten för Windows via Kontrollpanelen.

En administratör kan också köra %Programfiles%\Microsoft beroende Agent\Uninstall.exe avinstallera beroendeagenten.

### <a name="uninstall-agent-on-linux"></a>Avinstallera agenten på Linux
Du kan avinstallera beroendeagenten från Linux med följande kommando.

RHEL, CentOs eller Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>Felsökning
Om du får problem med installeras eller köras Tjänstkarta kan i det här avsnittet hjälpa dig. Kontakta Microsoft Support om du fortfarande inte kan lösa problemet.

### <a name="dependency-agent-installation-problems"></a>Problem med beroende agenten installationen
#### <a name="installer-prompts-for-a-reboot"></a>Installationsprogrammet frågar efter en omstart
Beroendeagenten *Allmänt* inte kräver en omstart vid installation eller avinstallation. I vissa sällsynta fall kräver dock Windows Server startas om för att fortsätta med en installation. Detta händer när ett beroende, vanligtvis Microsoft Visual C++ Redistributable, kräver en omstart på grund av en låst fil.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Meddelandet ”Det går inte att installera beroendeagenten: Det gick inte att installera Visual Studio-Runtime-bibliotek (kod = [code_number]) ”visas

Agenten Microsoft Dependency bygger på bibliotek för Microsoft Visual Studio-körning. Du får ett meddelande om ett problem har uppstått under installationen av biblioteken. 

Installationsprogram för runtime-biblioteket skapar loggarna i mappen %LOCALAPPDATA%\temp. Filen är dd_vcredist_arch_yyyymmddhhmmss.log, där *arch* är ”x86” eller ”amd64” och *ååååmmddhhmmss* är datum och tid (24-timmarsformat) när loggen har skapats. Loggen innehåller information om problem som blockerar installation.

Det kan vara praktiskt att installera den [bibliotek för senaste körning](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) själv första.

I följande tabell visas kodnummer och rekommenderade lösningar.

| Kod | Beskrivning | Lösning |
|:--|:--|:--|
| 0x17 | Installationsprogrammet biblioteket kräver en Windows-uppdatering som inte är installerad. | Titta i den senaste biblioteket installer-loggen.<br><br>Om en referens till ”Windows8.1-KB2999226-x64.msu” följt av en rad ”fel 0x80240017: Det gick inte att köra MSU-paketet ”, har du inte kraven för att installera KB2999226. Följ anvisningarna i kravavsnittet i [Universal C-körning i Windows](https://support.microsoft.com/kb/2999226). Du kan behöva köra Windows Update och starta om flera gånger för att kunna installera nödvändiga komponenter.<br><br>Kör installationsprogrammet för Microsoft Dependency agenten igen. |

### <a name="post-installation-issues"></a>Efter installationen problem
#### <a name="server-doesnt-appear-in-service-map"></a>Servern visas inte i Tjänstkarta
Om din beroende agentinstallationen har genomförts, men du inte ser din server i lösningen Tjänstkarta:
* Beroendeagenten installeras? Du kan kontrollera detta genom att markera om du vill se om tjänsten är installerad och körs.<br><br>
**Windows**: Leta efter tjänsten med namnet ”Microsoft Dependency agent”.<br>
**Linux**: Leta efter processen ”microsoft-beroende-agent”.

* Är du på den [kostnadsfria prisnivån för Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Det kostnadsfria abonnemanget kan upp till fem unika Tjänstkarta-servrar. Övriga servrar visas inte i Tjänstkartan, även om de tidigare fem inte längre skickar data.

* Är din server skicka logg- och prestandadata till Log Analytics? Gå till Loggsökning och kör följande fråga för din dator: 

    Användning | där dator == ”datornamn” | Sammanfatta sum(Quantity) any(QuantityUnit) av DataType

Fick du en rad olika händelser i resultaten? Är data de senaste? I så fall är din Log Analytics-agenten fungerar på rätt sätt och kommunicera med Log Analytics. Om inte, kontrollera agent på servern: [Log Analytics-agenten för felsökning av Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) eller [Log Analytics-agenten för Linux-felsökning](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Servern visas i Service Map men innehåller inga processer
Om servern i Tjänstkarta visas, men den har ingen process eller anslutningen data, som anger att beroendeagenten är installerat och körs, men kerneldrivrutinen lästes inte in. 

Kontrollera C:\Program Files\Microsoft beroende Agent\logs\wrapper.log fil (Windows) eller /var/opt/microsoft/dependency-agent/log/service.log fil (Linux). De sista raderna i filen ska indikera varför kernel lästes inte in. Till exempel kanske kernel inte kan användas i Linux om du har uppdaterat din kernel.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [använda Tjänstkartan]( service-map.md) när den har distribuerats och konfigurerats.
