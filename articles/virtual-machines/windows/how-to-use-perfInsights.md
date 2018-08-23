---
title: Använda PerfInsights i Microsoft Azure | Microsoft Docs
description: Lär sig hur du använder PerfInsights för att felsöka prestandaproblem för Windows-VM.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 16d023a2f3abf0feb1f1c0478edb3de7a157d5a4
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058178"
---
# <a name="how-to-use-perfinsights"></a>Hur du använder PerfInsights

[PerfInsights](http://aka.ms/perfinsightsdownload) är ett verktyg för självhjälp diagnostik som samlar in och analyserar diagnostiska data och ger en rapport för att felsöka prestandaproblem för Windows-dator i Azure. PerfInsights kan köras på virtuella datorer som ett fristående verktyg eller direkt från portalen genom att installera [Diagnostiktillägget för virtuell dator på Azure prestanda](performance-diagnostics-vm-extension.md).

Om du har problem med prestanda för virtuella datorer, innan du kontaktar support kan du köra det här verktyget.

## <a name="supported-troubleshooting-scenarios"></a>Felsökning scenarier som stöds

PerfInsights kan samla in och analysera flera olika typer av information. Följande avsnitt beskriver vanliga scenarier.

### <a name="quick-performance-analysis"></a>Snabb prestandaanalys

Det här scenariot samlar in diskkonfigurationen och annan viktig information, inklusive:

-   Händelseloggar

-   Nätverksstatus för alla inkommande och utgående anslutningar

-   Inställningar för nätverk och brandvägg

-   Uppgiftslista för alla program som körs på systemet

-   Microsoft SQL Server-databas-konfigurationsinställningar (om den virtuella datorn har identifierats som en server som kör SQL Server)

-   Storage tillförlitlighet räknare

-   Viktiga snabbkorrigeringar för Windows

-   Installerade filterdrivrutiner

Det här är en passiv insamling av information som inte ska påverka systemet. 

>[!Note]
>Det här scenariot ingår automatiskt i var och en av följande scenarier:

### <a name="benchmarking"></a>Prestandatest

Det här scenariot körs den [Diskspd](https://github.com/Microsoft/diskspd) benchmark-test (IOPS och Mbit/s) för alla enheter som är kopplade till den virtuella datorn. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Om det behövs kör du det här scenariot i en dedikerad underhållsperiod för att undvika eventuella problem. En ökad belastning som orsakas av ett spårningen eller benchmark-test kan försämra prestanda för den virtuella datorn.
>

### <a name="performance-analysis"></a>Prestandaanalys

Det här scenariot körs en [prestandaräknaren](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) spårningen med hjälp av de räknare som anges i filen RuleEngineConfig.json. Om den virtuella datorn identifieras som en server som kör SQL Server, kör en räknare spårning av prestanda. Detta sker med hjälp av de räknare som finns i filen RuleEngineConfig.json. Det här scenariot omfattar också diagnostik prestandadata.

### <a name="azure-files-analysis"></a>Azure Files-analys

Det här scenariot körs en särskild prestandaräknaren avbildning tillsammans med en nätverksspårning. Avbildningen innehåller alla Server Message Block (SMB) klienten resurser räknare. Här följer några viktiga SMB-resurs prestandaräknare i klient som ingår i avbildningen:

| **Typ**     | **SMB-klienten resurser räknare** |
|--------------|-------------------------------|
| IOPS         | Begäranden om data per sekund             |
|              | Läs begäranden/sek             |
|              | Skriva begäranden/sek            |
| Svarstid      | Medel s/databegäran         |
|              | Medel s/diskläsning                 |
|              | Medel s/diskskrivning                |
| I/o-storlek      | Genomsn. Byte/begäran       |
|              | Genomsn. Byte/diskläsning               |
|              | Genomsn. Byte/skrivning              |
| Dataflöde   | Data byte/sek                |
|              | Lästa byte/sek                |
|              | Skrivna byte/s               |
| Kölängd | Genomsn. Läs Kölängd        |
|              | Genomsn. Skriva Kölängd       |
|              | Genomsn. Kölängd för data        |

### <a name="advanced-performance-analysis"></a>Avancerade prestandaanalys

När du kör en avancerad analys, väljer du spårningar som körs parallellt. Om du vill kan köra du dem alla (prestandaräknare, Xperf, nätverk och StorPort).  

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Om det behövs kör du det här scenariot i en dedikerad underhållsperiod för att undvika eventuella problem. En ökad belastning som orsakas av ett spårningen eller benchmark-test kan försämra prestanda för den virtuella datorn.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Vilken typ av information som samlas in av PerfInsights?

Information om Windows-VM, diskar eller pooler lagringskonfiguration, prestandaräknare, loggar och olika spårningssessioner samlas in. Det beror på vilket scenario för prestanda som du använder. I följande tabell innehåller information:

|Data som samlas in                              |  |  | Prestanda-scenarier |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Snabb prestandaanalys | Prestandatest | Prestandaanalys | Azure Files-analys | Avancerade prestandaanalys |
| Information från händelseloggar       | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Systeminformation                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Volymen karta                        | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Disk-karta                          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Aktiviteter som körs                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Storage tillförlitlighet räknare      | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Lagringsinformation               | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Fsutil utdata                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Drivrutinen filterinformationen                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netstat-utdata                    | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Nätverkskonfiguration             | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Brandväggskonfiguration            | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Konfiguration av SQL Server          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Diagnostik prestandaspårningar *  | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Prestandaräknaren trace **      |                            |                                    | Ja                      |                      | Ja                  |
| SMB-räknaren trace **              |                            |                                    |                          | Ja                  |                      |
| Spårning av SQL Server-räknaren **       |                            |                                    | Ja                      |                      | Ja                  |
| XPerf spårning                       |                            |                                    |                          |                      | Ja                  |
| StorPort-spårning                    |                            |                                    |                          |                      | Ja                  |
| Nätverksspårning                     |                            |                                    |                          | Ja                  | Ja                  |
| Diskspd benchmark trace ***       |                            | Ja                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Prestandadiagnostik spåra (*)

Kör en regelbaserad motor i bakgrunden för att samla in data och diagnostisera problem med pågående prestanda. För närvarande stöds följande regler:

- HighCpuUsage regel: identifierar perioder med hög CPU-användning och visar översta CPU-användning konsumenterna under de här perioderna.
- HighDiskUsage regel: identifierar ett högt användning punkter på fysiska diskar och visar den högsta disken konsumenter för användning under de här perioderna.
- HighResolutionDiskMetric regel: Visar IOPS, dataflöde och i/o mått för datainmatningssvarstider per 50 tid i millisekunder för varje fysisk disk. Det hjälper dig att snabbt identifiera disken begränsning perioder.
- HighMemoryUsage regel: identifierar perioder för användning av extra minne och visar det översta minnet konsumenter för användning under de här perioderna.

> [!NOTE] 
> Windows-versioner med .NET Framework 4.5 eller senare versioner är för närvarande.

### <a name="performance-counter-trace-"></a>Prestandaräknaren spårning (*)

Samlar in följande prestandaräknare:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk och \LogicalDisk
- \Cache\Dirty sidor, \Cache\Lazy skrivna rensar/sek, \Server\Pool växlingsbart systemminne, fel och \Server\Pool systemminne-fel
- Valda räknare under \Network gränssnitt, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network nätverkskort, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ QoS-Policy\Packets, \Per Network Interface Card processoraktivitet och \Microsoft Winsock NSP

#### <a name="for-sql-server-instances"></a>För SQL Server-instanser
- \SQL server: bufferten Manager, \SQLServer:Resource Pool statistik och \SQLServer:SQL Statistics\
- \SQLServer:locks, \SQLServer:General, statistik
- \SQLServer:Access metoder

#### <a name="for-azure-files"></a>För Azure Files
\SMB Klientresurser

### <a name="diskspd-benchmark-trace-"></a>Diskspd benchmark-spårning (*)
I/o för Diskspd arbetsbelastning tester (OS-Disk [Skriv] och poolen enheter [Läs/Skriv])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Kör verktyget PerfInsights på den virtuella datorn

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Vad måste jag känna till innan jag kör verktyget? 

#### <a name="tool-requirements"></a>Verktyget krav

-  Det här verktyget måste köras på den virtuella datorn som har prestandaproblem. 

-  Följande operativsystem stöds: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016; Windows 8.1 och Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Möjliga problem när du kör verktyget på virtuella produktionsdatorer

-  För benchmarking scenariot eller ”Advanced prestandaanalys”-scenario som är konfigurerad för att använda Xperf eller Diskspd kan verktyget påverka prestanda negativt för den virtuella datorn. Dessa scenarier bör inte köras i en aktiv produktionsmiljö.

-  Se till att inga andra bakgrundsaktiviteten stör arbetsbelastningen i/o för benchmarking scenariot eller ”Advanced prestandaanalys”-scenario som är konfigurerad för att använda Diskspd.

-  Som standard använder verktyget en tillfällig lagringsenhet för att samla in data. Om spårning är kvar aktiverad under en längre tid, kan det vara relevanta mängden data som samlas in. Detta kan minska tillgängligheten för utrymme på den temporära disken och kan därför påverka alla program som förlitar sig på den här enheten.

### <a name="how-do-i-run-perfinsights"></a>Hur kör jag PerfInsights? 

Du kan köra PerfInsights på en virtuell dator genom att installera [Diagnostiktillägget för virtuell dator på Azure prestanda](performance-diagnostics-vm-extension.md). Du kan också köras som ett fristående verktyg. 

**Installera och köra PerfInsights från Azure portal**

Mer information om det här alternativet finns i [installera Azure prestanda VM-Diagnostiktillägget](performance-diagnostics-vm-extension.md#install-the-extension).  

**Kör PerfInsights i fristående läge**

Följ dessa steg om du vill köra verktyget PerfInsights:


1. Ladda ned [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Avblockera PerfInsights.zip-filen. Gör detta genom att högerklicka på filen PerfInsights.zip och välj **egenskaper**. I den **Allmänt** fliken **avblockera**, och välj sedan **OK**. Detta säkerställer att verktyget körs utan någon ytterligare säkerhetsmeddelanden.  

    ![Skärmbild av PerfInsights egenskaper, med avblockera markerat](media/how-to-use-perfInsights/unlock-file.png)

3.  Expandera den komprimerade filen PerfInsights.zip i tillfälliga enheten (som standard, detta är vanligtvis D-hårddisken). 

4.  Öppna Windows kommandotolk som administratör och kör sedan PerfInsights.exe om du vill visa tillgängliga commandline-parametrar.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Skärmbild av PerfInsights commandline utdata](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Grundläggande syntax för att köra PerfInsights scenarier är:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Du kan använda i exemplet för att köra prestanda dataanalysscenario för 5 minuter nedan:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda följande exempel för att köra Avancerat scenario med Xperf och prestanda räknaren spårningar för 5 minuter:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda det exemplet kör prestanda dataanalysscenario för 5 minuter och ladda upp zip-resultatfilen till lagringskontot nedan:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Du kan söka efter alla tillgängliga scenarier och alternativ med hjälp av den **/lista** kommando:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Innan du kör ett scenario uppmanar PerfInsights användaren att samtycker till att dela diagnostikinformation och för att godkänna LICENSAVTALET. Använd **/AcceptDisclaimerAndShareDiagnostics** möjlighet att hoppa över de här anvisningarna. 
    >
    >Om du har en aktiv supportärende hos Microsoft och som körs PerfInsights per begäran av supportteknikern du arbetar med, se till att tillhandahålla en support biljett nummer med hjälp av den **/sr** alternativet.
    >
    >Som standard försöker PerfInsights uppdaterar sig själv till den senaste versionen om de är tillgängliga. Använd **/SkipAutoUpdate** eller **/sau** parametern för att hoppa över automatisk uppdatering.  
    >
    >Om hela växlar **/d** inte anges PerfInsights uppmanas du att reproducera problemet när du kör vmslow azurefiles och avancerade scenarier. 

När spårningar eller åtgärder har slutförts, visas en ny fil i samma mapp som PerfInsights. Namnet på filen är **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip.** Du kan skicka den här filen till support-agenten för analys eller öppna rapporten i zip-filen till att granska resultaten och rekommendationerna.

## <a name="review-the-diagnostics-report"></a>Granska rapporten diagnostik

I den **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip** -fil som du kan hitta en HTML-rapport som beskriver resultaten av PerfInsights. För att granska rapporten, expandera den **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip** filen och öppna sedan den **PerfInsights Report.html** fil.

Välj den **resultat** fliken.

![Skärmbild av PerfInsights rapporten](media/how-to-use-perfInsights/findingtab.png)
![Skärmbild av PerfInsights rapport](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Resultat som kategoriseras som hög är kända problem som kan orsaka prestandaproblem. Resultat kategoriserat som medel representerar bristfälliga konfigurationer som inte nödvändigtvis orsakar prestandaproblem. Resultat som kategoriserats som låg är informativa instruktionerna.

Granska rekommendationer och länkar till alla hög och medelhög resultat. Lär dig om hur de kan påverka prestanda och även om bästa praxis för prestandaoptimerad konfigurationer.

### <a name="storage-tab"></a>Fliken lagring

Den **resultat** avsnittet visar olika resultat och rekommendationer relaterade till lagring.

Den **Disk kartan** och **volym kartan** avsnitt beskrivs logiska volymer och fysiska diskar som är relaterade till varandra.

I perspektivet fysisk disk (Disk Map) visas i tabell alla logiska volymer som körs på disken. I följande exempel **PhysicalDrive2** körs två logiska volymer som har skapats på flera partitioner (J och H):

![Skärmbild av fliken](media/how-to-use-perfInsights/disktab.png)

I volym-perspektiv (volym Map) visar tabellerna alla fysiska diskar under varje logisk volym. Observera att för RAID/dynamiska diskar, kan du köra en logisk volym på flera fysiska diskar. I följande exempel *C:\\montera* konfigureras en monteringspunkt som *SpannedDisk* på fysiska diskar med 2 och 3:

![Skärmbild av fliken volym](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL-flik

Om den Virtuella måldatorn är värd för alla SQL Server-instanser måste du se ytterligare en flik i rapporten med namnet **SQL**:

![Skärmbild av SQL-flik](media/how-to-use-perfInsights/sqltab.png)

Det här avsnittet innehåller en **resultat** fliken och ytterligare flikarna för var och en av SQL Server-instanserna på den virtuella datorn.

Den **resultat** fliken innehåller en lista över alla SQL relaterade prestandaproblem som hittas, tillsammans med rekommendationer.

I följande exempel **PhysicalDrive0** (körs C-enheten) visas. Detta beror på både den **modeldev** och **modellog** filerna finns på enhet C och de är av olika typer (till exempel data fil- och transaktionen loggen, respektive).

![Skärmbild av logginformation](media/how-to-use-perfInsights/loginfo.png)

Flikarna för specifika instanser av SQL Server innehåller ett allmänt avsnitt som visar grundläggande information om den valda instansen. Flikarna också innehålla ytterligare avsnitt avancerad information, inklusive inställningar, konfigurationer och alternativ.

### <a name="diagnostic-tab"></a>Fliken diagnostik
Den **diagnostiska** fliken innehåller information om främsta konsumenter av CPU, disk och minne på datorn under löpande PerfInsights. Du kan också hitta information om kritiska korrigeringar att systemet kanske saknas, uppgiftslista och viktiga händelser. 

## <a name="references-to-the-external-tools-used"></a>Referenser till de externa verktyg som används

### <a name="diskspd"></a>Diskspd

Diskspd är ett storage belastningen generator och prestanda test verktyg från Microsoft. Mer information finns i [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf är ett kommandoradsverktyg för att fånga in spårningar från Windows Performance Toolkit. Mer information finns i [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Nästa steg

Du kan överföra diagnostikloggar och rapporter till Microsoft Support och granska dem vidare. Support begära att du överför de utdata som genereras av PerfInsights som hjälper till med felsökningen.

Följande skärmbild visar ett meddelande som liknar vad du kan få:

![Skärmbild av exempelmeddelande från Microsoft Support](media/how-to-use-perfInsights/supportemail.png)

Följ instruktionerna i meddelandet för att få åtkomst till arbetsytan för överföring av filen. För ytterligare säkerhet måste du ändra ditt lösenord vid första användningen.

När du har loggat in hittar du en dialogruta för att ladda upp den **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip** -fil som har samlats in av PerfInsights.
