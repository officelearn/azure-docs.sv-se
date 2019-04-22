---
title: Koppla Data-lösning i Azure Monitor | Microsoft Docs
description: Wire-data är konsoliderade nätverks- och data från datorer med Log Analytics-agenter. Nätverksdata kombineras med dina loggdata, vilket hjälper dig att korrelera data.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: d295a5a7eae2bdc7983e7271aa11bce1840b92dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882080"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Lösning för Wire Data 2.0 (förhandsversion) i Azure Monitor

![Wire Data-symbol](media/wire-data/wire-data2-symbol.png)

Wire-data är konsoliderade nätverks- och data som samlats in från Windows-anslutna och ansluten Linux-datorer med Log Analytics-agenten, inklusive de som övervakas av Operations Manager i din miljö. Nätverksdata kombineras med dina övriga loggdata, vilket hjälper dig att korrelera data.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Förutom att Log Analytics-agenten använder Wire Data-lösningen Microsoft Beroendeagenter som du installerar på datorer i din IT-infrastruktur. Beroendeagenterna övervakar nätverksdata som skickas till och från dina datorer på nätverksnivåerna 2–3 i [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model), inklusive de olika protokoll och portar som används. Data skickas sedan till Azure Monitor använder agenter.  

>[!NOTE]
>Om du redan har distribuerat Service Map eller överväger Service Map eller [Azure Monitor för virtuella datorer](../../azure-monitor/insights/vminsights-overview.md), det finns en ny anslutning mått datauppsättning de samla in och lagra i Azure Monitor som tillhandahåller Wire-Data jämförbar information.

Som standard loggar data i Azure Monitor för processor, minne, disk och nätverk prestandadata från räknare som är inbyggda i Windows och Linux, samt andra prestandaräknare som du kan ange. Nätverks- och annan datainsamling är klar i realtid för varje agent, inklusive de undernät och protokoll på programnivå som används av datorn.  Wire Data granskar nätverksdata på programnivå, inte ned på TCP-transportnivå.  Lösningen granskar inte enskilda ACK:er och SYN-förfrågningar.  När handskakningen har slutförts anses det finnas en live-anslutning, vilken markeras med Ansluten. Anslutningen finns så länge båda sidorna är överens om att socketen är öppen och data kan överföras fram och tillbaka.  När endera sidan stängs anslutningen och markeras det som frånkopplad.  Därför räknar den bara bandbredden för paket som har slutförts, den rapporterar inte om återsända eller misslyckade paket.

Om du har använt [sFlow](http://www.sflow.org/) eller annan programvara med [Ciscos NetFlow-protokoll](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), känner du igen statistik och data som wire-datan visar.

Några av typerna i de inbyggda loggsökningsfrågorna är:

- Agenter som tillhandahåller kommunikationsdata (wire data).
- IP-adresser för agenterna som tillhandahåller kommunikationsdata
- Utgående kommunikation från IP-adresser
- Antal byte som skickats med programprotokoll
- Antal byte som skickats av en tillämpningstjänst
- Mottagna byte med olika protokoll
- Totalt antal byte som skickats och tagits emot av IP-versionen
- Genomsnittlig svarstid för anslutningar som har en tillförlitlig mätning
- Datorprocesser som har initierat eller mottagit nätverkstrafik
- Mängden nätverkstrafik för en process

När du söker med Wire Data kan du filtrera och gruppera data för att se information om toppagenter och topprotokoll. Du kan också se när vissa datorer (IP-adresser/MAC-adresser) har kommunicerat med varandra, under hur lång tid och hur mycket data som skickades. I princip ser du metadata om nätverkstrafiken som är sökbaserad.

Men eftersom du ser metadata är det inte säkert att det går att använda vid avancerad felsökning. Wire-data i Azure Monitor är inte en fullständig insamling av nätverksdata.  Den är inte avsedd för djupgående felsökning på paketnivå. Fördelen med att använda agenten, jämfört med andra samlingsmetoder, är att du inte behöver installera installationer, konfigurera om dina nätverksväxlar eller utföra komplicerade konfigurationer. Wire-data är helt enkelt agentbaserad – du installerar agenten på en dator och den övervakar sin egen nätverkstrafik. En annan fördel är när du vill övervaka arbetsbelastningar som körs i molnleverantörer, värdleverantörer eller Microsoft Azure, där användaren inte äger infrastrukturnivån.

## <a name="connected-sources"></a>Anslutna källor

Wire Data hämtar sina data från Microsofts beroendeagent. Beroendeagenten beror på Log Analytics-agenten för dess anslutningar till Azure Monitor. Det innebär att en server måste ha Log Analytics-agenten installeras och konfigureras med beroendeagenten. I följande tabell beskrivs de anslutna källor som stöds av Wire Data-lösningen.

| **Ansluten källa** | **Stöds** | **Beskrivning** |
| --- | --- | --- |
| Windows-agenter | Ja | Wire Data analyserar och samlar in data från Windows-agentdatorer. <br><br> Förutom den [Log Analytics-agenten för Windows](../../azure-monitor/platform/agent-windows.md), Windows-agenter kräver Microsofts Beroendeagent. Se [Operativsystem som stöds](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) för en fullständig lista med operativsystemversioner. |
| Linux-agenter | Ja | Wire Data analyserar och samlar in data från Linux-agentdatorer.<br><br> Förutom den [Log Analytics-agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), Linux-agenter kräver Microsofts Beroendeagent. Se [Operativsystem som stöds](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) för en fullständig lista med operativsystemversioner. |
| System Center Operations Manager-hanteringsgrupp | Ja | Wire Data analyserar och samlar in data från Windows- och Linux-agenter i en ansluten [System Center Operations Manager-hanteringsgrupp](../../azure-monitor/platform/om-agents.md). <br><br> Det krävs en direktanslutning från System Center Operations Manager agent-dator till Azure Monitor. |
| Azure Storage-konto | Nej | Wire Data samlar in data från agentdatorer, så det finns inte några data att samla in från Azure Storage. |

På Windows används Microsoft Monitoring Agent (MMA) av både System Center Operations Manager och Azure Monitor att samla in och skicka data. Beroende på kontext kallas agenten för System Center Operations Manager-agenten, Log Analytics-agenten, MMA eller Direct Agent. System Center Operations Manager och Azure Monitor har något annorlunda versioner av MMA. Dessa versioner kan var och en rapport till System Center Operations Manager, till Azure Monitor eller till båda.

På Linux, Log Analytics-agenten för Linux samlar in och skickar data till Azure Monitor. Du kan använda Wire-Data på servrar med agenter som är direkt anslutna till Azure Monitor eller på servrar som ansluter till Azure Monitor via System Center Operations Manager-hanteringsgrupper.

Beroendeagenten överför inte några data och kräver inte att brandväggar och portar ändras. Data i Wire-Data skickas alltid av Log Analytics-agenten till Azure Monitor, antingen direkt eller via Log Analytics-gateway.

![agentdiagram](./media/wire-data/agents.png)

Om du är en System Center Operations Manager-användare med en hanteringsgrupp som är anslutna till Azure Monitor:

- Ingen ytterligare konfiguration krävs när System Center Operations Manager-agenter kan ansluta till internet för att ansluta till Azure Monitor.
- Du måste konfigurera Log Analytics-gatewayen ska fungera med System Center Operations Manager när System Center Operations Manager-agenter inte kan komma åt Azure Monitor via internet.

Om din Windows- eller Linux-datorer inte kan ansluta direkt till tjänsten, måste du konfigurera Log Analytics-agenten för att ansluta till Azure Monitor med Log Analytics-gateway. Du kan ladda ned Log Analytics-gateway från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Förutsättningar

- Kräver lösningen [Insikter och analys](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Om du använder en tidigare version av Wire Data-lösningen måste du först bort den. Alla data som hämtats via den ursprungliga Wire Data-lösningen är dock fortfarande tillgängliga i Wire Data 2.0 och loggsökningen.
- Du måste ha administratörsbehörighet för att kunna installera eller avinstallera beroendeagenten.
- Beroendeagenten måste installeras på en dator med ett 64-bitars operativsystem.

### <a name="operating-systems"></a>Operativsystem

I följande avsnitt visas vilka operativsystem som stöds för beroendeagenten. Wire Data stöder inte 32-bitars arkitekturer för något operativsystem.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows-skrivbordet

- Windows 10-1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds
Följande avsnitt listar operativsystem som stöds för beroendeagenten i Linux.  

- Endast standardversioner och SMP Linux-kernelversioner stöds.
- Avvikande kernelversioner, som exempelvis PAE och Xen, stöds inte för någon Linux-distribution. Till exempel stöds ett system med release-sträng med ”2.6.16.21-0.8-xen” inte.
- Anpassade kernelversioner inklusive omkompileringar av standardkernelversioner, stöds inte.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operativsystemversion | Kernel-version |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Operativsystemversion | Kernel-version |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | Kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Operativsystemversion | Kernel-version
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Operativsystemversion | Kernel-version
|:--|:--|
| 12 SP2 | 4.4. * |
| 12 SP3 | 4.4. * |

### <a name="dependency-agent-downloads"></a>Hämtar beroendeagent

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfiguration

Utför följande steg när du konfigurerar Wire Data-lösningen för dina arbetsytor.

1. Aktivera Activity Log Analytics-lösningen från den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) eller genom att använda processen som beskrivs i [Lägg till övervakning lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Installera beroendeagenten på varje dator som du vill hämta data från. Beroendeagenten kan övervaka anslutningar till omedelbara grannar, så du behöver kanske inte ha en agent på varje dator.

> [!NOTE]
> Du kan inte lägga till den tidigare Wire Data-versionen på nya arbetsytor. Om du har den ursprungliga Wire Data-lösningen aktiverad, kan du fortsätta att använda den. Men för att använda Wire Data 2.0 måste du först ta bort den ursprungliga versionen.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Installera beroendeagenten i Windows

Administratörsbehörighet krävs för att installera eller avinstallera agenten.

Beroendeagenten installeras på Windows-datorer med InstallDependencyAgent-Windows.exe. Om du kör den här körbara filen utan alternativ, startas en guide som du kan följa om du vill installera interaktivt.

Använd följande steg för att installera beroendeagenten på varje dator som kör Windows:

1. Installera Log Analytics-agenten följa stegen i [samla in data från Windows-datorer i din miljö](../../azure-monitor/platform/agent-windows.md).
2. Hämta Windows beroendeagent med hjälp av länken i föregående avsnitt och kör den sedan med hjälp av följande kommando: `InstallDependencyAgent-Windows.exe`
3. Följ guiden för att installera agenten.
4. Om beroendeagenten inte startar kan du se om det finns detaljerad felinformation i loggarna. Loggkatalogen för Windows-agenter är %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Windows-kommandorad

Använd alternativen i följande tabell för att installera från en kommandorad. Om du vill se en lista med installationsflaggor kör du installationsprogrammet med hjälp av /? flagga enligt nedan.

InstallDependencyAgent-Windows.exe /?

| **Flagga** | **Beskrivning** |
| --- | --- |
| <code>/?</code> | Hämta en lista med kommandoradsalternativ. |
| <code>/S</code> | Utför en tyst installation utan någon användarprompter. |

Filer för Windows beroendeagent placeras i C:\Program Files\Microsoft Dependency Agent som standard.

### <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten i Linux

Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.

Beroendeagenten installeras på Linux-datorer med InstallDependencyAgent-Linux64.bin, ett kommandoskript med en självextraherande binär fil. Du kan köra filen med hjälp av _sh_ eller lägga till körningsbehörighet till själva filen.

Använd följande steg för att installera beroendeagenten på varje dator som kör Linux:

1. Installera Log Analytics-agenten följa stegen i [samla in data från Linux-datorer i din miljö](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Ladda ned Linux-beroendeagenten med hjälp av länken i föregående avsnitt och installera den som rot med hjälp av följande kommando: sh InstallDependencyAgent-Linux64.bin
3. Om beroendeagenten inte startar kan du se om det finns detaljerad felinformation i loggarna. På Linux-agenter är loggkatalogen: /var/opt/microsoft/dependency-agent/log.

Om du vill se en lista med installationsflaggorna kör du installationsprogrammet med `-help`-flaggan enligt nedan.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flagga** | **Beskrivning** |
| --- | --- |
| <code>-help</code> | Hämta en lista med kommandoradsalternativ. |
| <code>-s</code> | Utför en tyst installation utan någon användarprompter. |
| <code>--check</code> | Kontrollera behörigheter och operativsystemet, men installera inte agenten. |

Filer för beroendeagenten placeras i följande kataloger:

| **Filer** | **Plats** |
| --- | --- |
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Exempel på installationsskript

Det är enklare att distribuera beroendeagenten på flera servrar samtidigt med ett skript. Du kan använda följande skriptexempel till att ladda ned och installera beroendeagenten på antingen Windows eller Linux.

#### <a name="powershell-script-for-windows"></a>PowerShell-skript för Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Kommandoskript för Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Önskad tillståndskonfiguration

Om du vill distribuera beroendeagenten via Desired State Configuration, kan du använda modulen xPSDesiredStateConfiguration och en bit kod som liknar följande:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Avinstallera beroendeagenten

Använd följande avsnitt som hjälp när du tar bort beroendeagenten.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Avinstallera beroendeagenten i Windows

En administratör kan avinstallera beroendeagenten för Windows via Kontrollpanelen.

Administratören kan också köra %Programfiles%\Microsoft Dependency Agent\Uninstall.exe för att avinstallera beroendeagenten.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Avinstallera beroendeagenten i Linux

Om du vill avinstallera beroendeagenten helt från Linux, måste du ta bort själva agenten och anslutningsprogrammet som installerades automatiskt med agenten. Du kan avinstallera båda med hjälp av följande kommando:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Hanteringspaket

När Wire Data är aktiverat på en Log Analytics-arbetsyta, skickas ett hanteringspaket på 300 KB till alla Windows-servrar på arbetsytan. Om du använder System Center Operations Manager-agenter i en [ansluten hanteringsgrupp](../platform/om-agents.md), distribueras Dependency Monitor-hanteringspaketet från System Center Operations Manager. Om agenterna är direkt anslutna, ger Azure Monitor management pack.

Hanteringspaketet heter Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Det skrivs till: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Datakällan som hanteringspaketet använder är: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Använda lösningen

Använd följande information för att installera och konfigurera lösningen.

- Wire Data-lösningen hämtar data från datorer som kör operativsystemen Windows Server 2012 R2, Windows 8.1 och senare.
- Microsoft .NET Framework 4.0 eller senare krävs på de datorer som du vill hämta wire-data från.
- Lägg till lösning för Wire-Data till Log Analytics-arbetsytan med processen som beskrivs i [Lägg till övervakning lösningar från lösningsgalleriet](solutions.md). Det krävs ingen ytterligare konfiguration.
- Om du vill se kommunikationsdata för en viss lösning måste du redan ha lagt till lösningen på din arbetsyta.

När du har installerade agenter och du installerar lösningen, visas Wire Data 2.0-panelen på arbetsytan.

![Wire Data-panel](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Använda Wire Data 2.0-lösningen

På sidan **Översikt** för Log Analytics-arbetsytan i Azure Portal, klickar du på panelen **Wire Data 2.0** för att öppna instrumentpanelen för Wire Data. Instrumentpanelen innehåller bladen i följande tabell. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned på bladet eller genom att klicka på bladrubriken.

| **Blad** | **Beskrivning** |
| --- | --- |
| Agenter som samlar in nätverkstrafik | Visar antalet agenter som samlar in nätverkstrafik och visar en lista med de främsta 10 datorerna som hämtar trafik. Klicka på siffran om du vill köra en loggsökning efter <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Klicka på en dator i listan om du vill köra en loggsökning som returnerar det totala antalet byte som har hämtats. |
| Lokala undernät | Visar antalet lokala undernät som agenterna har identifierat.  Klicka på siffran om du vill köra en loggsökning efter <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> som visar en lista över alla undernät med antalet byte som skickats i var och en. Klicka på ett undernät i listan om du vill köra en loggsökning som returnerar det totala antalet byte som har skickats i undernätet. |
| Protokoll på programnivå | Visar antalet protokoll på programnivå som används och som identifierats av agenterna. Klicka på siffran om du vill köra en loggsökning efter <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Klicka på ett protokoll om du vill köra en loggsökning som returnerar det totala antalet byte som har skickats med protokollet. |

![Wire Data-instrumentpanel](./media/wire-data/wire-data-dash.png)

Du kan använda bladet **Agenter som samlar in nätverkstrafik** för att avgöra hur mycket nätverksbandbredd som används av datorerna. Det här bladet kan hjälpa dig att enkelt hitta den _trafikintensivaste_ datorn i din miljö. Sådana datorer kan vara överbelastade, bete sig onormalt eller använda fler nätverksresurser än normalt.

![exempel på loggsökning](./media/wire-data/log-search-example01.png)

På liknande sätt kan du använda bladet **Lokala undernät** till att avgöra hur mycket nätverkstrafik som passerar genom dina undernät. Användarna definierar ofta undernät runt kritiska områden för sina program. Det här bladet visar dessa områden.

![exempel på loggsökning](./media/wire-data/log-search-example02.png)

Bladet **Protokoll på programnivå** är användbart eftersom det alltid är bra att veta vilka protokoll som används. Du kanske till exempel misstänker att SSH inte används i din nätverksmiljö. Genom att läsa den information som finns i bladet kan du snabbt få din misstanke bekräftad eller motbevisad.

![exempel på loggsökning](./media/wire-data/log-search-example03.png)

Det är också bra att veta om protokolltrafiken ökar eller minskar med tiden. Om till exempel mängden data som skickas av ett program ökar, kan det vara något som du bör vara medveten om eller vara uppmärksam på.

## <a name="input-data"></a>Indata

Wire-datan samlar in metadata om nätverkstrafik med hjälp av de agenter som du har aktiverat. Varje agent skickar data var 15:e sekund.

## <a name="output-data"></a>Utdata

En post av typen _WireData_ skapas för varje typ av indata. WireData-poster har egenskaper enligt följande tabell:

| Egenskap | Beskrivning |
|---|---|
| Computer | Namn på den dator där data samlades in |
| TimeGenerated | Tid för posten |
| LocalIP | IP-adressen för den lokala datorn |
| SessionState | Ansluten eller frånkopplad |
| ReceivedBytes | Mängden byte som tagits emot |
| ProtocolName | Namnet på det nätverksprotokoll som används |
| IPVersion | IP-version |
| Direction | Inkommande eller utgående |
| MaliciousIP | IP-adressen för en känd skadlig källa |
| Severity | Allvarlighetsgrad för misstänkt skadlig programvara |
| RemoteIPCountry | Land för fjärr-IP-adress |
| ManagementGroupName | Namn på Operations Manager-hanteringsgrupp |
| SourceSystem | Källa där data samlades in |
| SessionStartTime | Starttid för sessionen |
| SessionEndTime | Sluttid för sessionen |
| LocalSubnet | Undernät där data samlades in |
| LocalPortNumber | Lokalt portnummer |
| RemoteIP | Fjärr-IP-adress som används av fjärrdatorn |
| RemotePortNumber | Portnummer som används av fjärr-IP-adressen |
| SessionID | Ett unikt värde som identifierar kommunikationssessionen mellan två IP-adresser |
| SentBytes | Antal skickade byte |
| TotalBytes | Totalt antal byte som skickats under sessionen |
| ApplicationProtocol | Namnet på det nätverksprotokoll som används   |
| ProcessID | Windows process-ID |
| ProcessName | Sökväg och filnamn för processen |
| RemoteIPLongitude | IP-longitudvärde |
| RemoteIPLatitude | IP-latitudvärde |


## <a name="next-steps"></a>Nästa steg

- [Sök i loggar](../../azure-monitor/log-query/log-query-overview.md) för att se detaljerade sökposter för wire-data.