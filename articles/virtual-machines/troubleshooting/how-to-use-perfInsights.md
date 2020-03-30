---
title: Så här använder du PerfInsights i Microsoft Azure| Microsoft-dokument
description: Lär dig hur du använder PerfInsights för att felsöka prestandaproblem för Windows VM.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 783b479dd3e5f429516799d7d3ea82f363cac2ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250132"
---
# <a name="how-to-use-perfinsights"></a>Så här använder du PerfInsights

[PerfInsights](https://aka.ms/perfinsightsdownload) är ett självhjälpsdiagnostikverktyg som samlar in och analyserar diagnostikdata och tillhandahåller en rapport som hjälper till att felsöka prestandaproblem för virtuella datorer i Windows i Azure. PerfInsights kan köras på virtuella datorer som ett fristående verktyg, direkt från portalen med hjälp av [Prestandadiagnostik för virtuella Azure-datorer](performance-diagnostics.md)eller genom att installera [Azure Performance Diagnostics VM Extension](performance-diagnostics-vm-extension.md).

Om du har prestandaproblem med virtuella datorer, innan du kontaktar supporten, kör du det här verktyget.

## <a name="supported-troubleshooting-scenarios"></a>Felsökningsscenarier som stöds

PerfInsights kan samla in och analysera flera typer av information. Följande avsnitt täcker vanliga scenarier.

### <a name="quick-performance-analysis"></a>Snabb prestandaanalys

Det här scenariot samlar in diskkonfigurationen och annan viktig information, inklusive:

-   Händelseloggar

-   Nätverksstatus för alla inkommande och utgående anslutningar

-   Konfigurationsinställningar för nätverk och brandvägg

-   Uppgiftslista för alla program som körs på systemet

-   Konfigurationsinställningar för Microsoft SQL Server-databasen (om den virtuella datorn identifieras som en server som kör SQL Server)

-   Tillförlitlighetsräknare för lagring

-   Viktiga Windows-snabbkorrigeringar

-   Installerade filterdrivrutiner

Detta är en passiv samling av information som inte bör påverka systemet. 

>[!Note]
>Det här scenariot inkluderas automatiskt i vart och ett av följande scenarier:

### <a name="benchmarking"></a>Benchmarking

Det här scenariot kör [diskspd-benchmarktestet](https://github.com/Microsoft/diskspd) (IOPS och MBPS) för alla enheter som är kopplade till den virtuella datorn. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett live-produktionssystem. Om det behövs kör du det här scenariot i ett dedikerat underhållsfönster för att undvika problem. En ökad arbetsbelastning som orsakas av ett spårnings- eller benchmark-test kan påverka datorns prestanda negativt.
>

### <a name="performance-analysis"></a>Resultatanalys

Det här scenariot kör en [prestandaräknarespårning](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) med hjälp av de räknare som anges i filen RuleEngineConfig.json. Om den virtuella datorn identifieras som en server som kör SQL Server körs en prestandaräknarespårning. Det gör du genom att använda de räknare som finns i filen RuleEngineConfig.json. Det här scenariot innehåller även prestandadiagnostikdata.

### <a name="azure-files-analysis"></a>Analys av Azure-filer

Det här scenariot kör en särskild prestandaräknare insamling tillsammans med ett nätverk spårning. Hämtningen innehåller alla SMB-klientresurser (Server Message Block). Följande är några viktiga SMB-klientresursprestandaräknare som ingår i insamlingen:

| **Typ**     | **Räknaren SMB-klientaktier** |
|--------------|-------------------------------|
| IOPS         | Databegäranden per sekund             |
|              | Läsa begäranden per sekund             |
|              | Skriv begäranden per sekund            |
| Svarstid      | Genomsnittlig sek/databegäran         |
|              | Genomsnittligt sek/läs                 |
|              | Genomsnittligt sek/skrivning                |
| I/o-storlek      | Begäran om medelsnuggingar/data       |
|              | Genomsnittligt antal byte/läs               |
|              | Genomsnittlig byte/skrivning              |
| Dataflöde   | Databyten per sekund                |
|              | Lästa byte/sek                |
|              | Skrivna byte/sek               |
| Kölängd | Genomsnittlig kölängd        |
|              | Genomsnittlig skrivkölängd       |
|              | Genomsnittlig datakölängd        |

### <a name="advanced-performance-analysis"></a>Avancerad prestandaanalys

När du kör en avancerad prestandaanalys väljer du spårningar som ska köras parallellt. Om du vill kan du köra dem alla (prestandaräknare, Xperf, nätverk och StorPort).  

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett live-produktionssystem. Om det behövs kör du det här scenariot i ett dedikerat underhållsfönster för att undvika problem. En ökad arbetsbelastning som orsakas av ett spårnings- eller benchmark-test kan påverka datorns prestanda negativt.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Vilken typ av information samlas in av PerfInsights?

Information om konfiguration av Windows VM, diskar eller lagringspooler, prestandaräknare, loggar och olika spårningar samlas in. Det beror på vilket prestandascenario du använder. Följande tabell innehåller mer information:

|Insamlade data                              |  |  | Prestandascenarier |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Snabb prestandaanalys | Benchmarking | Resultatanalys | Analys av Azure-filer | Avancerad prestandaanalys |
| Information från händelseloggar       | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Systeminformation                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Volymkarta                        | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Diskkarta                          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Köra uppgifter                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Tillförlitlighetsräknare för lagring      | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Lagringsinformation               | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Fsutil-utgång                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Information om filterdrivrutin                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netstat-utgång                    | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Nätverkskonfiguration             | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Konfigurering av brandvägg            | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| SQL Server-konfiguration          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Prestandadiagnostik spår *  | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Spårning av prestandaräknare **      |                            |                                    | Ja                      |                      | Ja                  |
| SMB-räknare spår **              |                            |                                    |                          | Ja                  |                      |
| SQL Server-räknarespårning **       |                            |                                    | Ja                      |                      | Ja                  |
| Xperf spår                       |                            |                                    |                          |                      | Ja                  |
| StorPort-spårning                    |                            |                                    |                          |                      | Ja                  |
| Nätverksspårning                     |                            |                                    |                          | Ja                  | Ja                  |
| Diskspd benchmark trace ***       |                            | Ja                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Spårning av prestandadiagnostik (*)

Kör en regelbaserad motor i bakgrunden för att samla in data och diagnostisera pågående prestandaproblem. Följande regler stöds för närvarande:

- HighCpuUsage-regel: Identifierar höga CPU-användningsperioder och visar de bästa processoranvändningskonsumenterna under dessa perioder.
- HighDiskUsage-regel: Identifierar hög diskanvändningsperioder på fysiska diskar och visar de bästa diskanvändningskonsumenterna under dessa perioder.
- HighResolutionDiskMetric-regel: Visar IOPS- och dataflödesmått och I/O-svarstid per 50 millisekunder för varje fysisk disk. Det hjälper till att snabbt identifiera diskbegränsningsperioder.
- HighMemoryUsage-regeln: Identifierar hög minnesanvändningsperioder och visar de bästa minnesanvändningskonsumenterna under dessa perioder.

> [!NOTE] 
> För närvarande stöds Windows-versioner som innehåller .NET Framework 4.5 eller senare versioner.

### <a name="performance-counter-trace-"></a>Spårning av prestandaräknare (**)

Samlar in följande prestandaräknare:

- \Process, \Processor, \Minne, \Tråd, \PhysicalDisk och \LogicalDisk
- \Cache\Smutsiga sidor, \Cache\Lat skrivavfärgning/sek, \Server\Pool ej växlings-, fel och \Server\Pool-sidtabellsfel
- Markerade räknare under \Nätverksgränssnitt, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segment, \TCPv6\Segment, \Nätverkskort, \WFPv4\Paket, \WFPv6\Paket, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ Nätverks-QoS-princip\Paket, \Per processor nätverkskortaktivitet och \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>För SQL Server-instanser
- \SQL Server:Bufferthanteraren, \SQLServer:Resource Pool Stats och \SQLServer:SQL Statistics\
- \SQLServer:Lås, \SQLServer:Allmänt, Statistik
- \SQLServer:Access-metoder

#### <a name="for-azure-files"></a>För Azure-filer
\SMB-klientresurser

### <a name="diskspd-benchmark-trace-"></a>Diskspd benchmark-spårning (***)
Diskspd I/O-arbetsbelastningstester (OS Disk [write] och poolenheter [läs/skriv])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Kör PerfInsights-verktyget på din virtuella dator

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Vad måste jag veta innan jag kör verktyget? 

#### <a name="tool-requirements"></a>Verktygskrav

-  Det här verktyget måste köras på den virtuella datorn som har prestandaproblemet. 

-  Följande operativsystem stöds: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Windows 8.1 och Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Möjliga problem när du kör verktyget på virtuella produktions-datorer

-  För benchmarking-scenariot eller scenariot "Avancerad prestandaanalys" som är konfigurerat för att använda Xperf eller Diskspd kan verktyget påverka den virtuella datorns prestanda negativt. Dessa scenarier bör inte köras i en levande produktionsmiljö.

-  För benchmarking-scenariot eller scenariot "Avancerad prestandaanalys" som är konfigurerat för att använda Diskspd, se till att ingen annan bakgrundsaktivitet stör I/O-arbetsbelastningen.

-  Som standard använder verktyget den tillfälliga lagringsenheten för att samla in data. Om spårningen förblir aktiverad under en längre tid kan mängden data som samlas in vara relevant. Detta kan minska tillgängligheten för utrymme på den temporära disken och kan därför påverka alla program som är beroende av den här enheten.

### <a name="how-do-i-run-perfinsights"></a>Hur kör jag PerfInsights? 

Du kan köra PerfInsights på en virtuell dator genom att installera [Azure Performance Diagnostics VM Extension](performance-diagnostics-vm-extension.md). Du kan också köra den som ett fristående verktyg. 

**Installera och köra PerfInsights från Azure-portalen**

Mer information om det här alternativet finns i [Installera Azure Performance Diagnostics VM-tillägg](performance-diagnostics-vm-extension.md#install-the-extension).  

**Kör PerfInsights i fristående läge**

Så här kör du perfInsights-verktyget:


1. Ladda ner [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Ta bort blockeringen av filen PerfInsights.zip. Det gör du genom att högerklicka på filen PerfInsights.zip och välja **Egenskaper**. Välj Ta bort **blockering**på fliken **Allmänt** och välj sedan **OK**. Detta säkerställer att verktyget körs utan några ytterligare säkerhetsankuppringar.  

    ![Skärmbild av PerfInsights-egenskaper, med Avblock markerad](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Expandera den komprimerade PerfInsights.zip-filen till den tillfälliga enheten (som standard är detta vanligtvis D-enheten). 

4.  Öppna Kommandotolken i Windows som administratör och kör sedan PerfInsights.exe för att visa tillgängliga kommandoradsparametrar.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Skärmbild av PerfInsights-kommandoradsutdata](media/how-to-use-perfInsights/pi-commandline.png)
    
    Den grundläggande syntaxen för att köra PerfInsights-scenarier är:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Du kan använda exemplet nedan för att köra prestandaanalysscenario i 5 minuter:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda följande exempel för att köra det avancerade scenariot med Xperf- och prestandaräknarespårningar i 5 minuter:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda exemplet nedan för att köra prestandaanalysscenario i 5 minuter och ladda upp zip-filen för resultatet till lagringskontot:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Du kan slå upp alla tillgängliga scenarier och alternativ med kommandot **/list:**
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Innan du kör ett scenario uppmanar PerfInsights användaren att gå med på att dela diagnostisk information och godkänna licensavtalet. Använd **alternativet /AcceptDisclaimerAndShareDiagnostics** för att hoppa över dessa uppmaningar. 
    >
    >Om du har en aktiv supportbiljett med Microsoft och kör PerfInsights enligt begäran från supportteknikern du arbetar med, se till att ange supportbiljettnumret med alternativet **/sr.**
    >
    >Som standard försöker PerfInsights uppdatera sig själv till den senaste versionen om den är tillgänglig. Använd **parametern /SkipAutoUpdate** eller **/sau** för att hoppa över automatisk uppdatering.  
    >
    >Om varaktighetsväxeln **/d** inte har angetts uppmanas PerfInsights dig att återberätta problemet när du kör vmslow, azurefiles och avancerade scenarier. 

När spårningarna eller åtgärderna är slutförda visas en ny fil i samma mapp som PerfInsights. Namnet på filen är **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.zip.** Du kan skicka den här filen till supportagenten för analys eller öppna rapporten i zip-filen för att granska resultat och rekommendationer.

## <a name="review-the-diagnostics-report"></a>Granska diagnostikrapporten

I **filen PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** hittar du en HTML-rapport som beskriver resultaten av PerfInsights. Om du vill granska rapporten expanderar du **filen PerformanceDiagnostics\_\_yyy-MM-dd hh-mm-ss-fff.zip** och öppnar sedan filen **PerfInsights Report.html.**

Välj fliken **Resultat.**

![Skärmbild av PerfInsights Report](media/how-to-use-perfInsights/pi-finding-tab.png)
![Skärmdump av PerfInsights Report](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Resultat som kategoriseras som höga är kända problem som kan orsaka prestandaproblem. Resultat som kategoriseras som medium representerar icke-optimala konfigurationer som inte nödvändigtvis orsakar prestandaproblem. Resultat kategoriseras som låg är informativa uttalanden bara.

Granska rekommendationerna och länkarna för alla höga och medelstora resultat. Läs mer om hur de kan påverka prestanda och även om metodtips för prestandaoptimerade konfigurationer.

### <a name="storage-tab"></a>Fliken Lagring

I avsnittet **Resultat** visas olika resultat och rekommendationer relaterade till lagring.

I avsnitten **Diskkarta** och **Volymkarta** beskrivs hur logiska volymer och fysiska diskar är relaterade till varandra.

I det fysiska diskperspektivet (Diskmappning) visar tabellen alla logiska volymer som körs på disken. I följande exempel kör **PhysicalDrive2** två logiska volymer som skapats på flera partitioner (J och H):

![Skärmbild av fliken Disk](media/how-to-use-perfInsights/pi-disk-tab.png)

I volymperspektivet (Volymkarta) visar tabellerna alla fysiska diskar under varje logisk volym. Observera att för RAID/Dynamiska diskar kan du köra en logisk volym på flera fysiska diskar. I följande exempel är *C:\\mount* en monteringspunkt som konfigurerats som *SpannedDisk* på fysiska diskar 2 och 3:

![Skärmbild av fliken Volym](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Fliken SQL

Om måldatorn är värd för sql server-instanser visas ytterligare en flik i rapporten med namnet **SQL:**

![Skärmbild av fliken SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Det här avsnittet innehåller fliken **Resultat** och ytterligare flikar för var och en av SQL Server-instanserna som finns på den virtuella datorn.

Fliken **Resultat** innehåller en lista över alla SQL-relaterade prestandaproblem som hittades, tillsammans med rekommendationerna.

I följande exempel visas **PhysicalDrive0** (som kör C-enheten). Detta beror på att både **modeldev-** och **modellloggfilerna** finns på C-enheten, och de är av olika typer (till exempel datafil respektive transaktionslogg).

![Skärmbild av logginformation](media/how-to-use-perfInsights/pi-log-info.png)

Flikarna för specifika instanser av SQL Server innehåller ett allmänt avsnitt som visar grundläggande information om den valda förekomsten. Flikarna innehåller också ytterligare avsnitt för avancerad information, inklusive inställningar, konfigurationer och användaralternativ.

### <a name="diagnostic-tab"></a>Fliken Diagnostik
Fliken **Diagnostik** innehåller information om de bästa cpu-, disk- och minneskonsumenterna på datorn under hela körningen av PerfInsights. Du kan också hitta information om kritiska korrigeringar som systemet kan sakna, uppgiftslistan och viktiga systemhändelser. 

## <a name="references-to-the-external-tools-used"></a>Referenser till de externa verktyg som används

### <a name="diskspd"></a>Diskspd

Diskspd är ett testverktyg för lagringsbelastning och prestandatest från Microsoft. Mer information finns i [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf (på andra sätt)

Xperf är ett kommandoradsverktyg för att fånga spår från Windows Performance Toolkit. Mer information finns i [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Nästa steg

Du kan ladda upp diagnostikloggar och rapporter till Microsoft Support för vidare granskning. Support kan begära att du överför utdata som genereras av PerfInsights för att hjälpa till med felsökningsprocessen.

Följande skärmbild visar ett meddelande som liknar det du kan få:

![Skärmbild av exempelmeddelande från Microsoft Support](media/how-to-use-perfInsights/pi-support-email.png)

Följ instruktionerna i meddelandet för att komma åt filöverföringsarbetsytan. För ytterligare säkerhet måste du ändra ditt lösenord vid första användningen.

När du har loggat in hittar du en dialogruta för att ladda upp **filen PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** som samlades in av PerfInsights.

