---
title: "Utesluta en disk från skydd med Azure Site Recovery | Microsoft Docs"
description: "I den här artikeln beskrivs varför och hur du undantar VM-diskar från replikering för scenarier med VMware till Azure och Hyper-V till Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2017
ms.author: nisoneji
ms.openlocfilehash: fccbe88e3c0c2b2f3e9958f5f2f27adc017e4d03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering
Den här artikeln beskriver hur du undantar diskar från replikering. Det här undantaget kan optimera replikeringsbandbredden som används eller optimera resurser som används av dessa diskar på målsidan. Funktionen stöds för scenarier med VMware till Azure och Hyper-V till Azure.

## <a name="prerequisites"></a>Krav

Som standard replikeras alla diskar på en dator. Om du vill undanta en disk från replikeringen måste du manuellt installera mobilitetstjänsten på datorn innan du aktiverar replikering om du replikerar från VMware till Azure.


## <a name="why-exclude-disks-from-replication"></a>Varför ska jag undanta en disk från replikering?
Vanliga orsaker till att diskar undantas från replikering:

- Data på den undantagna disken var inte viktiga eller behövde inte replikeras.

- Du kan spara lagrings- och nätverksresurser genom att inte replikera dataomsättningen.

## <a name="what-are-the-typical-scenarios"></a>Vilka är de typiska scenarierna?
Du kan identifiera specifika exempel på dataomsättning som är bra kandidater för uteslutning. Exemplen kan inkludera att skriva till en växlingsfil (pagefile.sys) och skriva till tempdb-filen för Microsoft SQL Server. Beroende på arbetsbelastningen och underlagringssystemet kan växlingsfilen registrera en stor dataomsättning. Men att replikera data från den primära platsen till Azure skulle vara resurskrävande. Du kan därför gå igenom följande steg för att optimera replikeringen för en virtuell dator med en enda virtuell disk som har både operativsystemet och växlingsfilen:

1. Dela upp den enda virtuella hårddisken på två virtuella diskar. En virtuell disk har operativsystemet och den andra har växlingsfilen.
2. Undanta växlingsfilen från replikering.

På samma sätt kan du följa stegen nedan för att optimera en disk som har både Microsoft SQL Server tempdb-filen och systemdatabasfilen:

1. Förvara systemdatabasen och tempdb på två olika diskar.
2. Undanta tempdb-disken från replikeringen.

## <a name="how-to-exclude-disks-from-replication"></a>Hur undantar jag en disk från replikering?

### <a name="vmware-to-azure"></a>VMware till Azure
Följ arbetsflödet i [Aktivera replikering](site-recovery-vmware-to-azure.md) för att skydda en virtuell dator med hjälp av Azure Site Recovery-portalen. I det fjärde steget i arbetsflödet ska du använda kolumnen **DISK TO REPLICATE** (DISK ATT REPLIKERA) för att exkludera diskar från replikering. Som standard är alla diskar markerade för replikering. Avmarkera den disk som du vill undanta från replikeringen och följ stegen för att aktivera replikering.

![Exkludera diskar från replikering och aktivera replikering för VMware till Azure-återställning efter fel](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)


>[!NOTE]
>
> * Du kan endast utesluta diskar som mobilitetstjänsten har installerats på. Du måste installera mobilitetstjänsten manuellt eftersom den endast installeras med push-mekanismen när replikering har aktiverats.
> * Endast standarddiskar kan undantas från replikering. Du kan inte undanta operativsystemdiskar eller dynamiska diskar.
> * När du har aktiverat replikering kan du inte lägga till eller ta bort diskar för replikering. Om du vill lägga till eller undanta en disk måste du inaktivera skyddet för datorn och sedan aktivera det igen.
> * Om du undantar en disk som behövs för att ett program ska fungera efter redundansväxlingen till Azure måste du skapa disken manuellt i Azure så att det replikerade programmet kan köras. Du kan också integrera Azure Automation i en återställningsplan för att skapa disken under en redundansväxling av datorn.
> * Virtuell Windows-dator: Diskar som du skapar manuellt i Azure växlas inte tillbaka igen. Om du till exempel växlar över tre diskar och skapar två diskar direkt i Azure Virtual Machines kommer endast tre diskar som växlades över att växlas tillbaka. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller vars skydd har återaktiverats från en lokal plats till Azure.
> * Virtuell Linux-dator: Diskar som du skapar manuellt i Azure växlas tillbaka igen. Om du till exempel växlar över tre diskar och skapar två diskar direkt i Azure Virtual Machines kommer alla fem diskar att växlas tillbaka. Du kan inte undanta diskar som har skapats manuellt från återställningen.
>

### <a name="hyper-v-to-azure"></a>Hyper-V till Azure
Följ arbetsflödet i [Aktivera replikering](site-recovery-hyper-v-site-to-azure.md) för att skydda en virtuell dator med hjälp av Azure Site Recovery-portalen. I det fjärde steget i arbetsflödet ska du använda kolumnen **DISK TO REPLICATE** (DISK ATT REPLIKERA) för att exkludera diskar från replikering. Som standard är alla diskar markerade för replikering. Avmarkera den disk som du vill undanta från replikeringen och följ stegen för att aktivera replikering.

![Exkludera diskar från replikering och aktivera replikering för Hyper-V till Azure-återställning efter fel](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * Du kan endast undanta standarddiskar från replikering. Du kan inte undanta operativsystemdiskar. Vi rekommenderar att du inte undantar dynamiska diskar. Azure Site Recovery kan inte identifiera vilken virtuell hårddisk (VHD) som är en standarddisk eller dynamisk disk på den virtuella gästdatorn.  Om alla beroende dynamiska volymdiskar inte undantas identifieras en skyddad dynamisk disk som felaktig på den virtuella datorn vid redundansväxlingen och data på disken kan inte nås.
> * När du har aktiverat replikering kan du inte lägga till eller ta bort diskar för replikering. Om du vill lägga till eller undanta en disk måste du inaktivera skyddet för den virtuella datorn och sedan aktivera det igen.
> * Om du undantar en disk som behövs för att ett program ska fungera efter redundansväxlingen till Azure måste du skapa disken manuellt i Azure så att det replikerade programmet kan köras. Du kan också integrera Azure Automation i en återställningsplan för att skapa disken under en redundansväxling av datorn.
> * Diskar som du skapar manuellt i Azure växlas inte tillbaka igen. Om du till exempel växlar över tre diskar och skapar två diskar direkt i Azure Virtual Machines kommer endast tre diskar som växlades över att växlas tillbaka från Azure till Hyper-V. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller omvända replikeringar från Hyper-V till Azure.



## <a name="end-to-end-scenarios-of-exclude-disks"></a>Undanta diskar i olika scenarier från slutpunkt till slutpunkt
Vi beskriver två scenarier för att du ska förstå hur undantag av diskar fungerar:

- SQL Server tempdb-disk
- Växlingsfildisk (pagefile.sys)

### <a name="exclude-the-sql-server-tempdb-disk"></a>Undanta en SQL Server tempdb-disk
Anta att du har en virtuell dator som kör SQL Server och som har en tmpdb som kan undantas.

Namnet på den virtuella disken är SalesDB.

Diskarna på den virtuella källdatorn är följande:


**Disknamn** | **Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operativsystemdisk
DB-Disk1| Disk1 | D:\ | SQL-systemdatabas och användardatabas1
DB-Disk2 (disken har undantagits från skydd) | Disk2 | E:\ | Tillfälliga filer
DB-Disk3 (disken har undantagits från skydd) | Disk3 | F:\ | SQL tempdb-databas (mappsökväg(F:\MSSQL\Data\) </br /> </br />Notera mappsökvägen före redundansväxling.
DB-Disk4 | Disk4 |G:\ |Användardatabas2

Eftersom dataomsättning på två diskar i den virtuella datorn är tillfällig ska du undanta Disk2 och Disk3 från replikering när du skyddar den virtuella SalesDB-datorn. Azure Site Recovery replikerar inte diskarna. Vid redundansväxlingen visas inte dessa diskar på den virtuella datorn som redundansväxlas på Azure.

Diskar på virtuella Azure-datorer efter redundansväxling är följande:

**Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | ---
DISK0 | C:\ | Operativsystemdisk
Disk1 | E:\ | Temporär lagring</br /> </br />Azure lägger till den här disken och tilldelar den första tillgängliga enhetsbeteckningen.
Disk2 | D:\ | SQL-systemdatabas och användardatabas1
Disk3 | G:\ | Användardatabas2

Eftersom Disk2 och Disk 3 undantogs från den virtuella SalesDB-datorn är E: den första tillgängliga enhetsbeteckningen i listan. Azure tilldelar E: till den tillfälliga lagringsvolymen. Alla replikerade diskar har kvar samma enhetsbeteckning.

Disk3, som var SQL tempdb-disken (tempdb-mappsökvägen F:\MSSQL\Data\), undantogs från replikering. Disken är inte tillgänglig på den redundansväxlade virtuella datorn. Därför är SQL-tjänsten i stoppat tillstånd och behöver sökvägen F:\MSSQL\Data.

Det finns två sätt att skapa den här sökvägen:

- Lägga till en ny disk och tilldela tempdb-mappsökvägen.
- Använda den befintliga tillfälliga lagringsdisken för tempdb-mappsökvägen.

#### <a name="add-a-new-disk"></a>Lägga till en ny disk:

1. Notera sökvägarna till SQL tempdb.mdf och tempdb.ldf före redundansväxlingen.
2. Lägg till en ny disk till den virtuella datorn som redundansväxlas med samma eller större storlek än SQL tempdb-disken (Disk3) med hjälp av Azure Portal.
3. Logga in på den virtuella Azure-datorn. Initiera och formatera den nytillagda disken från diskhanteringskonsolen (diskmgmt.msc).
4. Tilldela samma enhetsbeteckning som användes av SQL tempdb-disken (F:).
5. Skapa en tempdb-mapp på volym F: (F:\MSSQL\Data).
6. Starta SQL-tjänsten från tjänstkonsolen.

#### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>Använd den befintliga tillfälliga lagringsdisken för SQL tempdb-mappsökvägen:

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

Se följande Azure-riktlinjer för tillfällig lagringsdisk:

* [Använda SSD:er i virtuella Azure-datorer för att lagra SQL Server TempDB och buffertpooltillägg](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

### <a name="failback-from-azure-to-an-on-premises-host"></a>Redundansväxling (från Azure till lokal värd)
Låt oss se vilka diskar som replikeras när du redundansväxlar från Azure till din lokala VMware- eller Hyper-V-värd. De diskar som du skapar manuellt i Azure replikeras inte. Om du till exempel växlar över tre diskar och skapar två diskar direkt i Azure Virtual Machines kommer endast tre diskar som växlades över att växlas tillbaka. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller vars skydd har återaktiverats från en lokal plats till Azure. Den tillfälliga lagringsdisken replikeras inte heller till lokala värdar.

#### <a name="failback-to-original-location-recovery"></a>Redundansväxling till ursprungsplatsen

I föregående exempel är diskkonfigurationen av den virtuella Azure-datorn följande:

**Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | ---
DISK0 | C:\ | Operativsystemdisk
Disk1 | E:\ | Temporär lagring</br /> </br />Azure lägger till den här disken och tilldelar den första tillgängliga enhetsbeteckningen.
Disk2 | D:\ | SQL-systemdatabas och användardatabas1
Disk3 | G:\ | Användardatabas2


#### <a name="vmware-to-azure"></a>VMware till Azure
När återställningen till den ursprungliga platsen är klar finns inte den undantagna disken i diskkonfigurationen för den virtuella datorn. Diskar som undantogs från VMware till Azure är inte tillgängliga på den virtuella datorn som redundansväxlas.

Diskar på den virtuella VMWare-datorn (ursprunglig plats) efter planerad redundansväxling från Azure till lokalt VMware är följande:

**Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | ---
DISK0 | C:\ | Operativsystemdisk
Disk1 | D:\ | SQL-systemdatabas och användardatabas1
Disk2 | G:\ | Användardatabas2

#### <a name="hyper-v-to-azure"></a>Hyper-V till Azure
När återställningen till den ursprungliga platsen är klar är diskkonfigurationen för den virtuella datorn densamma som den ursprungliga diskkonfigurationen för den virtuella datorn för Hyper-V. Diskar som undantogs från Hyper-V-plats till Azure är inte tillgängliga på den virtuella datorn som redundansväxlas.

Diskar på den virtuella Hyper-V-datorn (ursprunglig plats) efter planerad redundansväxling från Azure till lokalt Hyper-V är följande:

**Disknamn** | **Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |   C:\ | Operativsystemdisk
DB-Disk1 | Disk1 | D:\ | SQL-systemdatabas och användardatabas1
DB-Disk2 (utesluten disk) | Disk2 | E:\ | Tillfälliga filer
DB-Disk3 (utesluten disk) | Disk3 | F:\ | SQL tempdb-databasen (mappsökväg (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | Användardatabas2


#### <a name="exclude-the-paging-file-pagefilesys-disk"></a>Undanta växlingsfildisken (pagefile.sys)

Anta att du har en virtuell dator som har en växlingsdisk som kan undantas.
Det finns två fall.

#### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>Fall 1: Växlingsfilen är konfigurerad på enheten D:
Här är diskkonfigurationen:


**Disknamn** | **Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operativsystemdisk
DB-Disk1 (disken har undantagits från skydd) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Här följer inställningarna för växlingsfiler på den virtuella källdatorn:

![Inställningar för växlingsfiler på den virtuella källdatorn](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)


Efter redundansväxlingen av den virtuella datorn från VMware till Azure eller Hyper-V till Azure är diskarna på den virtuella Azure-datorn följande:

**Disknamn** | **Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operativsystemdisk
DB-Disk1 | Disk1 | D:\ | Temporär lagring</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Eftersom Disk1 (D:) har undantagits är D: den första tillgängliga enhetsbeteckningen i listan. Azure tilldelar enhetsbeteckningen D: till den tillfälliga lagringsvolymen. Eftersom D: är tillgänglig på den virtuella Azure-datorn förblir inställningen för växlingsfilen densamma.

Här följer inställningarna för växlingsfiler på den virtuella Azure-källdatorn:

![Inställningar för växlingsfiler på den virtuella Azure-datorn](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

#### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>Fall 2: Växlingsfilen är konfigurerad på en annan enhet (än D:)

Här är diskkonfigurationen av den virtuella källdatorn:

**Disknamn** | **Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Operativsystemdisk
DB-Disk1 (disken har undantagits från skydd) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Här följer inställningarna för växlingsfiler på den lokala virtuella datorn:

![Inställningar för växlingsfiler på den lokala virtuella datorn](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Efter redundansväxlingen av den virtuella datorn från VMware/Hyper-V till Azure är diskarna på den virtuella Azure-datorn följande:

**Disknamn**| **Antal gästoperativsystem**| **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Operativsystemdisk
DB-Disk1 | Disk1 | D:\ | Temporär lagring</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Eftersom D: är den första tillgängliga enhetsbeteckningen i listan tilldelar Azure enhetsbeteckningen D: till den tillfälliga lagringsvolymen. Alla replikerade diskar har kvar samma enhetsbeteckning. Eftersom disk G: inte är tillgänglig använder systemet enhet C: för växlingsfilen.

Här följer inställningarna för växlingsfiler på den virtuella Azure-källdatorn:

![Inställningar för växlingsfiler på den virtuella Azure-datorn](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat och fått igång distributionen kan du [läsa mer](site-recovery-failover.md) om olika typer av redundansväxlingar.
