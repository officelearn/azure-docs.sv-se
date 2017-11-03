---
title: "Tråd lösning i Log Analytics | Microsoft Docs"
description: "Wire-data är konsoliderade nätverks- och data från datorer med OMS-agenter, inklusive Operations Manager och Windows-anslutna agenter. Nätverksdata kombineras med dina loggdata som hjälper dig att korrelera data."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: magoedte;banders
ms.openlocfilehash: 3bb4c82268fe7805227c213000dc803307876fe7
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Överföring Data 2.0 (förhandsgranskning) lösning i logganalys

![Överföring Data symbol](./media/log-analytics-wire-data/wire-data2-symbol.png)

Wire-data är konsoliderade nätverks- och data som samlas in från Windows-kopplad och Linux datorer med OMS-agent, inklusive de som övervakas av Operations Manager i din miljö. Nätverksdata kombineras med dina andra loggdata som hjälper dig att korrelera data.

Förutom OMS-agenten använder Wire-Data lösningen Microsoft beroende agenter som installeras på datorer i din IT-infrastruktur. Beroende agenter övervakar nätverksdata som skickas till och från dina datorer för nätverket nivåer 2-3 i den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), inklusive de olika protokoll och portar som används. Informationen skickas sedan till logganalys använder agenter.  

> [!NOTE]
> Du kan inte lägga till den tidigare versionen av lösningen Wire-Data till nya arbetsytor. Om du har den ursprungliga lösningen för Data under överföring aktiverat, kan du fortsätta att använda den. Men för att använda överföring Data 2.0 måste du först ta bort den ursprungliga versionen.

Som standard loggas logganalys data för CPU, minne, disk och nätverk prestandadata från räknare som är inbyggda i Windows och Linux, samt andra prestandaräknare som du kan ange. Nätverk och andra datainsamlingen är klar realtid för varje agent, inklusive undernät och programnivå protokoll som används av datorn.  Wire-Data verkar på nätverksdata på programnivå, inte ned på TCP-Transportskiktet.  Lösningen titta inte på enskilda bekräftelser och SYN-förfrågningar.  När handskakningen har slutförts är den anses vara en live-anslutning och markeras som ansluten. Att kopplingen förblir live så länge båda sidorna accepterar socket är öppen och överföra data fram och tillbaka.  När båda sidorna avslutar anslutningen kan markeras som frånkopplad.  Därför bara räknar bandbredden för paket som har slutförts, den inte rapporterar på skickar eller misslyckad paket.

Om du har använt [sFlow](http://www.sflow.org/) eller annan programvara med [Ciscos NetFlow protokollet](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), statistik och data som du ser från wire-data ska vara bekant med.

Några av typerna av inbyggda loggen sökfrågor är:

- Agenter som tillhandahåller wire-data
- IP-adressen för agenter som tillhandahåller wire-data
- Utgående kommunikation via IP-adresser
- Antal byte skickade efter programprotokoll
- Antalet byte som skickats av en programtjänsten
- Byte mottagna efter olika protokoll
- Totalt antal byte som skickas och tas emot av IP-version
- Genomsnittlig svarstid för anslutningar som har ett tillförlitligt sätt
- Datorn bearbetar som initierat eller mottagit nätverkstrafik
- Mängden nätverkstrafik för en process

När du söker med wire-data kan du filtrera och gruppera data att visa information om de översta agenter och övre protokoll. Eller så kan du visa när vissa datorer (IP-adresser/MAC-adresser) kommunicerat med varandra, hur lång tid och hur mycket data skickades – i princip du visa metadata om nätverkstrafik som är Sökbaserade.

Eftersom du visar metadata, är det dock inte nödvändigtvis användbart för avancerad felsökning. Wire-data i logganalys är inte en fullständig insamlingen av nätverksdata.  Det är inte avsedd för djupt paketnivå felsökning. Fördelen med att använda agenten jämfört med andra metoder för samlingen, är att du inte behöver installera apparater, konfigurera om dina nätverksväxlar eller utför komplicerade konfigurationer. Wire-data är helt enkelt agent-baserad – du installerar agenten på en dator och den ska övervaka sin egen nätverkstrafik. En annan fördel är när du vill övervaka arbetsbelastningar som körs i molntjänstleverantörer eller värdleverantör eller Microsoft Azure, där användaren inte äger fabric-lagret.

## <a name="connected-sources"></a>Anslutna källor

Wire-Data hämtar data från Microsoft Beroendeagent. Agenten beroende beror på OMS-Agent för sina anslutningar till logganalys. Detta innebär att en server måste ha OMS-Agent installeras och konfigureras först och sedan installerar du agenten beroende. I följande tabell beskrivs de anslutna källor som har stöd för lösningen Wire-Data.

| **Ansluten datakälla** | **Stöds** | **Beskrivning** |
| --- | --- | --- |
| Windows-agenter | Ja | Wire-Data analyserar och samlar in data från datorer med Windows-agenten. <br><br> Förutom den [OMS-Agent](log-analytics-windows-agents.md), Windows-agenter kräver Microsoft Beroendeagent. Finns det [operativsystem](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) för en fullständig lista över versioner av operativsystemet. |
| Linux-agenter | Ja | Wire-Data analyserar och samlar in data från datorer för Linux-agenten.<br><br> Förutom den [OMS-Agent](log-analytics-quick-collect-linux-computer.md), Linux-agenter kräver Microsoft Beroendeagent. Finns det [operativsystem](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) för en fullständig lista över versioner av operativsystemet. |
| System Center Operations Manager-hanteringsgrupp | Ja | Wire-Data analyserar och samlar in data från Windows- och Linux-agenter i en ansluten [System Center Operations Manager-hanteringsgruppen](log-analytics-om-agents.md). <br><br> Det krävs en direkt anslutning från System Center Operations Manager-agentdatorn till logganalys. Till logganalys vidarebefordras data från hanteringsgruppen. |
| Azure Storage-konto | Nej | Wire-Data samlar in data från agentdatorer, så det finns inga data från det att samla in från Azure Storage. |

I Windows används Microsoft Monitoring Agent (MMA) av både System Center Operations Manager och logganalys att samla in och skicka data. Beroende på kontext kallas agenten System Center Operations Manager-Agent, OMS-Agent, Log Analytics Agent, MMA eller direkt Agent. System Center Operations Manager och Log Analytics tillhandahåller något olika versioner av MMA. Dessa versioner kan varje rapport till System Center Operations Manager till logganalys eller båda.

På Linux, OMS-Agent för Linux samlar in och skickar data till logganalys. Du kan använda Wire-Data på servrar med OMS direkt agenter eller servrar som är kopplade till logganalys via System Center Operations Manager-hanteringsgrupper.

Referenser till alla agenter i den här artikeln om Linux- eller Windows, om ansluten till en System Center Operations Manager-hanteringsgrupp eller direkt till Log Analytics kallas den _OMS-agent_. Vi använder distributionsnamnet på agenten endast om det behövs för kontext.

Beroende agenten överföra inte alla data och kräver inte ändringar brandväggar och portar. Data i Wire-Data är alltid överförs av OMS-agent till logganalys, antingen direkt eller via OMS-Gateway.

![agenten diagram](./media/log-analytics-wire-data/agents.png)

Om du är en System Center Operations Manager-användare med en hanteringsgrupp som är ansluten till logganalys:

- Ingen ytterligare konfiguration krävs när System Center Operations Manager-agenter kan komma åt Internet för att ansluta till logganalys.
- Du måste konfigurera OMS-Gateway att fungera med System Center Operations Manager när System Center Operations Manager-agenter inte kan komma åt logganalys via Internet.

Om du använder direkt agenten måste du konfigurera OMS själva agenten att ansluta till logganalys eller till din OMS-Gateway. Du kan hämta OMS-Gateway från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Krav

- Kräver den [insikt och Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) lösningen-erbjudandet.
- Om du använder en tidigare version av lösningen Wire-Data måste du först bort den. Alla data som hämtats via den ursprungliga lösningen Wire-Data är dock fortfarande tillgängliga i överföring Data 2.0 och Sök i loggfilen.
- Administratörsbehörighet krävs för att installera eller avinstallera agenten beroende.
- Beroende agenten måste installeras på en dator med ett 64-bitars operativsystem.

### <a name="operating-systems"></a>Operativsystem

Följande avsnitt listar operativsystem som stöds för beroende agenten. Wire-Data stöder inte 32-bitars arkitekturer för alla operativsystem.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows-skrivbordet

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux och Oracle Linux (med RHEL Kernel)

- Endast standard och SMP Linux kernel-versioner stöds.
- Avvikande kernel Frigör som PAE och Xen, inte stöds för Linux-distribution. Till exempel ett system med versionen sträng med _2.6.16.21-0.8-xen_ stöds inte.
- Anpassade kärnor, inklusive omkompileringar standard kärnor stöds inte.
- CentOSPlus kernel stöds inte.
- Oracle Unbreakable Enterprise Kernel (UEK) ingår i ett senare avsnitt i den här artikeln.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **OS-version** | **Kernel-version** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **OS-version** | **Kernel-version** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **OS-version** | **Kernel-version** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux med Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **OS-version** | **Kernel-version** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **OS-version** | **Kernel-version** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **OS-version** | **Kernel-version** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **OS-version** | **Kernel-version** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Hämtar Beroendeagent

| **Fil** | **OS** | **Version** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Konfiguration

Utför följande steg för att konfigurera lösningen Wire-Data för dina arbetsytor.

1. Aktivera aktivitet logganalys-lösning från den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).
2. Installera agenten beroende på varje dator där du vill hämta data. Beroende agenten kan övervaka anslutningar till omedelbar grannar, så du inte kan ha en agent på varje dator.

### <a name="install-the-dependency-agent-on-windows"></a>Installera agenten beroende på Windows

Administratörsbehörighet krävs för att installera eller avinstallera agenten.

Beroende-agenten är installerad på Windows-datorer via InstallDependencyAgent Windows.exe. Om du kör den här körbara filen utan alternativ startas en guide som du kan följa om du vill installera interaktivt.

Använd följande steg för att installera agenten beroende på varje dator som kör Windows:

1. Installera OMS-agenten följa stegen i [samla in data från Windows-datorer i din miljö](log-analytics-windows-agents.md).
2. Hämta Windows beroende agenten med hjälp av länken i föregående avsnitt och kör sedan med hjälp av följande kommando:`InstallDependencyAgent-Windows.exe`
3. Följ guiden för att installera agenten.
4. Om agenten beroende inte startar kontrollerar du loggfilerna för Detaljerad felinformation. Loggkatalogen är %Programfiles%\Microsoft beroende Agent\logs för Windows-agenter.

#### <a name="windows-command-line"></a>Windows-kommandoraden

Använd alternativen i följande tabell för att installera från en kommandorad. Om du vill se en lista över flaggor för installation, kör installationsprogrammet med hjälp av den /? Flagga på följande sätt.

InstallDependencyAgent Windows.exe /?

| **Flaggan** | **Beskrivning** |
| --- | --- |
| <code>/?</code> | Hämta en lista över kommandoradsalternativ. |
| <code>/S</code> | Utföra en tyst installation utan uppmaningar för användaren. |

Filer för Windows Beroendeagent placeras i C:\Program Files\Microsoft beroende agenten som standard.

### <a name="install-the-dependency-agent-on-linux"></a>Installera agenten beroende på Linux

Rotåtkomst krävs för att installera eller konfigurera agenten.

Beroende-agenten är installerad på Linux-datorer via InstallDependencyAgent-Linux64.bin, ett kommandoskript med en självextraherande binär. Du kan köra filen med hjälp av _del_ eller Lägg till körbehörighet till själva filen.

Använd följande steg för att installera agenten beroende på varje Linux-dator:

1. Installera OMS-agenten följa stegen i [samla in data från Linux-datorer i din miljö](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Hämta Linux beroende agenten med hjälp av länken i föregående avsnitt och installera den som rot med hjälp av följande kommando: del InstallDependencyAgent Linux64.bin
3. Om agenten beroende inte startar kontrollerar du loggfilerna för Detaljerad felinformation. På Linux-agenter loggkatalogen är: /var/opt/microsoft/dependency-agent/log.

Om du vill se en lista över flaggor för installation, kör du installationsprogrammet med det `-help` flaggan på följande sätt.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flaggan** | **Beskrivning** |
| --- | --- |
| <code>-help</code> | Hämta en lista över kommandoradsalternativ. |
| <code>-s</code> | Utföra en tyst installation utan uppmaningar för användaren. |
| <code>--check</code> | Kontrollera behörigheter och operativsystemet, men inte installera agenten. |

Filer för Beroendeagent placeras i följande kataloger:

| **Filer** | **Plats** |
| --- | --- |
| -Filer | /OPT/Microsoft/Dependency-Agent |
| Loggfiler | /var/OPT/Microsoft/Dependency-Agent/log |
| Config-filer | /etc/OPT/Microsoft/Dependency-Agent/config |
| Tjänsten körbara filer | /OPT/Microsoft/Dependency-Agent/bin/Microsoft-Dependency-Agent<br><br>/OPT/Microsoft/Dependency-Agent/bin/Microsoft-Dependency-Agent-Manager |
| Binära filer | /var/OPT/Microsoft/Dependency-Agent/Storage |

### <a name="installation-script-examples"></a>Exempel på skript för installation

För att enkelt distribuera agenten beroende på flera servrar samtidigt, hjälper det för att använda ett skript. Du kan använda i följande skriptexempel att ladda ned och installera agenten beroende på Windows- eller Linux.

#### <a name="powershell-script-for-windows"></a>Windows PowerShell-skript

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Shell-skript för Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Önskad tillståndskonfiguration

Om du vill distribuera Beroendeagent via Desired State Configuration, kan du använda modulen xPSDesiredStateConfiguration och lite kod som liknar följande:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Avinstallera agenten beroende

Använd följande avsnitt som hjälper dig att ta bort beroendet.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Avinstallera agenten beroende på Windows

En administratör kan avinstallera beroende agenten för Windows via Kontrollpanelen.

En administratör kan också köra %Programfiles%\Microsoft beroende Agent\Uninstall.exe avinstallera agenten beroende.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Avinstallera agenten beroende på Linux

Om du vill avinstallera helt beroende agenten från Linux, måste du ta bort själva agenten och connector, som installeras automatiskt med agenten. Du kan avinstallera både med hjälp av följande kommando:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Hanteringspaket

När Wire-Data är aktiverat i logganalys-arbetsytan, skickas ett 300 KB hanteringspaket till alla Windows-servrar på arbetsytan. Om du använder System Center Operations Manager-agenter i en [ansluten hanteringsgrupp](log-analytics-om-agents.md), Dependency Monitor-hanteringspaket distribueras från System Center Operations Manager. Om agenter är anslutna direkt levererar Log Analytics management pack.

Management pack heter Microsoft.IntelligencePacks.ApplicationDependencyMonitor. De skrivs till: %Programfiles%\Microsoft övervakning Agent\Agent\Health State\Management servicepack. Datakällan som används av management pack är: % Program files%\Microsoft övervakning Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Använda lösningen

**Installera och konfigurera lösningen**

Använd följande information för att installera och konfigurera lösningen.

- Wire-Data lösningen hämtar data från datorer som kör Windows Server 2012 R2, Windows 8.1 och senare operativsystem.
- Microsoft .NET Framework 4.0 eller senare krävs på datorer där du vill hämta wire-data från.
- Lägg till lösningen Wire-Data i logganalys-arbetsytan med processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). Det krävs ingen ytterligare konfiguration.
- Om du vill visa wire-data för en viss lösning måste du har redan lagts till i din arbetsyta-lösning.

När du har agenter som installerats och du installerar lösningen, visas överföring Data 2.0-panelen på arbetsytan.

> [!NOTE]
> För närvarande måste du använda OMS-portalen för att visa wire-data. Du kan inte använda Azure-portalen för att visa wire-data.

![Panelen Wire-Data](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Med lösningen överföring Data 2.0

I OMS-portalen klickar du på den **överföring Data 2.0** öppna instrumentpanelen Wire-Data. Instrumentpanelen innehåller bladen i följande tabell. Varje bladet visar upp till 10 objekt som matchar det bladet villkoren för angivet omfång och tidsintervall. Du kan köra en sökning i loggen som returnerar alla poster genom att klicka på **se alla** längst ned på bladet eller genom att klicka på rubriken bladet.

| **Bladet** | **Beskrivning** |
| --- | --- |
| Agenter som samlar in nätverkstrafik | Visar antalet agenter som hämtar nätverkstrafik och visar de översta 10 datorer hämtar trafik. Klicka på siffran om du vill köra en logg sökning efter <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Klicka på en dator i listan om du vill köra en logg sökning returnera det totala antalet byte som har hämtats. |
| Lokala undernät | Visar antalet lokala undernät som agenter har identifierats.  Klicka på siffran om du vill köra en logg sökning efter <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> som visar en lista över alla undernät med antalet byte som skickats över var och en. Klicka på ett undernät i listan för att köra en logg sökning returnera det totala antalet byte som skickats över undernätet. |
| Protokoll på programnivå | Visar antalet protokoll på programnivå används, som identifierats av agenter. Klicka på siffran om du vill köra en logg sökning efter <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Klicka på ett protokoll för att köra en logg sökning returnera det totala antalet byte som skickats med protokollet. |

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Överföring Data instrumentpanelen](./media/log-analytics-wire-data/wire-data-dash.png)

Du kan använda den **agenter som samlar in nätverkstrafik** bladet för att avgöra hur mycket nätverksbandbredd som används av datorer. Det här bladet kan hjälpa dig att enkelt hitta den _chattiest_ dator i din miljö. Sådana datorer kan vara överbelastad fungerar onormalt eller använder mer nätverksresurser än normalt.

![loggen Sök-exempel](./media/log-analytics-wire-data/log-search-example01.png)

På liknande sätt kan du använda den **lokala undernät** bladet för att avgöra hur mycket trafik som flyttas via dina undernät. Användarna definiera ofta undernät runt viktiga områden för sina program. Det här bladet ger en överblick över dessa områden.

![loggen Sök-exempel](./media/log-analytics-wire-data/log-search-example02.png)

Den **protokoll på programnivå** bladet är användbar eftersom den är användbara veta vilka protokoll som används. Du kan till exempel räknar SSH kan inte användas i din nätverksmiljö. Visa information som är tillgängliga i bladet kan snabbt bekräfta eller disprove din förväntan.

![loggen Sök-exempel](./media/log-analytics-wire-data/log-search-example03.png)

I det här exemplet kan gå till SSH-information för att se vilka datorer som använder SSH och många andra kommunikationsdetaljer.

![del sökresultat](./media/log-analytics-wire-data/ssh-details.png)

Det är också bra att känna av om protokolltrafik ökar eller minskar med tiden. Till exempel om ökar mängden data som skickas av ett program som kan vara något du bör vara medveten om, eller att du kan hitta anmärkningsvärda.

## <a name="input-data"></a>Indata

Samlar in metadata om nätverkstrafik med hjälp av de agenter som du har aktiverat Wire-data. Varje agent skickar data om var 15: e sekund.

## <a name="output-data"></a>utdata

En post med en typ av _WireData_ skapas för varje typ av indata. WireData poster har egenskaperna som visas i följande tabell:

| Egenskap | Beskrivning |
|---|---|
| Dator | Datornamnet där data har samlats in |
| TimeGenerated | Tiden då posten |
| LocalIP | IP-adressen för den lokala datorn |
| SessionState | Ansluten eller frånkopplad |
| receivedBytes | Mängden byte som tagits emot |
| ProtocolName | Namnet på nätverksprotokoll som används |
| IPVersion | IP-version |
| Riktning | Inkommande eller utgående |
| MaliciousIP | IP-adressen för en känd skadlig källa |
| Allvarsgrad | Allvarlighetsgrad för misstänkt skadlig programvara |
| RemoteIPCountry | Land för fjärr-IP-adress |
| ManagementGroupName | Namnet på hanteringsgruppen för Operations Manager |
| SourceSystem | Källan där data har samlats in |
| SessionStartTime | Starttid för session |
| SessionEndTime | Sluttid för session |
| LocalSubnet | Undernät där data har samlats in |
| LocalPortNumber | Lokala portnumret |
| RemoteIP | Fjärranslutna IP-adress som används av fjärrdatorn |
| RemotePortNumber | Portnummer för fjärr-IP-adress |
| Sessions-ID | Ett unikt värde som identifierar kommunikationssessionen mellan två IP-adresser |
| sentBytes | Antal skickade byte |
| TotalBytes | Totalt antal byte som skickats under sessionen |
| ApplicationProtocol | Nätverksprotokoll som används   |
| Process-ID | Windows process-ID |
| Processnamn | Sökvägen och filnamnet för processen |
| RemoteIPLongitude | IP-Longitudvärde |
| RemoteIPLatitude | IP-latitud värde |


## <a name="next-steps"></a>Nästa steg

- [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad överföring sökposter.
