---
title: "Konfigurera Tjänstkarta i Operations Management Suite | Microsoft Docs"
description: "Tjänstkarta är en Operations Management Suite-lösning som automatiskt identifierar programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. Den här artikeln innehåller information för att distribuera Tjänstkarta i din miljö och använda den i en mängd olika scenarier."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: 9d310df29156f16f6b5290ff0575ff43d083a26c
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="configure-service-map-in-operations-management-suite"></a>Konfigurera Tjänstkarta i Operations Management Suite
Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Du kan använda den för att visa dina servrar som du betrakta dem--som sammanlänkade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitektur med än installation av en agent krävs ingen konfiguration.

Den här artikeln innehåller information om konfigurera Tjänstkarta och onboarding-agenter. Information om hur du använder Tjänstkarta finns [använder Tjänstkarta lösningen i Operations Management Suite](operations-management-suite-service-map.md).

## <a name="dependency-agent-downloads"></a>Hämtar Beroendeagent
| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.3.0 | 1F5261CAAF6C8DF4E03E4927DA918B3461B40B41C6BF5845803878D7CF975693 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.3.0 | 7BADFF2411899114F0214766160E4E871A2462DC137141CEEDEFAF528F428ADD  |


## <a name="connected-sources"></a>Anslutna källor
Tjänstkarta hämtar data från Microsoft Beroendeagent. Agenten beroende beror på OMS-Agent för sina anslutningar till Operations Management Suite. Detta innebär att en server måste ha OMS-Agent installeras och konfigureras först och sedan beroende agenten kan installeras. I följande tabell beskrivs anslutna källor som har stöd för Tjänstkarta-lösning.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Tjänstkarta analyserar och samlar in data från datorer med Windows-agenten. <br><br>Förutom den [OMS-Agent](../log-analytics/log-analytics-windows-agent.md), Windows-agenter kräver Microsoft Beroendeagent. Finns det [operativsystem](#supported-operating-systems) för en fullständig lista över versioner av operativsystemet. |
| Linux-agenter | Ja | Tjänstkarta analyserar och samlar in data från datorer för Linux-agenten. <br><br>Förutom den [OMS-Agent](../log-analytics/log-analytics-linux-agents.md), Linux-agenter kräver Microsoft Beroendeagent. Finns det [operativsystem](#supported-operating-systems) för en fullständig lista över versioner av operativsystemet. |
| System Center Operations Manager-hanteringsgrupp | Ja | Tjänstkarta analyserar och samlar in data från Windows- och Linux-agenter i en ansluten [System Center Operations Manager-hanteringsgruppen](../log-analytics/log-analytics-om-agents.md). <br><br>Det krävs en direkt anslutning från datorn som System Center Operations Manager-agenten till Operations Management Suite. Data skickas från hanteringsgruppen till Operations Management Suite-databasen.|
| Azure Storage-konto | Nej | Tjänstkarta samlar in data från agentdatorer, så det finns inga data från det att samla in från Azure Storage. |

Tjänstkarta stöder endast 64-bitars plattformar.

I Windows, Microsoft Monitoring Agent (MMA) används av både System Center Operations Manager och Operations Management Suite att samla in och skicka övervakningsdata. (Den här agenten kallas System Center Operations Manager-Agent, OMS-Agent, Log Analytics Agent, MMA eller direkt Agent, beroende på kontext.) System Center Operations Manager och Operations Management Suite ger olika out av the box-versioner av MMA. Dessa versioner kan varje rapport till System Center Operations Manager, Operations Management Suite, eller båda.  

På Linux, OMS-Agent för Linux samlar in och skickar övervakningsdata till Operations Management Suite. Du kan använda Tjänstkarta på servrar med OMS direkt agenter eller servrar som är kopplade till Operations Management Suite via System Center Operations Manager-hanteringsgrupper.  

I den här artikeln ska vi refererar till alla agenter--om Linux- eller Windows, om ansluten till en System Center Operations Manager-hanteringsgrupp eller direkt till Operations Management Suite--som ”OMS-agenten”. Vi använder distributionsnamnet på agenten endast om det behövs för kontext.

Tjänstkarta agenten överföra inte alla data och kräver inte ändringar brandväggar och portar. Data i Tjänstkartan överförs alltid av OMS-Agent till Operations Management Suite, antingen direkt eller via OMS-Gateway.

![Tjänstkarta agenter](media/oms-service-map/agents.png)

Om du är en System Center Operations Manager-kund med en hanteringsgrupp som är anslutna till Operations Management Suite:

- Om System Center Operations Manager-agenter kan komma åt Internet för att ansluta till Operations Management Suite, krävs ingen ytterligare konfiguration.  
- Om System Center Operations Manager-agenter inte kan komma åt Operations Management Suite via Internet, måste du konfigurera OMS-Gateway att fungera med System Center Operations Manager.
  
Om du använder direkt OMS-agenten måste du konfigurera OMS själva agenten att ansluta till Operations Management Suite eller till din OMS-Gateway. OMS-Gateway kan hämtas från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

### <a name="management-packs"></a>Hanteringspaket
När Tjänstkarta är aktiverat i en Operations Management Suite-arbetsyta, skickas ett 300 KB hanteringspaket till alla Windows-servrar i arbetsytan. Om du använder System Center Operations Manager-agenter i en [ansluten hanteringsgrupp](../log-analytics/log-analytics-om-agents.md), Tjänstkarta-hanteringspaket distribueras från System Center Operations Manager. Om agenter är anslutna direkt levererar Operations Management Suite management pack.

Management pack heter Microsoft.IntelligencePacks.ApplicationDependencyMonitor. De skrivs till %Programfiles%\Microsoft övervakning Agent\Agent\Health Service State\Management Packs\. Datakällan som används av management pack är % Program files%\Microsoft övervakning Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Installation
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Installera agenten beroende på Microsoft Windows
Administratörsbehörighet krävs för att installera eller avinstallera agenten.

Beroende-agenten är installerad på Windows-datorer via InstallDependencyAgent Windows.exe. Om du kör den här körbara filen utan alternativ startas en guide som du kan följa om du vill installera interaktivt.  

Använd följande steg för att installera agenten beroende på varje Windows-dator:

1.  Installera OMS-agenten med hjälp av anvisningarna i [ansluta Windows-datorer i Azure logganalys-tjänsten](../log-analytics/log-analytics-windows-agent.md).
2.  Ladda ned Windows agent och kör den med hjälp av följande kommando: <br>`InstallDependencyAgent-Windows.exe`
3.  Följ guiden för att installera agenten.
4.  Om agenten beroende inte startar kontrollerar du loggfilerna för Detaljerad felinformation. På Windows-agenter är loggkatalogen %Programfiles%\Microsoft beroende Agent\logs. 

#### <a name="windows-command-line"></a>Windows-kommandoraden
Använd alternativen i följande tabell för att installera från en kommandorad. Om du vill se en lista över flaggor för installation, kör installationsprogrammet med hjälp av den /? Flagga på följande sätt.

    InstallDependencyAgent-Windows.exe /?

| Flaggan | Beskrivning |
|:--|:--|
| /? | Hämta en lista över kommandoradsalternativ. |
| /S | Utföra en tyst installation utan uppmaningar för användaren. |

Filer för Windows Beroendeagent placeras i C:\Program Files\Microsoft beroende agenten som standard.

### <a name="install-the-dependency-agent-on-linux"></a>Installera agenten beroende på Linux
Rotåtkomst krävs för att installera eller konfigurera agenten.

Beroende-agenten är installerad på Linux-datorer via InstallDependencyAgent-Linux64.bin, ett kommandoskript med en självextraherande binär. Du kan köra filen med hjälp av del eller lägga till körbehörighet till själva filen.
 
Använd följande steg för att installera agenten beroende på varje Linux-dator:

1.  Installera OMS-agenten med hjälp av anvisningarna i [samla in och hantera data från Linux-datorer](https://technet.microsoft.com/library/mt622052.aspx).
2.  Installera beroende på Linux-agenten som rot med hjälp av följande kommando:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Om agenten beroende inte startar kontrollerar du loggfilerna för Detaljerad felinformation. Loggkatalogen är /var/opt/microsoft/dependency-agent/log på Linux-agenter.

För att se en lista över flaggor för installation, kör installationen av programmet med - hjälp flaggan på följande sätt.

    InstallDependencyAgent-Linux64.bin -help

| Flaggan | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista över kommandoradsalternativ. |
| -s | Utföra en tyst installation utan uppmaningar för användaren. |
| – Kontrollera | Kontrollera behörigheter och operativsystemet, men inte installera agenten. |

Filer för Beroendeagent placeras i följande kataloger:

| Filer | Plats |
|:--|:--|
| -Filer | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Config-filer | /etc/opt/microsoft/dependency-agent/config |
| Tjänsten körbara filer | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära filer | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exempel på skript för installation
För att enkelt distribuera agenten beroende på flera servrar samtidigt, hjälper det för att använda ett skript. Du kan använda i följande skriptexempel att ladda ned och installera agenten beroende på Windows- eller Linux.

### <a name="powershell-script-for-windows"></a>Windows PowerShell-skript
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Shell-skript för Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Azure VM-tillägget
Du kan enkelt distribuera agenten beroende på din virtuella Azure-datorer med en [Azure VM-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/classic/agents-and-extensions-classic).  Du kan distribuera beroende agenten till din virtuella dator via ett PowerShell-skript eller direkt i Azure Resource Manager-mall för den virtuella datorn med Azure VM-tillägg.  Det är ett tillägg för både Windows (DependencyAgentWindows) och Linux (DependencyAgentLinux).  Om du distribuerar via Azure VM-tillägget kan dina agenter uppdateras automatiskt till de senaste versionerna.

Du kan använda följande exempel för att distribuera Azure VM-tillägget via PowerShell:
```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
#

$version = "9.1"
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

Ett enklare sätt att kontrollera den beroende agenten finns på var och en av dina virtuella datorer är att inkludera agenten i Azure Resource Manager-mall.  Observera att beroende agenten fortfarande beroende OMS-Agent därför [OMS-agenten VM-tillägget](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-vm-extension) måste distribueras först.  Följande fragment av JSON kan läggas till i *resurser* i mallen.
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
    "typeHandlerVersion": "9.1",
    "autoUpgradeMinorVersion": true
}

```


## <a name="desired-state-configuration"></a>Önskad tillståndskonfiguration
Om du vill distribuera Beroendeagent via Desired State Configuration, kan du använda modulen xPSDesiredStateConfiguration och lite kod som liknar följande:
```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
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

## <a name="uninstallation"></a>Avinstallation
### <a name="uninstall-the-dependency-agent-on-windows"></a>Avinstallera agenten beroende på Windows
En administratör kan avinstallera beroende agenten för Windows via Kontrollpanelen.

En administratör kan också köra %Programfiles%\Microsoft beroende Agent\Uninstall.exe avinstallera agenten beroende.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Avinstallera agenten beroende på Linux
Du kan avinstallera beroende agenten från Linux med följande kommando.
<br>RHEL, CentOs eller Oracle:
```
sudo rpm -e dependency-agent
```
Ubuntu:
```
sudo dpkg --purge dependency-agent
```
## <a name="troubleshooting"></a>Felsökning
Om du har några problem med installeras eller köras Tjänstkarta kan i det här avsnittet hjälpa dig. Kontakta Microsoft-supporten om du fortfarande inte kan lösa problemet.

### <a name="dependency-agent-installation-problems"></a>Beroende Agent installationsproblem
#### <a name="installer-asks-for-a-reboot"></a>Installationsprogrammet frågar en omstart
Agenten beroende *vanligtvis* inte kräver en omstart vid installation eller avinstallation. I vissa sällsynta fall kräver dock Windows Server startas om för att fortsätta med installationen. Detta händer när en omstart krävs för ett beroende, vanligtvis Microsoft Visual C++ Redistributable, på grund av en låst fil.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Meddelandet ”Det går inte att installera agenten beroende: Det gick inte att installera Visual Studio Runtime-bibliotek (kod = [code_number])” visas

Microsoft Beroendeagent bygger på bibliotek för Microsoft Visual Studio-körning. Du får ett meddelande om ett problem har uppstått under installationen av biblioteken. 

Installationsprogram för runtime library skapa loggarna i mappen %LOCALAPPDATA%\temp. Filen är dd_vcredist_arch_yyyymmddhhmmss.log, där *arkitektur* är ”x86” eller ”amd64” och *ååååmmddhhmmss* är datum och tid (24-timmarsformat) när loggen skapades. Loggen innehåller information om problem som blockerar installationen.

Det kan vara praktiskt att installera den [bibliotek för senaste körning](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) själv först.

I följande tabell visas nummer och förslag på lösningar.

| Kod | Beskrivning | Lösning |
|:--|:--|:--|
| 0x17 | Biblioteket installationsprogrammet kräver en Windows-uppdatering inte har installerats. | Titta i den senaste biblioteket installer-loggen.<br><br>Om en referens till ”Windows8.1-KB2999226-x64.msu” följt av en rad ”fel 0x80240017: Det gick inte att köra MSU-paket”, du har inte några krav för att installera KB2999226. Följ anvisningarna i avsnittet krav i [Universal C körning i Windows](https://support.microsoft.com/kb/2999226). Du kan behöva köra Windows Update och starta om flera gånger för att installera nödvändiga komponenter.<br><br>Kör installationsprogrammet för Microsoft beroende agenten igen. |

### <a name="post-installation-issues"></a>Efter installationen problem
#### <a name="server-doesnt-appear-in-service-map"></a>Servern visas inte i Tjänstkartan
Om Beroendeagent-installationen har slutförts, men du inte ser servern i Tjänstkartan lösningen:
* Beroende agenten installeras? Du kan kontrollera detta genom att kontrollera om tjänsten är installerad och körs.<br><br>
**Windows**: Leta efter tjänsten med namnet ”Microsoft Dependency Agent”.<br>
**Linux**: Leta för körning ”microsoft-beroende-agent”.

* Är du på den [kostnadsfria prisnivån för Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? Fria abonnemang tillåter upp till fem unika servrar i Tjänstkartan. Övriga servrar visas inte i Tjänstkartan, även om de tidigare fem längre skickar data.

* Är din server skicka logg- och prestandadata till Operations Management Suite? Gå till loggen Sök och kör följande fråga för datorn: 

        * Computer="<your computer name here>" | measure count() by Type
        
  Fick du ett antal händelser i resultaten? Är data senaste? I så fall, fungerar OMS-Agent och kommunicera med Operations Management Suite-tjänsten. Om inte, kontrollera OMS-agenten på servern: [OMS-Agent för Windows felsökning](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) eller [OMS-Agent för Linux felsökning](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Servern visas i Tjänstkartan men har inga processer
Om du ser servern i Tjänstkartan, men den har ingen process eller anslutning till data, som anger att beroende-agenten är installerad och körs, men kernel-drivrutinen lästes inte in. 

Kontrollera C:\Program Files\Microsoft beroende Agent\logs\wrapper.log fil (Windows) eller /var/opt/microsoft/dependency-agent/log/service.log-fil (Linux). De sista raderna i filen bör ange varför kernel lästes inte in. Till exempel kernel kanske inte finns stöd för Linux om du har uppdaterat din kernel.

## <a name="data-collection"></a>Datainsamling
Du kan förvänta dig varje agent som överför ungefär 25 MB per dag, beroende på hur komplex din system beroenden är. Varje agent skickar Tjänstkarta beroendedata var 15: e sekund.  

Beroende agenten förbrukar vanligtvis 0.1 systemminne och 0,1 procent för processor.

## <a name="supported-azure-regions"></a>Azure-regioner som stöds
Tjänstkarta är tillgängliga i de följande Azure-regionerna:
- Östra USA
- Västra Europa
- Västra centrala USA
- Sydostasien


## <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande avsnitt listar operativsystem som stöds för beroende agenten. Tjänstkarta stöder inte 32-bitars arkitekturer för alla operativsystem.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows-skrivbordet
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux och Oracle Linux (med RHEL Kernel)
- Endast standard och SMP Linux kernel-versioner stöds.
- Avvikande kernel Frigör som PAE och Xen, inte stöds för Linux-distribution. Till exempel stöds ett system med versionen sträng med ”2.6.16.21-0.8-xen” inte.
- Anpassade kärnor, inklusive omkompileringar standard kärnor stöds inte.
- CentOSPlus kernel stöds inte.
- Oracle Unbreakable Enterprise Kernel (UEK) ingår i ett senare avsnitt i den här artikeln.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| OS-version | Kernel-version |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| OS-version | Kernel-version |
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

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| OS-version | Kernel-version |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- Anpassade kärnor, inklusive omkompileringar standard kärnor stöds inte.

| OS-version | Kernel-version |
|:--|:--|
| 16.04 | 4.4.0-103<br>4.11.0-1016 |
| 14.04 | 3.13.0-137<br>4.4.0-103 |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux med Unbreakable Enterprise Kernel
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| OS-version | Kernel-version
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| OS-version | Kernel-version
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| OS-version | Kernel-version
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>diagnostik och användningsdata
Microsoft samlar automatiskt in användnings- och prestandadata via din användning av tjänsten Tjänstkartan. Microsoft använder informationen för att tillhandahålla och förbättra kvalitet, säkerhet och integritet Tjänstkarta-tjänsten. Data innehåller information om konfiguration av programvaran som operativsystem och version. Även IP-adress, DNS-namn och namn på arbetsstation för att kunna tillhandahålla korrekta och effektiva funktioner för felsökning. Vi samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i [sekretesspolicy för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [använder Tjänstkarta](operations-management-suite-service-map.md) när den har distribuerats och konfigurerats.
