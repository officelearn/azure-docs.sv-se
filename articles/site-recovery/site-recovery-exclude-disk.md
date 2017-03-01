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
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/24/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: af0d66d92ca542f415de779fb638db166ba5f26a
ms.openlocfilehash: 5e0527fb0a41d8892c9e22d6d6d2f252972e69d0


---
#<a name="exclude-disk-from-replication"></a>Undanta en disk från replikering
Den här artikeln beskriver hur du tar bort diskar från replikering för att optimera replikeringsbandbredden som används eller för att optimera resurser som används av dessa diskar på målsidan. Funktionen stöds för scenarier med VMware till Azure och Hyper-V till Azure.

##<a name="prerequisites"></a>Krav

Som standard replikeras alla diskar på en dator. Om du vill undanta en disk från replikeringen måste mobilitetstjänsten installeras manuellt på datorn innan du aktiverar replikering om du replikerar från **VMware till Azure**


## <a name="why-exclude-disks-from-replication"></a>Varför ska jag undanta en disk från replikering?
Vanliga orsaker till att diskar undantas från replikering:

1. Data på den undantagna disken var inte viktiga eller behövde inte replikeras.

2. Lagrings- och nätverksresurser kan sparas genom att inte replikera dataomsättningen.

##<a name="what-are-the-typical-scenarios"></a>Vilka är de typiska scenarierna?
Det finns vissa specifika exempel på dataomsättning som är enkla att identifiera och som är bra exempel på sådant som kan undantas, till exempel växlingsfilskrivningar och Microsoft SQL Server tempdb-skrivningar. Beroende på arbetsbelastningen och underlagringssystemet kan växlingsfilen registrera en stor dataomsättning. Men att replikera data från den primära platsen till Azure skulle vara resurskrävande. Replikering av en virtuell dator med en enskild virtuell disk med både operativsystemet och växlingsfilen kan därför optimeras genom att:

1. Dela upp den enskilda virtuella hårddisken i två virtuella diskar – en med operativsystemet och en med växlingsfilen.
2. Undanta växlingsfilen från replikering.

Om du har Microsoft SQL Server med tempdb- och systemdatabasfilen på samma disk kan replikeringen optimeras genom att:

1. Förvara systemdatabasen och tempdb på två olika diskar.
2. Undanta tempdb-disken från replikeringen.

##<a name="how-to-exclude-disk-from-replication"></a>Hur undantar jag en disk från replikering?

###<a name="vmware-to-azure"></a>VMware till Azure
Följ arbetsflödet i [Aktivera replikering](site-recovery-vmware-to-azure.md#enable-replication) för att skydda en virtuell dator med hjälp av Azure Site Recovery-portalen. I steg 4 i Aktivera replikering finns kolumnen **DISK TO REPLICATE** (DISK ATT REPLIKERA) som kan användas för att undanta en disk från replikeringen. Alla diskar är markerade som standard. Avmarkera den disk som du vill undanta från replikeringen och följ stegen för att aktivera replikering. 

![Aktivera replikering](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)
    
    
>[!NOTE]
> 
> * Du kan endast utesluta diskar som mobilitetstjänsten har installerats på. Du måste installera mobilitetstjänsten manuellt eftersom den endast installeras med push-mekanismen när replikering har aktiverats.
> * Endast standarddiskar kan undantas från replikering. Du kan inte undanta operativsystemdiskar eller dynamiska diskar.
> * När replikering har aktiverats kan du inte lägga till eller ta bort diskar för replikering. Om du vill lägga till eller undanta en disk måste du inaktivera skyddet för datorn och sedan aktivera det igen.
> * Om du undantar en disk som behövs för att ett program ska fungera efter redundansväxlingen till Azure måste du skapa den manuellt i Azure så att det replikerade programmet kan köras. Du kan också integrera Azure Automation i en återställningsplan för att skapa disken under en redundansväxling av datorn.
> * Virtuell Windows-dator: Diskar som du skapar manuellt i Azure växlas inte tillbaka igen. Om du till exempel växlar över tre diskar och skapar två direkt på den virtuella Azure-datorn kommer endast tre diskar som växlades över att växlas tillbaka. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller vars skydd har återaktiverats från en lokal plats till Azure.
> * Virtuell Linux-dator: Diskar som du skapar manuellt i Azure växlas tillbaka igen. Om du till exempel växlar över tre diskar och skapar två direkt i Azure kommer alla fem diskar att växlas tillbaka. Du kan inte undanta diskar som har skapats manuellt från återställningen.
> 

###<a name="hyper-v-to-azure"></a>Hyper-V till Azure
Följ arbetsflödet i [Aktivera replikering](site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication) för att skydda en virtuell dator med hjälp av Azure Site Recovery-portalen. I steg 4 i Aktivera replikering finns kolumnen **DISK TO REPLICATE** (DISK ATT REPLIKERA) som kan användas för att undanta diskar från replikeringen. Som standard är alla diskar markerade för replikering. Avmarkera den disk som du vill undanta från replikeringen och följ stegen för att aktivera replikering. 

![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
>[!NOTE]
> 
> * Endast standarddiskar kan undantas från replikering. Du kan inte utesluta operativsystemdisken och vi rekommenderar att du inte undantar dynamiska diskar. ASR kan inte identifiera vilken virtuell hårddisk som är en standarddisk eller dynamisk disk på den virtuella gästdatorn.  Om beroende dynamiska volymdiskar inte undantas identifieras en skyddad dynamisk disk som felaktig på den virtuella datorn vid redundansväxlingen och data på disken kan inte nås.    
> * När replikering har aktiverats kan du inte lägga till eller ta bort diskar för replikering. Om du vill lägga till eller undanta en disk måste du inaktivera skyddet för den virtuella datorn och sedan aktivera det igen.
> * Om du undantar en disk som behövs för att ett program ska fungera efter redundansväxlingen till Azure måste du skapa den manuellt i Azure så att det replikerade programmet kan köras. Du kan också integrera Azure Automation i en återställningsplan för att skapa disken under en redundansväxling av datorn.
> * Diskar som du skapar manuellt i Azure växlas inte tillbaka igen. Om du till exempel växlar över tre diskar och skapar två direkt på den virtuella datorn i Azure kommer endast tre diskar som växlades över att växlas tillbaka från Azure till Hyper-V. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller omvända replikeringar från Hyper-V till Azure.
 


##<a name="end-to-end-scenarios-of-exclude-disks"></a>Undanta diskar i olika scenarier från slutpunkt till slutpunkt
Vi beskriver två scenarier för att du ska få en bättre förståelse för hur undantag av diskar fungerar.

1. SQL Server tempdb-disk
2. Växlingsdisk

###<a name="excluding-the-sql-server-tempdb-disk"></a>Undanta en SQL Server tempdb-disk
Anta att du har en virtuell dator som kör SQL Server och som har en tmpdb som kan undantas.

Namnet på den virtuella datorn: SalesDB Diskar på den virtuella källdatorn:


**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | OS-disk
DB-Disk1| Disk1 | D:\ | SQL-systemdatabas och användardatabas1
DB-Disk2 (disken har undantagits från skydd) | Disk2 | E:\ | Tillfälliga filer
DB-Disk3 (disken har undantagits från skydd) | Disk3 | F:\ | SQL tempdb-databas (mappsökväg (F:\MSSQL\Data\) --> notera mappsökvägen före redundansväxling
DB-Disk4 | Disk4 |G:\ |Användardatabas2

Eftersom dataomsättningen på två diskar för den virtuella datorn är tillfällig, undantas Disk2 och Disk3 från replikeringen samtidigt som SalesDB VM skyddas. Azure Site Recovery replikerar inte dessa diskar, och vid redundansväxlingen visas inte dessa diskar på den virtuella datorn som redundansväxlas på Azure

Diskar på den virtuella Azure-datorn efter redundansväxlingen:

**Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | ---
DISK0 |    C:\ | OS-disk
Disk1 |    E:\ | Temporär lagring [Azure lägger till den här disken och tilldelar den första tillgängliga enhetsbeteckningen]
Disk2 | D:\ | SQL-systemdatabas och användardatabas1
Disk3 | G:\ | Användardatabas2

E: är den första tillgängliga enhetsbeteckningen i listan eftersom Disk2 och Disk3 undantogs från SalesDB VM. Azure tilldelar E: till den tillfälliga lagringsvolymen. Alla replikerade diskar har kvar samma enhetsbeteckning.

Disk3 som var SQL tempdb-disken (mappsökvägen för tempdb är F:\MSSQL\Data\) och som undantogs från replikering är inte tillgänglig på den virtuella datorn som redundansväxlas. Därför är SQL-tjänsten i stoppat tillstånd och behöver sökvägen F:\MSSQL\Data.

Du kan skapa sökvägen på två sätt.

1. Lägga till en ny disk och tilldela tempdb-mappsökvägen eller
2. Använda den befintliga tillfälliga lagringsdisken för tempdb-mappsökvägen

####<a name="add-a-new-disk"></a>Lägga till en ny disk:

1. Notera sökvägen SQL tempdb.mdf och tempdb.ldf före redundansväxlingen.
2. Lägg till en ny disk till den virtuella datorn som redundansväxlas med samma eller större storlek än SQL tempdb-disken (Disk3) med hjälp av Azure Portal.
3. Logga in på den virtuella Azure-datorn. Initiera och formatera den nytillagda disken från diskhanteringskonsolen (diskmgmt.msc).
4. Tilldela samma enhetsbeteckning som användes av SQL tempdb-disken (F:).
5. Skapa en tempdb-mapp på volym F: (F:\MSSQL\Data).
6. Starta SQL-tjänsten från tjänstkonsolen.

####<a name="use-existing-temporary-storage-disk-for-sql-tempdb-folder-path"></a>Använd den befintliga tillfälliga lagringsdisken för SQL tempdb-mappsökvägen:

1. Öppna en kommandotolk
2. Kör SQL Server i återställningsläge från kommandotolken

        Net start MSSQLSERVER /f / T3608

3. Kör följande sqlcmd för att ändra tempdb-sökvägen till en ny sökväg

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

Se följande Azure-riktlinjer för tillfällig lagringsdisk

* Använda SSD:er i virtuella Azure-datorer för att lagra SQL Server TempDB och buffertpooltillägg
* Prestandametodtips för SQL Server på virtuella Azure-datorer

###<a name="failback-from-azure-to-on-premises"></a>Återställning efter fel (från Azure till lokal värd)
Låt oss se vilka diskar som replikeras när du redundansväxlar från Azure till din lokala VMware- eller Hyper-V-värd. De diskar som du skapar manuellt i Azure replikeras inte. Om du till exempel växlar över tre diskar och skapar två direkt på den virtuella Azure-datorn kommer endast tre diskar som växlades över att växlas tillbaka. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller återaktivering av skydd lokalt till Azure. Den tillfälliga lagringsdisken replikeras inte heller till den lokala värden.

####<a name="failback-to-original-location-recovery-olr"></a>Återställning till ursprungsplatsen (OLR)

Diskkonfiguration för virtuell Azure-dator (i exemplet ovan):

**Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken** 
--- | --- | --- 
DISK0 | C:\ | OS-disk
Disk1 |    E:\ | Temporär lagring [Azure lägger till den här disken och tilldelar den första tillgängliga enhetsbeteckningen]
Disk2 |    D:\ | SQL-systemdatabas och användardatabas1
Disk3 |    G:\ | Användardatabas2


####<a name="vmware-to-azure"></a>VMware till Azure
När återställningen till den ursprungliga platsen är klar finns inte den undantagna disken i diskkonfigurationen för den virtuella datorn. Det innebär att diskar som undantas från VMware till Azure inte är tillgängliga på den virtuella datorn som redundansväxlas. 

Diskar på den virtuella VMWare-datorn (ursprunglig plats) efter planerad redundansväxling från Azure till lokalt VMware:

**Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken** 
--- | --- | --- 
DISK0 | C:\ | OS-disk
Disk1 |    D:\ | SQL-systemdatabas och användardatabas1
Disk2 |    G:\ | Användardatabas2

####<a name="hyper-v-to-azure"></a>Hyper-V till Azure
När återställningen till den ursprungliga platsen är klar är diskkonfigurationen för den virtuella datorn densamma som den ursprungliga diskkonfigurationen för den virtuella datorn för Hyper-V. Det innebär att de diskar som undantogs från Hyper-V-platsen till Azure inte är tillgängliga på den virtuella datorn som redundansväxlas.

Diskar på den virtuella Hyper-V-datorn (ursprunglig plats) efter planerad redundansväxling från Azure till lokalt Hyper-V:

**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |    C:\ | OS-disk
DB-Disk1 | Disk1 | D:\ | SQL-systemdatabas och användardatabas1
DB-Disk2 (utesluten disk) | Disk2 | E:\ | Tillfälliga filer
DB-Disk3 (utesluten disk) | Disk3 | F:\ | SQL tempdb-databasen (mappsökväg (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | Användardatabas2


####<a name="exclude-paging-file-disk"></a>Utesluta växlingsfilen

Anta att du har en virtuell dator som har en växlingsdisk som kan undantas.
Det finns två fall:

####<a name="case-1-pagefile-is-configured-on-the-d-drive"></a>Fall 1: Växlingsfilen är konfigurerad på enheten D:
Diskkonfiguration:


**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | OS-disk
DB-Disk1 (disken har undantagits från skydd) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Växlingsfilens inställningar på den virtuella källdatorn:

![Aktivera replikering](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)
    

Diskar på den virtuella Azure-datorn efter redundansväxling av den virtuella datorn från VMware till Azure eller Hyper-V till Azure:
**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | OS-disk
DB-Disk1 | Disk1 | D:\ | Temporär lagring – > pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Eftersom Disk1 (D:) har undantagits är D: den första tillgängliga enhetsbeteckningen i listan. Azure tilldelar enhetsbeteckningen D: till den tillfälliga lagringsvolymen.  Eftersom D: är tillgänglig på den virtuella Azure-datorn förblir växlingsfilens inställning på den virtuella datorn densamma.

Växlingsfilens inställningar på den virtuella Azure-datorn:

![Aktivera replikering](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

####<a name="case-2-pagefile-file-is-configured-on-any-other-driveother-than-d-drive"></a>Fall 2: Växlingsfilen är konfigurerad på en annan enhet (än D:)

Diskkonfiguration för den virtuella källdatorn:

**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | OS-disk
DB-Disk1 (disken har undantagits från skydd) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Växlingsfilens inställningar på den lokala virtuella datorn:

![Aktivera replikering](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Diskar på den virtuella Azure-datorn efter redundansväxling av den virtuella datorn från VMware till Azure eller Hyper-V till Azure:

**Disknamn**| **Gäst-OS disknr**| **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |OS-disk
DB-Disk1 | Disk1 | D:\ | Temporär lagring – > pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Eftersom D: är den första tillgängliga enhetsbeteckningen i listan tilldelar Azure enhetsbeteckningen D: till den tillfälliga lagringsvolymen. Alla replikerade diskar har kvar samma enhetsbeteckning. Eftersom G-disken inte är tillgänglig används enhet C: för växlingsfilen.

Växlingsfilens inställningar på den virtuella Azure-datorn:

![Aktivera replikering](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat och fått igång distributionen kan du [läsa mer](site-recovery-failover.md) om olika typer av redundansväxlingar.



<!--HONumber=Feb17_HO3-->


