---
title: "Optimera prestanda för MySQL på Linux | Microsoft Docs"
description: "Lär dig hur du optimerar MySQL som körs på en Azure-dator (VM) kör Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 8f2ec884fa98e989448ac11675e71f39aa21fa7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Optimera MySQL prestanda på virtuella Azure Linux-datorer
Det finns många faktorer som påverkar prestanda MySQL på Azure, både i val av virtuell maskinvara och konfiguration av programvara. Den här artikeln fokuserar på optimera prestanda via lagring, system och konfigurationer för databasen.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager](../../../resource-manager-deployment-model.md) och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om Linux VM optimeringar med Resource Manager-modellen finns [optimera din Linux VM på Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Använda RAID på en virtuell Azure-dator
Lagring är avgörande som påverkar databasprestanda i miljöer i molnet. Jämfört med en enskild disk ger RAID snabbare åtkomst via samtidighet. Mer information finns i [Standard RAID-nivåer](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

I/o-genomflöde och svarstid för i/o i Azure kan förbättras genom RAID. Våra tester visar att i/o-genomflöde kan vara dubblerad och i/o-svarstid kan reduceras med halvt i genomsnitt när antalet RAID-diskar fördubblas (från två till fyra, fyra till åtta osv.). Se [bilaga A](#AppendixA) mer information.  

Utöver disk-i/o förbättras MySQL prestanda om du ökar RAID-nivå.  Se [bilaga B](#AppendixB) mer information.  

Du kanske också vill överväga segmentstorleken. I allmänhet när du har en större segmentstorlek kan hämta du lägre omkostnader, särskilt för stora skrivningar. Om segmentstorleken är för stor, kan det dock lägga till ytterligare kostnader som hindrar dig från att dra nytta av RAID. Den aktuella standardstorleken är 512 KB som visat sig vara optimala för mest allmänna produktionsmiljöer. Se [bilaga C](#AppendixC) mer information.   

Det finns begränsningar på hur många diskar som du kan lägga till för olika virtuella typer. Dessa värden beskrivs i [storlekar för virtuella datorer och moln för Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Du behöver fyra bifogade datadiskar till RAID-exempel i den här artikeln, men du kan välja att ställa in RAID med färre diskar.  

Den här artikeln förutsätter att du redan har skapat en virtuell Linux-dator och ha MYSQL installeras och konfigureras. Mer information om att komma igång finns i hur du installerar MySQL på Azure.  

### <a name="set-up-raid-on-azure"></a>Ställ in RAID på Azure
Följande steg visar hur du skapar RAID på Azure med hjälp av Azure portal. Du kan också ställa in RAID med hjälp av Windows PowerShell-skript.
I det här exemplet ska vi konfigurera RAID 0 med fyra diskar.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Lägg till en datadisk till den virtuella datorn
Gå till instrumentpanelen i Azure-portalen och välj den virtuella dator som du vill lägga till en datadisk. I det här exemplet är den virtuella datorn mysqlnode1.  

<!--![Virtual machines][1]-->

Klicka på **diskar** och klicka sedan på **bifoga nya**.

![Virtuella datorer lägger du till disk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Skapa en ny 500 GB disk. Se till att **värden Cache inställningar** är inställd på **ingen**.  När du är klar klickar du på **OK**.

![Anslut tom disk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Detta lägger till en tom disk i den virtuella datorn. Upprepa detta steg tre gånger så att du har fyra datadiskar för RAID.  

Du kan se enheterna som har lagts till i den virtuella datorn genom att titta på kernel message-loggen. Till exempel om du vill se detta på Ubuntu, använder du följande kommando:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Skapa RAID med ytterligare diskar
Följande steg beskriver hur du [konfigurera programvarubaserad RAID på Linux](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Om du använder filsystemet XFS, kör du följande steg när du har skapat RAID.
>
>

Om du vill installera XFS på Debian och Ubuntu Linux myntverket, använder du följande kommando:  

    apt-get -y install xfsprogs  

Om du vill installera XFS på Fedora, CentOS eller RHEL, använder du följande kommando:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Ställ in en ny lagringssökväg
Använd följande kommando för att ställa in en ny lagringssökväg som:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Kopiera den ursprungliga informationen till den nya sökvägen för lagring
Använd följande kommando för att kopiera data till den nya sökvägen för lagring:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Ändra behörigheter så att MySQL kan komma åt (läsning och skrivning) datadisken
Använd följande kommando för att ändra behörigheter:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Justera den schemaläggning algoritmen för disk-i/o
Linux implementerar fyra typer av i/o schemaläggning algoritmer:  

* NOOP algoritmen (åtgärden kan Nej)
* Tidsgräns för algoritmen (tidsgräns)
* Helt verkliga MSMQ-algoritmen (CFQ)
* Budget period algoritmen (Anticipatory)  

Du kan välja olika i/o-planeringsprogram under olika scenarier att optimera prestanda. I en miljö med helt direktåtkomst finns det inte en betydande skillnader mellan CFQ och tidsgräns algoritmer för prestanda. Vi rekommenderar att du ställer in MySQL-databasmiljön tidsgräns för stabilitet. Om det är mycket sekventiella i/o, kan CFQ minska diskens i/o-prestanda.   

NOOP eller tidsgräns kan få bättre prestanda än standard Schemaläggaren för SSD och annan utrustning.   

Standard-i/o schemaläggning algoritmen är innan kernel 2.5 tidsgräns. Från och med kernel 2.6.18 blev CFQ standard-i/o schemaläggning algoritmen.  Du kan ange den här inställningen när datorn startas i kernel eller ändra den här inställningen dynamiskt när systemet körs.  

Exemplet nedan visar hur du kontrollerar och ange standard Schemaläggaren till algoritmen NOOP i familjen Debian distribution.  

### <a name="view-the-current-io-scheduler"></a>Visa aktuella i/o-Schemaläggaren
Visa scheduler kör följande kommando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Visas följande utdata som visar aktuella Schemaläggaren:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Ändra den aktuella enheten (/ dev/sda) schemaläggning i/o-algoritmen
Kör följande kommandon för att ändra den aktuella enheten:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Ange detta för /dev/sda enbart är inte användbar. Det måste anges på alla datadiskar där databasen finns.  
>
>

Du bör se följande utdata, som anger att grub.cfg har återskapats och som standard Schemaläggaren har uppdaterats till NOOP:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Red Hat distribution-familjen behöver du följande kommando:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Konfigurera systeminställningar filen operations
Ett bra tips är att inaktivera den *atime* loggningsfunktionen i filsystemet. Atime är den senaste åtkomsttid för fil. När en fil används registrerar filsystemet i loggen. Men används den här informationen sällan. Du kan inaktivera den om du inte behöver den, vilket minskar övergripande disktid åtkomst.  

Om du vill inaktivera atime loggning måste du ändra filen system configuration filen/etc / fstab och lägga till den **noatime** alternativet.  

Till exempel redigera vim /etc/fstab filen, lägga till noatime som visas i följande exempel:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Sedan återansluta filsystem med följande kommando:  

    mount -o remount /RAID0

Testa ändrade resultatet. När du ändrar Testfilen uppdateras inte den åtkomst-tid. Följande exempel visar hur koden ser ut före och efter ändring.

Innan:        

![Code innan åtkomst ändringar][5]

Efter:

![Kod efter åtkomst ändring][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Öka det maximala antalet system handtag för hög samtidighet
MySQL är en hög concurrency-databas. Standardvärdet för antalet samtidiga handtag är 1024 för Linux som räcker inte alltid. Använd följande steg för att öka i systemet för att stödja hög samtidighet på MySQL maximala samtidiga handtag.

### <a name="modify-the-limitsconf-file"></a>Ändra filen limits.conf
Lägg till följande fyra rader i filen /etc/security/limits.conf för att öka de maximala tillåtna samtidiga referenser. Observera att 65536 är det maximala antal som har stöd för systemet.   

    * mjuka nofile 65536
    * hårda nofile 65536
    * mjuka nproc 65536
    * hårda nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Uppdatera systemet för de nya gränserna
Kör följande kommandon för att uppdatera systemet:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Se till att gränserna som har uppdaterats vid start
Placera följande startkommandon i filen /etc/rc.local så börjar gälla när datorn startas.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>MySQL-databas optimering
För att konfigurera MySQL på Azure, kan du använda samma prestandajustering strategin du använder på en lokal dator.  

De huvudsakliga i/o optimering reglerna är:   

* Öka cachestorleken.
* Minska i/o-svarstid.  

Du kan uppdatera filen my.cnf, som är standardkonfigurationsfilen för både servern och klientdatorerna för att optimera MySQL-serverinställningar.  

För följande konfigurationsobjekt är de viktigaste faktorerna som påverkar prestanda MySQL:  

* **innodb_buffer_pool_size**: buffertpoolen innehåller buffrade data och index. Det här värdet vanligtvis 70 procent av det fysiska minnet.
* **innodb_log_file_size**: Detta är gör om loggfilens storlek. Du kan använda gör om-loggarna för att säkerställa att skrivåtgärder snabb, tillförlitlig och återställas efter en krasch. Detta är inställt på 512 MB, vilket ger tillräckligt med utrymme för att logga skrivåtgärder.
* **max_connections**: ibland program Stäng inte anslutningar korrekt. Ett större värde får servern längre tid att återvinna inaktiv anslutningar. Det maximala antalet anslutningar är 10 000, men det rekommenderade maximala antalet är 5 000.
* **Innodb_file_per_table**: den här inställningen aktiverar eller inaktiverar InnoDB möjlighet att lagra tabeller i separata filer. Aktivera alternativet så att flera avancerade administration-åtgärder kan användas effektivt. Ur en prestanda, den påskynda överföringen tabell utrymme och optimera prestanda för skräp management. Den rekommenderade inställningen för det här alternativet är Aktiverat.</br></br>
Standardinställningen är ON, så ingen åtgärd krävs från MySQL 5.6. Standardinställningen är för tidigare versioner av. Inställningen måste ändras innan data läses eftersom endast nya tabeller påverkas.
* **innodb_flush_log_at_trx_commit**: standardvärdet är 1, med den omfattning som anges som 0 ~ 2. Standardvärdet är det lämpligaste alternativet för fristående MySQL-databas. Inställningen av 2 kan de flesta dataintegriteten och lämpar sig för Master i MySQL-kluster. 0 inställningen dataförluster som kan påverka tillförlitlighet (i vissa fall med bättre prestanda) och lämpar sig för underordnade i MySQL-kluster.
* **Innodb_log_buffer_size**: Loggningsbufferten kan transaktioner ska köras utan att rensa loggen på disken innan du genomför transaktionerna. Om det finns stora binära objekt eller textfältet, cachen används snabbt och ofta disk-i/o ska utlösas. Det är bättre öka buffertstorleken om Innodb_log_waits tillstånd variabeln inte är 0.
* **query_cache_size**: det bästa alternativet är att inaktivera redan från början. Query_cache_size 0 (detta är standardinställningen i MySQL 5.6) och använda andra metoder för att påskynda frågor.  

Se [bilaga D](#AppendixD) för en jämförelse av prestanda före och efter optimeringen.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Aktivera MySQL långsam frågeloggen för att analysera flaskhals
MySQL långsam fråga loggen kan hjälpa dig identifiera långsamma frågor för MySQL. Du kan använda MySQL-verktyg som när du har aktiverat MySQL långsam frågeloggen **mysqldumpslow** att identifiera flaskhals.  

Som standard är detta inte aktiverat. Aktivera långsam frågeloggen kan använda vissa processorresurser. Vi rekommenderar att du aktiverar detta tillfälligt för felsökning av flaskhalsar. Aktivera långsam frågeloggen:

1. Ändra my.cnf-filen genom att lägga till följande rader i slutet:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Starta om MySQL-servern.

        service  mysql  restart

3. Kontrollera om inställningen börjar gälla med hjälp av den **visa** kommando.

![Långsamma frågeloggen ON][7]   

![Långsamma frågeloggen resultat][8]

I det här exemplet ser du att funktionen långsam fråga har aktiverats. Du kan sedan använda den **mysqldumpslow** verktyg för att fastställa prestandaflaskhalsar och optimera prestanda, till exempel lägga till index.

## <a name="appendices"></a>Tilläggen
Följande är exempel test prestandadata framställs i labbmiljö riktade. De ger grundläggande på prestanda data trend med olika metoder för prestandajustering. Resultatet kan variera under olika versioner av miljö eller produkt.

### <a name="AppendixA"></a>Bilaga A  
**Diskprestanda (IOPS) med olika RAID-nivåer**

![Disk-IOPS med olika RAID-nivåer][9]

**Test-kommandon**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> Arbetsbelastningen för det här testet använder 64 trådar, försöker komma åt den övre gränsen för RAID.
>
>

### <a name="AppendixB"></a>Bilaga B  
**MySQL prestanda (dataflöde) jämförelse med olika RAID-nivåer**   
(XFS filsystem)

![MySQL prestanda jämförelse med olika RAID-nivåer][10]  
![MySQL prestanda jämförelse med olika RAID-nivåer][11]

**Test-kommandon**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**MySQL prestanda (OLTP) jämförelse med olika RAID-nivåer**  
![MySQL prestanda (OLTP) jämförelse med olika RAID-nivåer][12]

**Test-kommandon**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Bilaga C   
**Jämförelse av disk prestanda (IOPS) för olika segment storlekar**  
(XFS filsystem)

![][13]

**Test-kommandon**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Filstorlekarna som används för detta test är 30 och 1 GB respektive, med RAID 0 (4 diskar) XFS filsystem.

### <a name="AppendixD"></a>Bilaga D  
**MySQL prestanda (dataflöde) jämförelse före och efter optimering**  
(XFS File System)

![MySQL prestanda (dataflöde) jämförelse före och efter optimering][14]

**Test-kommandon**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Konfigurationsinställning för standard och optimering är följande:**

| Parametrar | Standard | Optimering |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Ingen |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Mer detaljerad [optimering konfigurationsparametrar](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), referera till den [MySQL officiella instruktioner](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Testmiljö**  

| Maskinvara | Information |
| --- | --- |
| Processor |AMD Opteron (TM)-Processor 4171 HAN / 4 kärnor |
| Minne |14 GB |
| Disk |10 GB-disk |
| Operativsystem |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

