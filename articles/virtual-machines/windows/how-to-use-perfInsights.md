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
ms.openlocfilehash: bb4c21456643532df040df4fcd5f4fa1a4f48d2c
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-perfinsights"></a>Hur du använder PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) är ett automatiserat skript som samlar in viktig diagnostisk information, körs i/o-belastning stress och ger en analysrapport för att felsöka Windows VM prestandaproblem i Microsoft Azure. Det kan köras på virtuella datorer som en fristående skript eller direkt från portalen genom att installera [Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md).

Vi rekommenderar att du kör det här skriptet innan du skapar ett supportärende hos Microsoft VM prestandaproblem.

## <a name="supported-troubleshooting-scenarios"></a>Felsökning scenarier som stöds

PerfInsights kan samla in och analysera flera typer av information som är grupperade i unika scenarier.

### <a name="collect-basic-configuration"></a>Samla in grundläggande konfiguration 

Det här scenariot samlar in diskkonfigurationen och annan viktig information, inklusive följande:

-   Händelseloggar

-   Nätverkets status för alla inkommande och utgående anslutningar

-   Inställningarna för nätverk och brandvägg

-   Uppgiftslista för alla program som körs på systemet

-   Informationsfilen som skapas av msinfo32 för den virtuella datorn (VM)

-   Microsoft SQL Server-databasen-konfigurationsinställningarna (om den virtuella datorn identifieras som en server som kör SQL Server)

-   Lagring tillförlitlighet räknare

-   Viktiga snabbkorrigeringar för Windows

-   Installerade filterdrivrutiner

Detta är en passiv insamling av information som inte bör påverka systemet. 

>[!Note]
>Det här scenariot ingår automatiskt i var och en av följande scenarier.

### <a name="benchmarking"></a>Prestandamätningar

Det här scenariot körs den [diskspd](https://github.com/Microsoft/diskspd) benchmark-test (IOPS och Mbit/s) för alla enheter som är kopplade till den virtuella datorn. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test att sänka prestandan för den virtuella datorn.
>

### <a name="slow-vm-analysis"></a>Långsam VM analys 

Det här scenariot körs en [prestandaräknaren](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) spårningen med hjälp av de räknare som anges i filen Generalcounters.txt. Om den virtuella datorn identifieras som en server som kör SQL Server, kör en räknare spårning av prestanda med hjälp av de räknare som finns i filen Sqlcounters.txt. Den omfattar också prestanda diagnostikdata.

### <a name="slow-vm-analysis-and-benchmarking"></a>Långsam VM-analys och prestandamätningar

Det här scenariot körs en [prestandaräknaren](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) spårning som följs av en [diskspd](https://github.com/Microsoft/diskspd) benchmark-test. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test att sänka prestandan för den virtuella datorn.
>

### <a name="azure-files-analysis"></a>Azure filer analys 

Det här scenariot körs en särskild prestandaräknaren avbildning tillsammans med en spårning i nätverket. Avbildningen innehåller räknarna för ”SMB-Klientresurser”. Här följer några viktiga SMB-resursen prestandaräknare i klient som ingår i avbildningen:

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

### <a name="custom-slow-vm-analysis"></a>Anpassade långsam VM-analys 

När du kör en anpassad långsam VM analys kör du alla spår (prestandaräknaren, xperf, nätverk, storport) parallellt, beroende på hur många olika spår har valts. När spårning är klar kör verktyget diskspd-prestandamått, om den är markerad. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras på ett levande produktionssystem. Kör det här scenariot i en dedikerad underhållsfönstret för att undvika problem vid behov. En ökad belastning som orsakas av ett spårningen eller prestandamått test att sänka prestandan för den virtuella datorn.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Vilken typ av information som samlas in av skriptet?

Information om Windows VM, diskar eller pooler lagringskonfiguration, prestandaräknare, loggar och olika spårningssessioner samlas in, beroende på scenario prestanda används:

|Data som samlas in                              |  |  | Scenarier för prestanda |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Samla in grundläggande konfiguration | Prestandamätningar | Långsam VM analys | Långsam VM-analys och prestandamätningar | Azure filer analys | Anpassade långsam VM-analys |
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
| Prestandaräknaren Trace **     |                            |                                    |                          |                                |                      | Ja                  |
| SMB-räknaren Trace **             |                            |                                    |                          |                                | Ja                  |                      |
| SQL Server-räknaren Trace **      |                            |                                    |                          |                                |                      | Ja                  |
| XPerf spårning                      |                            |                                    |                          |                                |                      | Ja                  |
| StorPort-spårning                   |                            |                                    |                          |                                |                      | Ja                  |
| Spårning i nätverket                    |                            |                                    |                          |                                | Ja                  | Ja                  |
| Diskspd Benchmark trace ***      |                            | Ja                                |                          | Ja                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Prestanda diagnostik trace (*)

Kör en regelbaserad motor i bakgrunden för att samla in data och diagnostisera pågående prestandaproblem. Följande regler stöds:

- HighCpuUsage regel: identifierar perioder med hög CPU-användning och visar de högsta CPU användning konsumenterna under dessa punkter.
- HighDiskUsage regel: identifierar ett högt användning punkter på fysiska diskar och visar högsta disk användning konsumenter under dessa punkter.
- HighResolutionDiskMetric regel: Visar IOPS, genomflöde och IO latens mått per 50 tid i millisekunder för varje fysisk disk. Det hjälper till att snabbt identifiera disken begränsning punkter.
- HighMemoryUsage regel: identifierar höga minnesområdet användning punkter och visar det översta minnet användning konsumenter under dessa punkter.

> [!NOTE] 
> För närvarande stöds Windows-versioner med .NET Framework 3.5 eller senare versioner.

### <a name="performance-counter-trace-"></a>Prestandaräknaren trace (*)

Samlar in följande prestandaräknare:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk, \LogicalDisk
- \Cache\Dirty sidor, \Cache\Lazy tömningar/s, \Server\Pool växlingsbart systemminne, fel, \Server\Pool växlingsbart systemminne-fel
- Valda räknare under \Network gränssnitt, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network nätverkskort, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Network Interface Card processoraktivitet, \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>För SQL Server-instanser
- \SQL server: bufferthanteraren, \SQLServer:Resource Pool statistik, \SQLServer:SQL Statistics\
- \SQLServer:locks \SQLServer:General statistik
- \SQLServer:Access metoder

#### <a name="for-azure-files"></a>För Azure-filer
\SMB Klientresurser

### <a name="diskspd-benchmark-trace-"></a>Diskspd Benchmark-spårning (*)
Diskspd-i/o arbetsbelastning tester [OS-disken (write) och pool-enheter (läsa/skriva)]

## <a name="run-the-perfinsights-on-your-vm"></a>Kör PerfInsights på den virtuella datorn

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Vad måste jag känna till innan jag köra skriptet? 

**Skriptkrav för**

1.  Det här skriptet måste köras på den virtuella datorn som har prestandaproblem. 

2.  Har stöd för följande operativsystem: Windows Server 2008 R2, 2012, 2012 R2, 2016; Windows 8.1 och Windows 10.

**Möjliga problem när du kör skriptet på produktion virtuella datorer:**

1.  När du använder någon Benchmarking scenarier eller ”anpassad långsam VM analys” scenario som är konfigurerad för att använda XPerf eller DiskSpd kan skriptet påverka prestanda negativt för den virtuella datorn. Det rekommenderas inte att köra dessa scenarier i en produktionsmiljö utan övervakning av en CSS-tekniker.

2.  När du använder någon Benchmarking scenarier eller ”anpassad långsam VM analys” scenario som är konfigurerad för att använda DiskSpd, se till att inga andra bakgrundsaktivitet stör i/o-arbetsbelastning på testade diskarna.

3.  Som standard använder skriptet enhetens tillfällig lagring för att samla in data. Om spårning förblir aktiverad under en längre tid, vara mängden data som samlas in relevanta. Detta kan minska tillgängligheten för utrymmet på den tillfälliga disken därför påverkar alla program som förlitar sig på den här enheten.

### <a name="how-do-i-run-perfinsights"></a>Hur kör PerfInsights? 

Du kan köra PerfInsights på en virtuell dator genom att installera [Azure prestanda diagnostik VM-tillägget](performance-diagnostics-vm-extension.md) eller köra den som ett fristående skript. 

**Installera och köra PerfInsights från Azure-portalen**

PerfInsights kan nu köras med hjälp av en VM-tillägg som kallas Azure prestanda diagnostik tillägg. Mer information finns i [installera Azure Prestandadiagnostik tillägget](performance-diagnostics-vm-extension.md#install-the-extension).  

**Kör PerfInsights skript i fristående läge**

Följ dessa steg om du vill köra skriptet PerfInsights:


1. Hämta [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Avblockera PerfInsights.zip-filen. Gör du genom att högerklicka på filen PerfInsights.zip, Välj **egenskaper**. I den **allmänna** väljer **avblockera** och välj sedan **OK**. Detta säkerställer att skriptet körs utan några ytterligare säkerhetsmeddelanden.  

    ![Låsa upp zip-filen](media/how-to-use-perfInsights/unlock-file.png)

3.  Expandera den komprimerade filen PerfInsights.zip i tillfälliga enheten (som standard, normalt enhet D). Den komprimerade filen bör innehålla följande filer och mappar:

    ![filerna i mappen zip](media/how-to-use-perfInsights/file-folder.png)

4.  Öppna Windows PowerShell som administratör och kör sedan skriptet PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Du kan behöva ange ”y” om du uppmanas att bekräfta att du vill ändra körningsprincipen.

    I dialogrutan friskrivning möjlighet du att dela diagnostikinformation med Microsoft-supporten. Du måste också accepterar du licensavtalet för att fortsätta. Gör dina val och klicka sedan på **kör skriptet**.

    ![FRISKRIVNING rutan](media/how-to-use-perfInsights/disclaimer.png)

5.  Skicka case siffra, om den är tillgänglig när du kör skriptet (vilket är för våra statistik). Klicka på **OK**.
    
    ![Ange support-ID](media/how-to-use-perfInsights/enter-support-number.png)

6.  Välj enheten tillfällig lagring. Skriptet kan automatisk identifiering enhetsbeteckningen för enheten. Om problem uppstår i det här steget kan uppmanas du att välja enheten (standardenheten D). Genererade loggar lagras här i loggen\_samlingsmapp. När du anger eller acceptera enhetsbeteckningen klickar du på **OK**.

    ![Ange enhet](media/how-to-use-perfInsights/enter-drive.png)

7.  Välj ett scenario med felsökning i listan.

       ![Välj supportscenarier](media/how-to-use-perfInsights/select-scenarios.png)

8.  Du kan också köra PerfInsights utan användargränssnitt.

    Följande kommando körs ”långsam VM analysen” felsökning scenariot utan en UI-kommandotolk eller samla in data i 30 sekunder. Ombeds du samtycker till att samma friskrivning och LICENSAVTALET som nämns i steg 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    Om du vill PerfInsights körs i tyst läge använder den **- AcceptDisclaimerAndShareDiagnostics** parameter. Till exempel använda följande kommando:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Hur felsöker problem när skriptet kördes?

Om skriptet avslutas onormalt, kan du rensa ett inkonsekvent tillstånd genom att köra skriptet tillsammans med växeln-rensning, enligt följande:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Om problem uppstår under automatisk identifiering av den tillfälliga enheten, kan du uppmanas att välja enheten (standardenheten D).

![Ange enhet](media/how-to-use-perfInsights/enter-drive.png)

Skriptet avinstallerar verktyg och tar bort temporära mappar.

### <a name="troubleshooting-other-script-issues"></a>Felsökning av problem med andra skript 

Om problem uppstår när du kör skriptet, tryck på Ctrl + C för att avbryta körningen av skriptet. Se avsnittet ”Rensa när onormala avslutades” för att ta bort temporära objekt.

Om du fortfarande har skriptfelet trots flera försök, rekommenderar vi att du kör skriptet i ”felsökningsläge” med hjälp av den ”-Debug” parametern alternativet vid start.

När felet inträffade, kopiera fullständig utdata från PowerShell-konsolen och skicka den till Microsoft Support-agent som hjälper dig att felsöka problemet.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Hur kör skriptet i anpassade långsam VM analys läge?

Genom att välja den **anpassad långsam VM analysis**, du kan aktivera flera spårningar parallellt (Använd skifte till flerval):

![Välj scenarier](media/how-to-use-perfInsights/select-scenario.png)

När du väljer prestandaräknaren spårningen XPerf spårning, spårning i nätverket eller Storport Trace scenarier, följ instruktionerna i dialogrutorna och försöker återskapa problemet med långsamma prestanda när du har startat spåren.

Följande dialogruta kan du starta en spårning:

![Starta spårning](media/how-to-use-perfInsights/start-trace-message.png)

Du måste bekräfta kommandot i en andra dialogruta för att stoppa spårningen.

![Stoppa spårning](media/how-to-use-perfInsights/stop-trace-message.png)
![Stoppa spårning](media/how-to-use-perfInsights/ok-trace-message.png)

När spårningssessioner eller åtgärder har slutförts, skapas en ny fil i D:\\loggen\_samling (eller den tillfälliga enheten) som heter **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip.** Du kan skicka den här filen till supportpersonalen för analys.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>Granska diagnostik rapporten som skapas av PerfInsights

I den **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip filen** som genereras av PerfInsights, kan du hitta en HTML-rapport som beskriver resultaten av PerfInsights. För att granska rapporten, expandera den **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip** filen och öppna den **PerfInsights Report.html** fil.

Välj den **resultaten** fliken.

![hitta fliken](media/how-to-use-perfInsights/findingtab.png)
![resultat](media/how-to-use-perfInsights/findings.PNG)

**Anteckningar**

-   Resultaten kategoriserade som kritiskt är kända problem som kan orsaka prestandaproblem.

-   Resultaten kategoriserade som representerar viktiga icke-optimal konfigurationer som inte nödvändigtvis orsakar prestandaproblem.

-   Resultaten kategoriserade som informationsmeddelande är informativa instruktioner.

Granska rekommendationer och länkar till alla kritiska och viktiga resultat att få mer detaljerad information om resultaten och hur de kan påverka prestanda eller bästa praxis för prestanda-optimerad konfigurationer.

### <a name="storage-tab"></a>Fliken lagring

Den **resultaten** avsnittet visas olika resultat och rekommendationer om lagring.

Den **DiskMap** och **VolumeMap** avsnitt beskrivs på ett dubbla perspektiv logiska volymer och fysiska diskar som är relaterade till varandra.

I perspektivet fysisk disk (DiskMap) visas i tabell alla logiska volymer som körs på disken. I följande exempel körs PhysicalDrive2 två logiska volymer som har skapats på flera partitioner (J och H):

![datafliken](media/how-to-use-perfInsights/disktab.png)

Med tanke på volymen (*VolumeMap*), tabeller visar alla fysiska diskar under varje logisk volym. Observera att du kan köra en logisk volym på flera fysiska diskar för RAID/dynamiska diskar. I följande exempel *C:\\montera* är en monteringspunkt som konfigurerats som *SpannedDisk* på PhysicalDisks \#2 och \#3:

![fliken volym](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL-fliken

Om målet VM är värd för SQL Server-instanser måste du se ytterligare en flik i rapporten som heter **SQL**:

![SQL-fliken](media/how-to-use-perfInsights/sqltab.png)

Det här avsnittet innehåller en ”resultat”-fliken och ytterligare sub flikarna för varje SQL Server-instanser som finns på den virtuella datorn.

Fliken ”resultat” innehåller en lista över alla SQL relaterade prestandaproblem hitta tillsammans med rekommendationer.

I följande exempel *PhysicalDrive0* (som kör enhet C) visas eftersom både den *modeldev* och *modellog* filerna finns på enhet C och de är av olika typer (till exempel datafilen och transaktionsloggen, respektive):

![LogInfo](media/how-to-use-perfInsights/loginfo.png)

SQL Server-instans-specifika flikarna innehåller ett allmänt avsnitt som innehåller grundläggande information om den valda instansen och ytterligare avsnitt avancerad information, inklusive inställningar, konfigurationer och användaralternativ.

### <a name="diagnostic-tab"></a>Fliken diagnostik
Diagnostiska som innehåller information om högsta CPU, Disk- och konsumenter på rutan under PerfInsights kör. Du kan också hitta annan användbar information, till exempel kritiska korrigeringar att systemet kan sakna, uppgiftslista och viktiga händelser i systemet. 

## <a name="references-to-the-external-tools-used"></a>Referenser till externa verktyg som används

### <a name="diskspd"></a>Diskspd

DISKSPD är ett lagring belastningen generator och prestanda test från Windows och Windows Server och moln serverinfrastruktur engineering team. Mer information finns i [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf är ett kommandoradsverktyg för att fånga in spårningar från Windows Performance Tools Kit.

Mer information finns i [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Nästa steg

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>Överför diagnostik loggar och rapporter till Microsoft Support för ytterligare granskning

När du arbetar med Microsoft Support-personal kan uppmanas du att skicka utdata som genereras av PerfInsights för att underlätta felsökningen.

Support-agenten skapar en arbetsyta DTM åt dig och du får ett e-postmeddelande som innehåller en länk till den [DTM portal (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) och ett unikt användarnamn och lösenord.

Det här meddelandet ska skickas från **CTS Automated diagnostik Services** (ctsadiag@microsoft.com).

![Exempel på meddelandet](media/how-to-use-perfInsights/supportemail.png)

För ytterligare säkerhet kan behöver du ändra ditt lösenord vid första användningen.

När du loggar in på DTM hittar du en dialogruta för att överföra den **CollectedData\_åååå-MM-dd\_hh\_mm\_ss.zip** filen som samlats in av PerfInsights.
