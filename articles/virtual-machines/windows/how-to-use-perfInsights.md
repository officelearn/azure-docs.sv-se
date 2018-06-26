---
title: Hur du använder PerfInsights i Microsoft Azure | Microsoft Docs
description: Veta hur du använder PerfInsights för att felsöka Windows VM prestandaproblem.
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
ms.openlocfilehash: 2f496f906eef416b35e2e59b2db93481ce65acb1
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946539"
---
# <a name="how-to-use-perfinsights"></a>Hur du använder PerfInsights

[PerfInsights](http://aka.ms/perfinsightsdownload) är ett verktyg för självhjälp diagnostik som samlar in & analyserar diagnostiska data, och ger en rapport för att felsöka problem med Windows virtuella prestanda i Azure. PerfInsights kan köras på virtuella datorer som ett fristående verktyg eller direkt från portalen genom att installera [Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md).

Kör det här verktyget om du får prestandaproblem med virtuella datorer innan du kontaktar supporten.

## <a name="supported-troubleshooting-scenarios"></a>Felsökning scenarier som stöds

PerfInsights kan samla in och analysera flera typer av information. Följande avsnitt beskriver vanliga scenarier.

### <a name="quick-performance-analysis"></a>Snabb prestandaanalys

Det här scenariot samlar in diskkonfigurationen och annan viktig information, inklusive:

-   Händelseloggar

-   Nätverkets status för alla inkommande och utgående anslutningar

-   Inställningarna för nätverk och brandvägg

-   Uppgiftslista för alla program som körs på systemet

-   Microsoft SQL Server-databasen-konfigurationsinställningarna (om den virtuella datorn identifieras som en server som kör SQL Server)

-   Lagring tillförlitlighet räknare

-   Viktiga snabbkorrigeringar för Windows

-   Installerade filterdrivrutiner

Detta är en passiv insamling av information som inte bör påverka systemet. 

>[!Note]
>Det här scenariot ingår automatiskt i var och en av följande scenarier:

### <a name="benchmarking"></a>Prestandamätningar

Det här scenariot körs den [Diskspd](https://github.com/Microsoft/diskspd) benchmark-test (IOPS och Mbit/s) för alla enheter som är kopplade till den virtuella datorn. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test kan försämra prestanda på den virtuella datorn.
>

### <a name="slow-vm-analysis"></a>Långsam VM analys

Det här scenariot körs en [prestandaräknaren](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) spårningen med hjälp av de räknare som anges i filen RuleEngineConfig.json. Om den virtuella datorn identifieras som en server som kör SQL Server, kör en räknare spårning av prestanda. Detta sker med hjälp av de räknare som finns i filen RuleEngineConfig.json. Det här scenariot omfattar också diagnostik prestandadata.

### <a name="azure-files-analysis"></a>Azure Files analys

Det här scenariot körs en särskild prestandaräknaren avbildning tillsammans med en spårning i nätverket. Avbildningen innehåller alla Server Message Block (SMB) klienten resurser räknare. Här följer några viktiga SMB-resursen prestandaräknare i klient som ingår i avbildningen:

| **Typ**     | **SMB-klienten resurser räknare** |
|--------------|-------------------------------|
| IOPS         | Data-begäranden per sekund             |
|              | Läs begäranden per sekund             |
|              | Skriva begäranden per sekund            |
| Svarstid      | Medel s/begäran         |
|              | Medel s/diskläsning                 |
|              | Medel s/diskskrivning                |
| I/o-storlek      | Genomsn. Begäran om byte-Data       |
|              | Genomsn. Lästa byte /               |
|              | Genomsn. Byte/skrivning              |
| Dataflöde   | Byte/sek                |
|              | Lästa byte/sek                |
|              | Skrivna byte/s               |
| Kölängd | Genomsn. Läs Kölängd        |
|              | Genomsn. Skriva Kölängd       |
|              | Genomsn. Kölängd för data        |

### <a name="advanced-slow-vm-analysis"></a>Avancerad analys långsam VM

När du kör en avancerad analys för långsam VM markerar spårningar som körs parallellt. Om du vill kan köra du dem alla (prestandaräknaren Xperf, nätverk och StorPort).  

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test kan försämra prestanda på den virtuella datorn.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Vilken typ av information som samlas in av PerfInsights?

Information om Windows VM, diskar eller pooler lagringskonfiguration, prestandaräknare, loggar och olika spårningssessioner samlas in. Det beror på prestanda-scenario som du använder. Följande tabell innehåller information:

|Data som samlas in                              |  |  | Scenarier för prestanda |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Snabb prestandaanalys | Prestandamätningar | Långsam VM analys | Azure Files analys | Avancerad analys långsam VM |
| Information från händelseloggar       | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Systeminformation                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Volymen karta                        | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Disk-karta                          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Aktiviteter som körs                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Lagring tillförlitlighet räknare      | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Storage-informationen               | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Fsutil utdata                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Drivrutinen filterinformationen                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netstat-utdata                    | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Nätverkskonfiguration             | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Brandväggskonfiguration            | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Konfiguration av SQL Server          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Prestanda diagnostik spårningar *  | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Prestandaräknaren trace **      |                            |                                    | Ja                      |                      | Ja                  |
| SMB-räknaren trace **              |                            |                                    |                          | Ja                  |                      |
| SQL Server räknaren trace **       |                            |                                    | Ja                      |                      | Ja                  |
| XPerf spårning                       |                            |                                    |                          |                      | Ja                  |
| StorPort-spårning                    |                            |                                    |                          |                      | Ja                  |
| Spårning i nätverket                     |                            |                                    |                          | Ja                  | Ja                  |
| Diskspd benchmark trace ***       |                            | Ja                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Prestanda diagnostik trace (*)

Kör en regelbaserad motor i bakgrunden för att samla in data och diagnostisera pågående prestandaproblem. Följande regler stöds:

- HighCpuUsage regel: identifierar perioder med hög CPU-användning och visar de högsta CPU användning konsumenterna under dessa punkter.
- HighDiskUsage regel: identifierar ett högt användning punkter på fysiska diskar och visar högsta disk användning konsumenter under dessa punkter.
- HighResolutionDiskMetric regel: Visar IOPS, genomflöde och i/o-svarstid mått per 50 tid i millisekunder för varje fysisk disk. Det hjälper till att snabbt identifiera disken begränsning punkter.
- HighMemoryUsage regel: identifierar höga minnesområdet användning punkter och visar det översta minnet användning konsumenter under dessa punkter.

> [!NOTE] 
> För närvarande stöds Windows-versioner med .NET Framework 3.5 eller senare versioner.

### <a name="performance-counter-trace-"></a>Prestandaräknaren trace (*)

Samlar in följande prestandaräknare:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk och \LogicalDisk
- \Cache\Dirty sidor, \Cache\Lazy tömningar/s, \Server\Pool växlingsbart systemminne, fel och \Server\Pool växlingsbart systemminne-fel
- Valda räknare under \Network gränssnitt, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network nätverkskort, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ Nätverket QoS Policy\Packets \Per Network Interface Card processoraktivitet och \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>För SQL Server-instanser
- \SQL server: bufferthanteraren \SQLServer:Resource Pool statistik och \SQLServer:SQL Statistics\
- \SQLServer:locks \SQLServer:General statistik
- \SQLServer:Access metoder

#### <a name="for-azure-files"></a>För Azure-filer
\SMB Klientresurser

### <a name="diskspd-benchmark-trace-"></a>Diskspd benchmark-spårning (*)
I/o för Diskspd arbetsbelastning prov (OS-Disk [Skriv] och pool enheter [läsning/skrivning])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Kör verktyget PerfInsights på den virtuella datorn

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Vad måste jag känna till innan jag kör verktyget? 

#### <a name="tool-requirements"></a>Verktyget krav

-  Det här verktyget måste köras på den virtuella datorn som har prestandaproblem. 

-  Har stöd för följande operativsystem: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016; Windows 8.1 och Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Möjliga problem när du kör verktyget på produktion virtuella datorer

-  För benchmarking scenariot eller ”avancerad långsam VM analys” scenariot som är konfigurerad för att använda Xperf eller Diskspd kan verktyget påverka prestanda negativt för den virtuella datorn. Dessa scenarier ska inte köras i en verklig produktionsmiljö.

-  Se till att inga andra bakgrundsaktivitet stör i/o-arbetsbelastning för benchmarking scenariot eller ”avancerad långsam VM analys”-scenario som är konfigurerad för att använda Diskspd.

-  Som standard använder verktyget enhetens tillfällig lagring för att samla in data. Om spårning förblir aktiverad under en längre tid, vara mängden data som samlas in relevanta. Detta kan minska tillgängligheten för utrymmet på den tillfälliga disken och kan därför påverka alla program som förlitar sig på den här enheten.

### <a name="how-do-i-run-perfinsights"></a>Hur kör PerfInsights? 

Du kan köra PerfInsights på en virtuell dator genom att installera [Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md). Du kan också köras som ett fristående verktyg. 

**Installera och köra PerfInsights från Azure-portalen**

Mer information om det här alternativet finns [installera Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md#install-the-extension).  

**Kör PerfInsights i fristående läge**

Följ dessa steg om du vill köra verktyget PerfInsights:


1. Hämta [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Avblockera PerfInsights.zip-filen. Gör du genom att högerklicka på filen PerfInsights.zip och välj **egenskaper**. I den **allmänna** väljer **avblockera**, och välj sedan **OK**. Detta säkerställer att verktyget körs utan några ytterligare säkerhetsmeddelanden.  

    ![Skärmbild av PerfInsights egenskaper, med avblockera markerat](media/how-to-use-perfInsights/unlock-file.png)

3.  Expandera den komprimerade filen PerfInsights.zip i tillfälliga enheten (som standard, detta är vanligtvis enhet D). 

4.  Öppna Windows kommandotolk som administratör och kör sedan PerfInsights.exe om du vill visa tillgängliga commandline-parametrar.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Skärmbild av PerfInsights kommandorad för utdata](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Den grundläggande syntaxen för att köra PerfInsights scenarier är:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Du kan använda den under exempel för att köra långsam VM scenario för 5 minuter:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda följande exempel för att köra Avancerat scenario med Xperf och prestanda räknaren spårningar för 5 minuter:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda det exemplet körs långsamt VM scenario för 5 minuter och ladda upp zip-resultatfilen till lagringskontot nedan:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Du kan söka efter alla tillgängliga scenarier och alternativ med hjälp av den **/list** kommando:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Innan du kör ett scenario uppmanas PerfInsights användaren att godkänna att dela diagnostikinformation och om du accepterar LICENSVILLKOREN. Använd **/AcceptDisclaimerAndShareDiagnostics** möjlighet att hoppa över de här anvisningarna. 
    >
    >Om du har en aktiv supportärende hos Microsoft och körs PerfInsights per begäran av du arbetar med supportteknikern, se till att ange den stöd biljett tal med den **/sr** alternativet.
    >
    >Som standard försöker PerfInsights uppdatera sig själv till den senaste versionen om de är tillgängliga. Använd **/SkipAutoUpdate** eller **/sau** parametern för att hoppa över automatisk uppdatering.  
    >
    >Om varaktigheten växlar **/d** anges PerfInsights uppmanas du att reproducera problemet när du kör vmslow azurefiles och avancerade scenarier. 

När spårningssessioner eller åtgärder har slutförts, visas en ny fil i samma mapp som PerfInsights. Namnet på filen är **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip.** Du kan skicka filen till supportpersonalen för analys eller öppna rapporten i zip-filen för att granska resultaten och rekommendationerna.

## <a name="review-the-diagnostics-report"></a>Granska rapporten diagnostik

I den **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip** fil, du kan söka efter en HTML-rapport som beskriver resultaten av PerfInsights. För att granska rapporten, expandera den **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip** filen och öppna den **PerfInsights Report.html** fil.

Välj den **resultaten** fliken.

![Skärmbild av PerfInsights rapporten](media/how-to-use-perfInsights/findingtab.png)
![Skärmbild av PerfInsights rapport](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Resultaten kategoriseras som hög kända problem som kan orsaka prestandaproblem. Resultaten kategoriserat som medel representerar icke-optimal konfigurationer som inte nödvändigtvis orsakar prestandaproblem. Resultaten kategoriserade som låg är informativa instruktioner.

Granska rekommendationer och länkar till alla hög eller medelhög resultaten. Lär dig mer om hur de kan påverka prestanda och även om bästa praxis för prestanda-optimerad konfigurationer.

### <a name="storage-tab"></a>Fliken lagring

Den **resultaten** avsnittet visas olika resultat och rekommendationer om lagring.

Den **Disk kartan** och **volym kartan** avsnitt beskrivs hur logiska volymer och fysiska diskar som är relaterade till varandra.

Tabellen visar alla logiska volymer som körs på disken i perspektivet fysisk disk (Disk mappar). I följande exempel **PhysicalDrive2** körs två logiska volymer som har skapats på flera partitioner (J och H):

![Skärmbild av fliken](media/how-to-use-perfInsights/disktab.png)

Tabeller visar alla fysiska diskar under varje logiska volym i perspektivet volym (volym mappar). Observera att för RAID/dynamiska diskar, kan du köra en logisk volym på flera fysiska diskar. I följande exempel *C:\\montera* konfigureras en monteringspunkt som *SpannedDisk* på fysiska diskar 2 och 3:

![Skärmbild av fliken volym](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL-fliken

Om målet VM är värd för SQL Server-instanser måste du se ytterligare en flik i rapporten med namnet **SQL**:

![Skärmbild av SQL-fliken](media/how-to-use-perfInsights/sqltab.png)

Det här avsnittet innehåller en **resultaten** fliken och ytterligare flikarna för var och en av SQL Server-instanser som finns på den virtuella datorn.

Den **resultaten** fliken innehåller en lista över alla SQL relaterade prestandaproblem hittas, tillsammans med rekommendationer.

I följande exempel **PhysicalDrive0** (som kör enhet C) visas. Detta beror på att både den **modeldev** och **modellog** filerna finns på enhet C och de är av olika typer (till exempel data fil- och transaction log respektive).

![Skärmbild av logginformation](media/how-to-use-perfInsights/loginfo.png)

Flikar för specifika instanser av SQL Server innehåller ett allmänt avsnitt som innehåller grundläggande information om den valda instansen. Flikarna också innehålla ytterligare avsnitt avancerad information, inklusive inställningar, konfigurationer och användaralternativ.

### <a name="diagnostic-tab"></a>Fliken diagnostik
Den **diagnostiska** fliken innehåller information om högsta CPU, disk och minne konsumenter på datorn under körning av PerfInsights. Du kan också hitta information om viktiga korrigeringsfiler att systemet kanske saknas, uppgiftslista och viktiga systemhändelser. 

## <a name="references-to-the-external-tools-used"></a>Referenser till externa verktyg som används

### <a name="diskspd"></a>Diskspd

Diskspd är ett lagring belastningen generator och prestanda test från Microsoft. Mer information finns i [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf är ett kommandoradsverktyg för att fånga in spårningar från Windows Performance Toolkit. Mer information finns i [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Nästa steg

Du kan överföra diagnostik loggar och rapporter till Microsoft Support för ytterligare granskning. Support kan begära att du överför de utdata som genereras av PerfInsights för att underlätta felsökningen.

Följande skärmbild visar ett meddelande som liknar kan du få:

![Skärmbild av exempelmeddelande från Microsoft Support](media/how-to-use-perfInsights/supportemail.png)

Följ instruktionerna i meddelandet för att komma åt filen överföring arbetsytan. För ytterligare säkerhet som du behöver ändra ditt lösenord vid första användningen.

När du har loggat in hittar du en dialogruta för att överföra den **CollectedData\_åååå-MM-dd\_hh-mm-ss-fff.zip** filen som samlats in av PerfInsights.
