---
title: "Hur du använder PerfInsights i Microsoft Azure | Microsoft Docs"
description: "Veta hur du använder PerfInsights för att felsöka Windows VM prestandaproblem."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2018
---
# <a name="how-to-use-perfinsights"></a>Hur du använder PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) är ett automatiserat skript som samlar in viktig diagnostisk information. Den också körs i/o-belastning stress och ger en analysrapport för att felsöka problem med Windows virtuella prestanda i Azure. Det kan köras på virtuella datorer som en fristående skript eller direkt från portalen genom att installera [Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md).

Om du har prestandaproblem med med virtuella datorer innan du kontaktar supporten, köra skriptet.

## <a name="supported-troubleshooting-scenarios"></a>Felsökning scenarier som stöds

PerfInsights kan samla in och analysera flera typer av information. Följande avsnitt beskriver vanliga scenarier.

### <a name="collect-basic-configuration"></a>Samla in grundläggande konfiguration 

Det här scenariot samlar in diskkonfigurationen och annan viktig information, inklusive:

-   Händelseloggar

-   Nätverkets status för alla inkommande och utgående anslutningar

-   Inställningarna för nätverk och brandvägg

-   Uppgiftslista för alla program som körs på systemet

-   Informationsfilen som skapas av msinfo32 för den virtuella datorn

-   Microsoft SQL Server-databasen-konfigurationsinställningarna (om den virtuella datorn identifieras som en server som kör SQL Server)

-   Lagring tillförlitlighet räknare

-   Viktiga snabbkorrigeringar för Windows

-   Installerade filterdrivrutiner

Detta är en passiv insamling av information som inte bör påverka systemet. 

>[!Note]
>Det här scenariot ingår automatiskt i var och en av följande scenarier.

### <a name="benchmarking"></a>Benchmarking

Det här scenariot körs den [Diskspd](https://github.com/Microsoft/diskspd) benchmark-test (IOPS och Mbit/s) för alla enheter som är kopplade till den virtuella datorn. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test kan försämra prestanda på den virtuella datorn.
>

### <a name="slow-vm-analysis"></a>Långsam VM analys 

Det här scenariot körs en [prestandaräknaren](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) spårningen med hjälp av de räknare som anges i filen Generalcounters.txt. Om den virtuella datorn identifieras som en server som kör SQL Server, kör en räknare spårning av prestanda. Detta sker med hjälp av de räknare som finns i filen Sqlcounters.txt och den innehåller också diagnostik prestandadata.

### <a name="slow-vm-analysis-and-benchmarking"></a>Långsam VM-analys och prestandamätningar

Det här scenariot körs en [prestandaräknaren](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) spårning som följs av en [Diskspd](https://github.com/Microsoft/diskspd) benchmark-test. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test kan försämra prestanda på den virtuella datorn.
>

### <a name="azure-files-analysis"></a>Azure filer analys 

Det här scenariot körs en särskild prestandaräknaren avbildning tillsammans med en spårning i nätverket. Avbildningen innehåller alla Server Message Block (SMB) klienten resurser räknare. Här följer några viktiga SMB-resursen prestandaräknare i klient som ingår i avbildningen:

| **Typ**     | **SMB-klienten resurser räknare** |
|--------------|-------------------------------|
| IOPS         | Data-begäranden per sekund             |
|              | Läs begäranden per sekund             |
|              | Skriva begäranden per sekund            |
| Svarstid      | Medel s/begäran         |
|              | Medel s/diskläsning                 |
|              | Medel s/diskskrivning                |
| I/o-storlek      | Avg. Begäran om byte-Data       |
|              | Avg. Bytes/Read               |
|              | Avg. Byte/skrivning              |
| Dataflöde   | Byte/sek                |
|              | Lästa byte/sek                |
|              | Skrivna byte/s               |
| Queue Length | Avg. Läs Kölängd        |
|              | Avg. Skriva Kölängd       |
|              | Avg. Kölängd för data        |

### <a name="custom-slow-vm-analysis"></a>Anpassade långsam VM-analys 

När du kör en anpassad långsam VM-analys, markerar spårningar som körs parallellt. Du kan köra dem alla (prestandaräknaren Xperf, nätverk och StorPort) om du vill. När spårning är klar kör verktyget Diskspd-prestandamått, om den är markerad. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test kan försämra prestanda på den virtuella datorn.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Vilken typ av information som samlas in av skriptet?

Information om Windows VM, diskar eller pooler lagringskonfiguration, prestandaräknare, loggar och olika spårningssessioner samlas in. Det beror på prestanda-scenario som du använder. Följande tabell innehåller information:

|Data som samlas in                              |  |  | Scenarier för prestanda |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Samla in grundläggande konfiguration | Benchmarking | Långsam VM analys | Långsam VM-analys och prestandamätningar | Azure filer analys | Anpassade långsam VM-analys |
| Information från händelseloggar      | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Systeminformation               | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Volymen karta                       | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Disk-karta                         | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Aktiviteter som körs                    | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Lagring tillförlitlighet räknare     | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Storage-informationen              | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Fsutil utdata                    | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Drivrutinen filterinformationen               | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Netstat-utdata                   | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Nätverkskonfiguration            | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Brandväggskonfiguration           | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Konfiguration av SQL Server         | Ja                        | Ja                                | Ja                      | Ja                            | Ja                  | Ja                  |
| Prestanda diagnostik spårningar * | Ja                        | Ja                                | Ja                      |                                | Ja                  | Ja                  |
| Prestandaräknaren trace **     |                            |                                    |                          |                                |                      | Ja                  |
| SMB-räknaren trace **             |                            |                                    |                          |                                | Ja                  |                      |
| SQL Server räknaren trace **      |                            |                                    |                          |                                |                      | Ja                  |
| XPerf spårning                      |                            |                                    |                          |                                |                      | Ja                  |
| StorPort-spårning                   |                            |                                    |                          |                                |                      | Ja                  |
| Spårning i nätverket                    |                            |                                    |                          |                                | Ja                  | Ja                  |
| Diskspd benchmark trace ***      |                            | Ja                                |                          | Ja                            |                      |                      |
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
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>För Azure-filer
\SMB Klientresurser

### <a name="diskspd-benchmark-trace-"></a>Diskspd benchmark-spårning (*)
I/o för Diskspd arbetsbelastning prov (OS-Disk [Skriv] och pool enheter [läsning/skrivning])

## <a name="run-the-perfinsights-script-on-your-vm"></a>Kör skriptet PerfInsights på den virtuella datorn

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Vad måste jag känna till innan jag köra skriptet? 

#### <a name="script-requirements"></a>Skriptkrav för

-  Det här skriptet måste köras på den virtuella datorn som har prestandaproblem. 

-  Har stöd för följande operativsystem: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016; Windows 8.1 och Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>Möjliga problem när du kör skriptet på produktion virtuella datorer

-  För alla scenarier för benchmarking eller ”anpassad långsam VM analys” scenariot som är konfigurerad för att använda Xperf eller Diskspd kan skriptet påverka prestanda negativt för den virtuella datorn. Du bör inte köra dessa scenarier i en produktionsmiljö.

-  Se till att inga andra bakgrundsaktivitet stör i/o-arbetsbelastning för alla scenarier för benchmarking eller ”anpassad långsam VM analys”-scenario som är konfigurerad för att använda Diskspd.

-  Som standard använder skriptet enhetens tillfällig lagring för att samla in data. Om spårning förblir aktiverad under en längre tid, vara mängden data som samlas in relevanta. Detta kan minska tillgängligheten för utrymmet på den tillfälliga disken och kan därför påverka alla program som förlitar sig på den här enheten.

### <a name="how-do-i-run-perfinsights"></a>Hur kör PerfInsights? 

Du kan köra PerfInsights på en virtuell dator genom att installera [Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md). Du kan också köras som ett fristående skript. 

**Installera och köra PerfInsights från Azure-portalen**

Mer information om det här alternativet finns [installera Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md#install-the-extension).  

**Kör PerfInsights skript i fristående läge**

Följ dessa steg om du vill köra skriptet PerfInsights:


1. Hämta [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Avblockera PerfInsights.zip-filen. Gör du genom att högerklicka på filen PerfInsights.zip och välj **egenskaper**. I den **allmänna** väljer **avblockera**, och välj sedan **OK**. Detta säkerställer att skriptet körs utan några ytterligare säkerhetsmeddelanden.  

    ![Skärmbild av PerfInsights egenskaper, med avblockera markerat](media/how-to-use-perfInsights/unlock-file.png)

3.  Expandera den komprimerade filen PerfInsights.zip i tillfälliga enheten (som standard, detta är vanligtvis enhet D). Den komprimerade filen bör innehålla följande filer och mappar:

    ![Skärmbild av filer i mappen zip](media/how-to-use-perfInsights/file-folder.png)

4.  Öppna Windows PowerShell som administratör och kör sedan skriptet PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Du kan behöva ange ”y” för att bekräfta att du vill ändra körningsprincipen.

    I den **meddelande och godkännande** dialogrutan har du möjlighet att dela diagnostikinformation med Microsoft-supporten. Du måste också accepterar du licensavtalet för att fortsätta. Gör dina val och välj sedan **kör skriptet**.

    ![Skärmbild av meddelande och samtycke dialogrutan](media/how-to-use-perfInsights/disclaimer.png)

5.  Skicka case siffra, om den är tillgänglig när du kör skriptet. Välj sedan **OK**.
    
    ![Skärmbild av dialogrutan för support-ID](media/how-to-use-perfInsights/enter-support-number.png)

6.  Välj enheten tillfällig lagring. Skriptet kan automatisk identifiering enhetsbeteckningen för enheten. Om problem uppstår i det här steget kan uppmanas du att välja enheten (standardenheten D). Genererade loggar lagras här, i loggen\_samlingsmapp. När du anger eller acceptera enhetsbeteckningen markerar **OK**.

    ![Skärmbild av dialogrutan tillfälliga enhet](media/how-to-use-perfInsights/enter-drive.png)

7.  Välj ett scenario med felsökning i listan.

       ![Skärmbild av felsökning scenarier lista](media/how-to-use-perfInsights/select-scenarios.png)

Du kan också köra PerfInsights utan användargränssnitt. Följande kommando kör ”långsam VM analysen” felsökning scenariot utan användargränssnitt. Ombeds du samtycker till att samma friskrivning och LICENSAVTALET som nämns i steg 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Om du vill PerfInsights körs i tyst läge använder den **- AcceptDisclaimerAndShareDiagnostics** parameter. Till exempel använda följande kommando:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Hur felsöker problem när skriptet kördes?

Om skriptet avslutas onormalt, kan du återgå till ett konsekvent tillstånd genom att köra skriptet tillsammans med växeln rensning på följande sätt:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Om problem uppstår under automatisk identifiering av den tillfälliga enheten, kan du uppmanas att välja enheten (standardenheten D).

![Skärmbild av dialogrutan tillfälliga enhet](media/how-to-use-perfInsights/enter-drive.png)

Skriptet avinstallerar verktyg och tar bort temporära mappar.

### <a name="troubleshoot-other-script-issues"></a>Felsökning av problem med andra skript 

Tryck på Ctrl + C för att avbryta skriptet om eventuella problem uppstår när du kör skriptet. Om du fortfarande har skriptfelet trots flera försök att köra skriptet i felsökningsläge med hjälp av den ”-Debug” parametern alternativet vid start.

När felet inträffade, kopiera fullständig utdata från PowerShell-konsolen och skicka den till Microsoft Support-agent som hjälper dig att felsöka problemet.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Hur kör skriptet i ”anpassade långsam VM analys” läge?

Genom att välja den **anpassad långsam VM analysis**, du kan aktivera flera spårningar parallellt (om du vill välja flera spårningar använder SKIFT):

![Skärmbild av lista över scenarier](media/how-to-use-perfInsights/select-scenario.png)

När du väljer prestandaräknaren spårningen Följ Xperf spårning, spårning i nätverket eller Storport Trace scenarier, instruktionerna i efterföljande dialogrutor. Försök att återskapa problemet med långsamma prestanda när du har startat spåren.

Du startar en stega igenom följande dialogruta:

![Skärmbild av starta prestandaräknaren Trace dialogrutan](media/how-to-use-perfInsights/start-trace-message.png)

Du måste bekräfta kommandot i en andra dialogruta för att stoppa spårningen.

![Skärmbild av stoppar prestandaräknaren Trace dialogrutan](media/how-to-use-perfInsights/stop-trace-message.png)
![Skärmbild av stoppar alla spårar dialogrutan](media/how-to-use-perfInsights/ok-trace-message.png)

När spårningssessioner eller åtgärder har slutförts, visas en ny fil i D:\\loggen\_samling (eller den tillfälliga enheten). Namnet på filen är **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip.** Du kan skicka den här filen till supportpersonalen för analys.

## <a name="review-the-diagnostics-report"></a>Granska rapporten diagnostik

I den **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip** fil, du kan söka efter en HTML-rapport som beskriver resultaten av PerfInsights. För att granska rapporten, expandera den **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip** filen och öppna den **PerfInsights Report.html** fil.

Välj den **resultaten** fliken.

![Skärmbild av PerfInsights rapporten](media/how-to-use-perfInsights/findingtab.png)
![Skärmbild av PerfInsights rapport](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Resultaten kategoriserade som kritiskt är kända problem som kan orsaka prestandaproblem. Resultaten kategoriserade som representerar viktiga icke-optimal konfigurationer som inte nödvändigtvis orsakar prestandaproblem. Resultaten kategoriserade som informationsmeddelande är informativa instruktioner.

Granska rekommendationer och länkar för alla kritiska och viktiga resultaten. Lär dig mer om hur de kan påverka prestanda och även om bästa praxis för prestanda-optimerad konfigurationer.

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

### <a name="xperf"></a>Xperf

XPerf är ett kommandoradsverktyg för att fånga in spårningar från Windows Performance Toolkit. Mer information finns i [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Nästa steg

Du kan överföra diagnostik loggar och rapporter till Microsoft Support för ytterligare granskning. Support kan begära att du överför de utdata som genereras av PerfInsights för att underlätta felsökningen.

Följande skärmbild visar ett meddelande som liknar kan du få:

![Skärmbild av exempelmeddelande från Microsoft Support](media/how-to-use-perfInsights/supportemail.png)

Följ instruktionerna i meddelandet för att komma åt filen överföring arbetsytan. För ytterligare säkerhet som du behöver ändra ditt lösenord vid första användningen.

När du har loggat in hittar du en dialogruta för att överföra den **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip** filen som samlats in av PerfInsights.
