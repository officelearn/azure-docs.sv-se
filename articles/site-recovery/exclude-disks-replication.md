---
title: Exkludera diskar från replikering med Azure Site Recovery
description: Så här utesluter du diskar från replikering till Azure med Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281852"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Exkludera diskar från haveriberedskap

I den här artikeln beskrivs hur du utesluter diskar från replikering vid haveriberedskap från lokalt till Azure med [Azure Site Recovery](site-recovery-overview.md). Du kan utesluta diskar från replikering av flera skäl:

- Så att oviktiga data som spottas på den uteslutna disken inte replikeras.
- Så här optimerar du förbrukad replikeringsbandbredd eller målresurser.
- Så här sparar du lagrings- och nätverksresurser genom att inte replikera data som du inte behöver.
- Virtuella Azure-datorer har nått replikeringsgränser för webbplatsåterställning.


## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan utesluta diskar från replikering som sammanfattas i tabellen.

**Azure till Azure** | **VMware till Azure** | **Hyper-V till Azure** 
--- | --- | ---
Ja (med PowerShell) | Ja | Ja 

## <a name="exclude-limitations"></a>Exkludera begränsningar

**Begränsning** | **Virtuella Azure-datorer** | **VMwares virtuella datorer** | **Virtuella Hyper-V-datorer**
--- | --- | ---
**Disktyper** | Du kan utesluta grundläggande diskar från replikering.<br/><br/> Du kan inte utesluta operativsystemdiskar eller dynamiska diskar. Temp-diskar är undantagna som standard. | Du kan utesluta grundläggande diskar från replikering.<br/><br/> Du kan inte utesluta operativsystemdiskar eller dynamiska diskar. | Du kan utesluta grundläggande diskar från replikering.<br/><br/> Du kan inte undanta operativsystemdiskar. Vi rekommenderar att du inte undantar dynamiska diskar. Site Recovery kan inte identifiera vilken VHS är grundläggande eller dynamisk i gästdatorn. Om alla beroende dynamiska volymdiskar inte är undantagna blir den skyddade dynamiska disken en misslyckad disk på en redundans-VM och data på disken är inte tillgängliga.
**Replikera disk** | Du kan inte utesluta en disk som replikerar.<br/><br/> Inaktivera och återaktivera replikering för den virtuella datorn. |  Du kan inte utesluta en disk som replikerar. |  Du kan inte utesluta en disk som replikerar.
**Mobilitetstjänst (VMware)** | Inte relevanta | Du kan utesluta diskar endast på virtuella datorer som har mobilitetstjänsten installerad.<br/><br/> Det innebär att du manuellt måste installera mobilitetstjänsten på de virtuella datorer som du vill utesluta diskar för. Du kan inte använda push-installationsmekanismen eftersom den installerar mobilitetstjänsten först när replikeringen är aktiverad. | Inte relevant.
**Lägg till/ta bort** | Du kan lägga till och ta bort diskar på virtuella Azure-datorer med hanterade diskar. | Du kan inte lägga till eller ta bort diskar när replikering har aktiverats. Inaktivera och sedan återaktivera replikering för att lägga till en disk. | Du kan inte lägga till eller ta bort diskar när replikering har aktiverats. Inaktivera och sedan återaktivera replikering.
**Redundans** | Om en app behöver en disk som du har uteslutit måste du skapa disken manuellt efter redundansen så att den replikerade appen kan köras.<br/><br/> Alternativt kan du skapa disken under vm-redundans genom att integrera Azure-automatisering i en återställningsplan. | Om du utesluter en disk som en app behöver skapar du den manuellt i Azure efter redundans. | Om du utesluter en disk som en app behöver skapar du den manuellt i Azure efter redundans.
**Lokala återställningsdiskar som skapats manuellt** | Inte relevanta | **Windows virtuella datorer:** Diskar som skapats manuellt i Azure har inte misslyckats tillbaka. Om du till exempel växlar över tre diskar och skapar två diskar direkt på en Virtuell Azure- -dator, misslyckades endast de tre diskarna som misslyckades över tillbaka.<br/><br/> **Virtuella Linux-datorer:** Diskar som skapats manuellt i Azure har misslyckats tillbaka. Om du till exempel växlar över tre diskar och skapar två diskar på en Virtuell Azure- -så misslyckas alla fem tillbaka. Du kan inte undanta diskar som har skapats manuellt från återställningen. | Diskar som skapats manuellt i Azure har inte misslyckats tillbaka. Om du till exempel växlar över tre diskar och skapar två diskar direkt på en Virtuell Azure- -dator, kommer endast tre diskar som misslyckades över att återställas.
**Lokala återställningsdiskar för återställning av fel i lokal tid** | Inte relevanta | Om du växlar tillbaka till den ursprungliga datorn innehåller den virtuella diskkonfigurationen för återställning inte de uteslutna diskarna. Diskar som har uteslutits från VMware till Azure-replikering är inte tillgängliga på den virtuella återställningsdatorn. | När återställning efter fel är till den ursprungliga Hyper-V-platsen förblir vm-diskkonfigurationen för återställning samma som för den ursprungliga vm-disken. Diskar som exkluderades från Hyper-V-plats till Azure-replikering är tillgängliga på den virtuella återställningsdatorn.



## <a name="typical-scenarios"></a>Typiska scenarier

Exempel på dataomsättning som är bra kandidater för uteslutning är skrivningar till en växlingsfil (pagefile.sys) och skriver till tempdb-filen i Microsoft SQL Server. Beroende på arbetsbelastningen och lagringsundersystemet kan växlings- och tempdb-filerna registrera en betydande mängd omsättning. Att replikera den här typen av data till Azure är resurskrävande.

- Om du vill optimera replikeringen för en virtuell dator med en enda virtuell disk som innehåller både operativsystemet och växlingsfilen kan du:
    1. Dela upp den enda virtuella hårddisken på två virtuella diskar. En virtuell disk har operativsystemet och den andra har växlingsfilen.
    2. Undanta växlingsfilen från replikering.

- Om du vill optimera replikeringen för en disk som innehåller både Tempdb-filen i Microsoft SQL Server och systemdatabasfilen kan du:
    1. Förvara systemdatabasen och tempdb på två olika diskar.
    2. Undanta tempdb-disken från replikeringen.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exempel 1: Undanta en SQL Server tempdb-disk

Låt oss titta på hur man hanterar diskuteslutning, redundans och redundans för en källa SQL Server Windows VM - ** SalesDB ***, som vi vill utesluta tempdb. 

### <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

Vi har dessa diskar på källan Windows VM SalesDB.

**Disknamn** | **Gäst-OS-disk** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativsystem disk.
DB-Disk1| Disk1 | D:\ | SQL-systemdatabas och användardatabas1.
DB-Disk2 (disken har undantagits från skydd) | Disk2 | E:\ | Temp filer.
DB-Disk3 (disken har undantagits från skydd) | Disk3 | F:\ | SQL tempdb databas.<br/><br/> Mappsökväg - F:\MSSQL\Data\. Anteckna mappsökvägen före redundans.
DB-Disk4 | Disk4 |G:\ | Användardatabas2

1. Vi aktiverar replikering för den virtuella virtuella salesdb-datorn.
2. Vi utesluter Disk2 och Disk3 från replikering eftersom dataomsättning på dessa diskar är tillfällig. 


### <a name="handle-disks-during-failover"></a>Hantera diskar under redundans

Eftersom diskar inte replikeras, när du växlar över till Azure dessa diskar inte finns på Azure VM som skapats efter redundans. Den virtuella Azure-datorn har diskarna sammanfattade i den här tabellen.

**Gäst-OS-disk** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | ---
Disk0 | C:\ | Operativsystem disk.
Disk1 | E:\ | Tillfällig lagring<br/><br/>Azure lägger till den här disken. Eftersom Disk2 och Disk3 uteslöts från replikering är E: den första enhetsbeteckningen från den tillgängliga listan. Azure tilldelar E: till den tillfälliga lagringsvolymen. Andra enhetsbeteckningar för replikerade diskar förblir desamma.
Disk2 | D:\ | SQL-systemdatabas och användardatabas1
Disk3 | G:\ | Användardatabas2

I vårt exempel, eftersom Disk3, SQL tempdb-disken, uteslöts från replikering och inte är tillgänglig på Azure VM, sql-tjänsten är i ett stoppat tillstånd och den behöver sökvägen F:\MSSQL\Data. Du kan skapa den här sökvägen på ett par olika sätt: 

- Lägg till en ny disk efter redundans och tilldela sökvägen till tempdb-mapp.
- Använda den befintliga tillfälliga lagringsdisken för tempdb-mappsökvägen.

#### <a name="add-a-new-disk-after-failover"></a>Lägga till en ny disk efter redundans

1. Notera sökvägarna till SQL tempdb.mdf och tempdb.ldf före redundansväxlingen.
2. Lägg till en ny disk i redundans-Azure VM från Azure-portalen. Disken ska ha samma storlek (eller större) som källan SQL tempdb disk (Disk3).
3. Logga in på den virtuella Azure-datorn.
4. Initiera och formatera den nytillagda disken från diskhanteringskonsolen (diskmgmt.msc).
5. Tilldela samma enhetsbeteckning som användes av SQL tempdb-disken (F:)
6. Skapa en tempdb-mapp på volym F: (F:\MSSQL\Data).
7. Starta SQL-tjänsten från tjänstkonsolen.

#### <a name="use-an-existing-temporary-storage-disk"></a>Använda en befintlig tillfällig lagringsdisk 

1. Öppna en kommandotolk.
2. Kör SQL Server i återställningsläge från kommandotolken.

        Net start MSSQLSERVER /f / T3608

3. Kör följande sqlcmd för att ändra tempdb-sökvägen till en ny sökväg.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Stoppa Microsoft SQL Server-tjänsten.

        Net stop MSSQLSERVER
5. Starta Microsoft SQL Server-tjänsten.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Virtuella datorer med VMware: Diskar under återställning till ursprunglig plats

Nu ska vi se hur du hanterar diskar på virtuella datorer med VMware när du växlar tillbaka till din ursprungliga lokala plats.

- **Diskar som skapats i Azure:** Eftersom vårt exempel använder en Windows-virtuell dator replikeras inte diskar som du skapar manuellt i Azure tillbaka till din webbplats när du misslyckas med att återställa eller rotera en virtuell dator igen.
- **Tillfällig lagringsdisk i Azure**: Den tillfälliga lagringsdisken replikeras inte tillbaka till lokala värdar.
- **Uteslutna diskar**: Diskar som exkluderades från VMware till Azure-replikering är inte tillgängliga på den lokala virtuella datorn efter återställning efter återställning efter återställning.

Innan du misslyckas med att återställa virtuella datorer med VMware till den ursprungliga platsen är azure VM-diskinställningarna följande.

**Gäst-OS-disk** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | ---
Disk0 | C:\ | Operativsystem disk.
Disk1 | E:\ | Tillfällig lagring.
Disk2 | D:\ | SQL-systemdatabas och användardatabas1.
Disk3 | G:\ | Användardatabas2.

Efter återställning efter återställning av fel har virtuella VMware-datorer på den ursprungliga platsen diskarna summeras i tabellen.

**Gäst-OS-disk** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | ---
Disk0 | C:\ | Operativsystem disk.
Disk1 | D:\ | SQL-systemdatabas och användardatabas1.
Disk2 | G:\ | Användardatabas2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Virtuella datorer med hyper-V: Diskar under återställning till ursprunglig plats

Nu ska vi se hur du hanterar diskar på virtuella hyper-virtuella datorer när du växlar tillbaka till din ursprungliga lokala plats.

- **Diskar som skapats i Azure:** Diskar som du skapar manuellt i Azure replikeras inte tillbaka till din webbplats när du misslyckas tillbaka eller återrotect en virtuell dator.
- **Tillfällig lagringsdisk i Azure**: Den tillfälliga lagringsdisken replikeras inte tillbaka till lokala värdar.
- **Uteslutna diskar:** Efter återställning av fel på den virtuella datorn är vm-diskkonfigurationen samma som den ursprungliga VM-diskkonfigurationen. Diskar som exkluderades från replikering från Hyper-V till Azure är tillgängliga på den virtuella återställningsdatorn.

Innan du misslyckas med att återställa de virtuella datorerna med hyper-V till den ursprungliga platsen är azure VM-diskinställningarna följande.

**Gäst-OS-disk** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | ---
Disk0 | C:\ | Operativsystem disk.
Disk1 | E:\ | Tillfällig lagring.
Disk2 | D:\ | SQL-systemdatabas och användardatabas1.
Disk3 | G:\ | Användardatabas2.

Efter planerad redundans (redundans) från Azure till lokalt Hyper-V, har Hyper-V-virtuella datorn på den ursprungliga platsen diskarna summerade i tabellen.

**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp för disk**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Operativsystem disk.
DB-Disk1 | Disk1 | D:\ | SQL-systemdatabas och användardatabas1.
DB-Disk2 (utesluten disk) | Disk2 | E:\ | Temp filer.
DB-Disk3 (utesluten disk) | Disk3 | F:\ | SQL tempdb-databas<br/><br/> Mappsökväg (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | Användardatabas2


## <a name="example-2-exclude-the-paging-file-disk"></a>Exempel 2: Exkludering av växlingsfildisken

Låt oss titta på hur du hanterar diskuteslutning, redundans och redundans för en källa Windows VM, som vi vill utesluta pagefile.sys-fildisken på både D-enheten och en alternativ enhet.


### <a name="paging-file-on-the-d-drive"></a>Växlingsfil på D-enheten

Vi har dessa diskar på källan VM.

**Disknamn** | **Gäst-OS-disk** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativsystemdisk
DB-Disk1 (Uteslut från replikering) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Våra växlingsfilinställningar på källdatorns virtuella dator är följande:

![Inställningar för växlingsfiler på den virtuella källdatorn](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Vi aktiverar replikering för den virtuella datorn.
2. Vi utesluter DB-Disk1 från replikering.

#### <a name="disks-after-failover"></a>Diskar efter redundans

Efter redundans azure VM har diskarna sammanfattas i tabellen.

**Disknamn** | **Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativsystemdisk
DB-Disk1 | Disk1 | D:\ | Tillfälligt lagringsutrymme/pagefile.sys <br/><br/> Eftersom DB-Disk1 (D:) uteslöts, D: är den första enhetsbeteckningen från den tillgängliga listan.<br/><br/> Azure tilldelar enhetsbeteckningen D: till den tillfälliga lagringsvolymen.<br/><br/> Eftersom D: är tillgänglig förblir inställningen för växling av virtuella datorer samma).
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Våra växlingsfilinställningar på Den virtuella Azure-datorn är följande:

![Inställningar för växlingsfiler på den virtuella Azure-datorn](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Växlingsfil på en annan enhet (inte D:)

Låt oss titta på exempel där växlingsfilen inte finns på D-enheten.  

Vi har dessa diskar på källan VM.

**Disknamn** | **Gäst-OS-disk** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativsystemdisk
DB-Disk1 (Uteslut från replikering) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Våra växlingsfilinställningar på den lokala virtuella datorn är följande:

![Inställningar för växlingsfiler på den lokala virtuella datorn](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Vi aktiverar replikering för den virtuella datorn.
2. Vi utesluter DB-Disk1 från replikering.

#### <a name="disks-after-failover"></a>Diskar efter redundans

Efter redundans azure VM har diskarna sammanfattas i tabellen.

**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp för disk**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Operativsystemdisk
DB-Disk1 | Disk1 | D:\ | Tillfällig lagring<br/><br/> Eftersom D: är den första tillgängliga enhetsbeteckningen i listan tilldelar Azure enhetsbeteckningen D: till den tillfälliga lagringsvolymen.<br/><br/> Alla replikerade diskar har kvar samma enhetsbeteckning.<br/><br/> Eftersom G:-disken inte är tillgänglig använder systemet C:-enheten för växlingsfilen.
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Våra växlingsfilinställningar på Den virtuella Azure-datorn är följande:

![Inställningar för växlingsfiler på den virtuella Azure-datorn](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Nästa steg

- Läs mer om riktlinjer för den tillfälliga lagringsdisken:
    - [Lär dig mer om](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) hur du använder SSD:er i virtuella Azure-datorer för att lagra SQL Server TempDB- och buffertpooltillägg
    - [Granska](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) metodtips för prestanda för SQL Server i virtuella Azure-datorer.
- När du har konfigurerat och fått igång distributionen kan du [läsa mer](failover-failback-overview.md) om olika typer av redundansväxlingar.

