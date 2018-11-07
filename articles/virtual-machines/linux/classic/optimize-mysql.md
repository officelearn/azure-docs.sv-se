---
title: Optimera MySQL-prestandan i Linux | Microsoft Docs
description: Lär dig hur du optimerar MySQL som körs på en Azure-dator (VM) som kör Linux.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 0ba85e82824bc257869d9801f342bd6dbb0402d2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247457"
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Optimera MySQL-prestanda på virtuella Azure Linux-datorer
Det finns många faktorer som påverkar MySQL-prestandan på Azure, både i val av virtuell maskinvara och konfiguration av programvara. Den här artikeln fokuserar på optimera prestanda genom lagring, system och konfigurationer.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager](../../../resource-manager-deployment-model.md) och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om Linux VM optimeringar med Resource Manager-modellen finns i [optimera din Linux-VM på Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Använda RAID på en Azure virtuell dator
Lagring är avgörande som påverkar prestanda för databasen i molnmiljöer. Jämfört med en enskild disk, ger RAID snabbare åtkomst via samtidighet. Mer information finns i [Standard RAID-nivåer](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Diskens i/o-dataflöde och svarstid för i/o i Azure kan förbättras genom RAID. Våra tester visar att kan vara dubblerad diskens i/o-dataflöde och svarstid för i/o kan minskas med halvt i genomsnitt när antalet diskar som RAID fördubblas (från två till fyra, 4 och 8 osv.). Se [bilaga A](#AppendixA) mer information.  

Förutom diskens i/o förbättrar MySQL-prestandan när du ökar RAID-nivå.  Se [bilaga B](#AppendixB) mer information.  

Du kan också vara bättre att segmentstorleken. I allmänhet när du har en större segmentstorlek kan få du lägre omkostnader, särskilt för stora skrivningar. Om segmentstorleken är för stor, kan det dock lägga till ytterligare kostnader som hindrar dig från att utnyttja RAID. Den aktuella standardstorleken är 512 KB som har visat sig vara optimal för de flesta Allmänt produktionsmiljöer. Se [bilaga C](#AppendixC) mer information.   

Det finns begränsningar för hur många diskar som du kan lägga till för olika VM-typer. Dessa begränsningar beskrivs i [storlekar för virtuella datorer och molnet för Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx). Du behöver fyra anslutna datadiskar följa RAID-exemplet i den här artikeln, men du kan välja att ställa in RAID med färre diskar.  

Den här artikeln förutsätter att du redan har skapat en Linux-dator och har MYSQL installerat och konfigurerat. Mer information om att komma igång finns i så här installerar du MySQL på Azure.  

### <a name="set-up-raid-on-azure"></a>Konfigurera RAID på Azure
Följande steg visar hur du skapar RAID på Azure med hjälp av Azure portal. Du kan också ställa in RAID med hjälp av Windows PowerShell-skript.
I det här exemplet ska vi konfigurera RAID 0 med fyra diskar.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Lägga till en datadisk till den virtuella datorn
Gå till instrumentpanelen i Azure-portalen och välj den virtuella dator som du vill lägga till en datadisk. I det här exemplet är den virtuella datorn mysqlnode1.  

<!--![Virtual machines][1]-->

Klicka på **diskar** och klicka sedan på **Attach New**.

![Virtuella datorer Lägg till disk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Skapa en ny 500 GB-disk. Se till att **Värdcacheinställningen** är inställd på **ingen**.  När du är klar klickar du på **OK**.

![Koppla tom disk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Detta lägger till en tom disk till din virtuella dator. Upprepa detta steg tre gånger så att du har fyra datadiskar för RAID.  

Du kan se enheterna som har lagts till i den virtuella datorn genom att titta på Meddelandelogg kernel. Till exempel om du vill se det på Ubuntu, använder du följande kommando:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Skapa RAID med dessa ytterligare diskar
Följande steg beskriver hur du [konfigurera programvaru-RAID på Linux](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Om du använder XFS filsystemet, kör du följande steg när du har skapat RAID.
>
>

Om du vill installera XFS på Debian eller Ubuntu Linux myntverket, använder du följande kommando:  

    apt-get -y install xfsprogs  

Om du vill installera XFS på RHEL, Fedora eller CentOS, använder du följande kommando:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Konfigurera en ny lagringssökväg
Använd följande kommando för att ställa in en ny lagringssökväg:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Kopiera den ursprungliga informationen till en ny lagringssökväg
Använd följande kommando för att kopiera data till en ny lagringssökväg:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Ändra behörigheter så att MySQL kan komma åt (läsning och skrivning) datadisken
Använd följande kommando för att ändra behörigheter:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Justera den schemaläggning algoritmen för disk-i/o
Linux implementerar fyra typer av i/o schemaläggning algoritmer:  

* NOOP algoritmen (ingen åtgärd)
* Tidsgräns för algoritmen (tidsgräns)
* Helt fair queuing algoritmen (CFQ)
* Budget period algoritmen (Anticipatory)  

Du kan välja olika i/o-planeringsprogram under olika scenarier att optimera prestanda. I en helt direktåtkomst-miljö är det inte en viktig skillnad mellan CFQ och tidsgräns algoritmerna för prestanda. Vi rekommenderar att du ställer in miljön för MySQL-databas till tidsgräns för stabilitet. Om det finns en massa sekventiella i/o, kan CFQ minska i/o-diskprestanda.   

För SSD och annan utrustning, kan NOOP eller tidsgräns få bättre prestanda än standard scheduler.   

Innan kernel 2,5 är den standard-i/o-schemaläggning algoritmen tidsgräns. Från och med kernel 2.6.18, blev CFQ Standardalgoritmen för i/o schemaläggning.  Du kan ange den här inställningen när datorn startas för kernel eller ändra den här inställningen dynamiskt när systemet körs.  

I följande exempel visar hur du kontrollerar och ange scheduler standard NOOP-algoritmen i familjen Debian distribution.  

### <a name="view-the-current-io-scheduler"></a>Visa aktuella i/o-Schemaläggaren
Visa scheduler kör du följande kommando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Du kommer se följande utdata, vilket indikerar den aktuella scheduler:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Ändra den aktuella enheten (/ dev/sda) av algoritmen för schemaläggning i/o
Kör följande kommandon för att ändra den aktuella enheten:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Ange detta för /dev/sda enbart är inte användbart. Det måste anges för alla datadiskar där databasen finns.  
>
>

Du bör se följande utdata, som anger den grub.cfg har återskapats och att scheduler standard har uppdaterats till NOOP:  

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
Ett bra tips är att inaktivera den *atime* loggningsfunktionen i filsystemet. Atime är den senaste åtkomsttid för fil. När en fil används registrerar filsystemet tidsstämpeln i loggen. Men används den här informationen sällan. Du kan inaktivera den om du inte behöver den, vilket minskar övergripande diskåtkomsttid.  

Om du vill inaktivera loggning för atime, måste du ändra filen system configuration filen/etc / fstab och lägga till den **noatime** alternativet.  

Till exempel redigera vim/etc/fstab-filen, som att lägga till noatime som visas i följande exempel:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Sedan montera filsystemet med följande kommando:  

    mount -o remount /RAID0

Testa ändrade resultatet. När du ändrar Testfilen uppdateras inte vid åtkomst. I följande exempel visas hur koden ser ut före och efter ändringar.

Innan:        

![Code innan ändring av åtkomst till][5]

Efter:

![Code efter ändring av åtkomst till][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Öka det maximala antalet system handtag för hög samtidighet
MySQL är en databas med hög samtidighet. Standardvärdet för antal samtidiga hanterar är 1024 för Linux, vilket räcker inte alltid. Använd följande steg för att öka maximal samtidiga handtagen av systemet för att stödja hög samtidighet på MySQL.

### <a name="modify-the-limitsconf-file"></a>Ändra filen limits.conf
För att öka de högsta tillåtna samtidiga hanterar, lägger du till följande fyra rader i filen /etc/security/limits.conf. Observera att 65536 är det maximala antalet som har stöd för systemet.   

    * Mjuk nofile 65536
    * hårda nofile 65536
    * Mjuk nproc 65536
    * hårda nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Uppdatera systemet så att de nya gränserna
Om du vill uppdatera systemet, kör du följande kommandon:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Se till att gränserna uppdateras när datorn startas
Placera följande startkommandon i filen /etc/rc.local så att den träder i kraft när datorn startas.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>Optimering av MySQL-databas
Du kan använda samma inställning av prestanda strategin du använder på en lokal dator för att konfigurera MySQL på Azure.  

Viktigaste reglerna för i/o-optimering är:   

* Öka cachestorleken.
* Minska i/o-svarstid.  

Du kan uppdatera filen my.cnf, som är standardkonfigurationsfilen för både servern och klientdatorerna för att optimera MySQL-serverinställningar.  

Följande konfigurationsobjekt är de viktigaste faktorerna som påverkar prestanda för MySQL:  

* **innodb_buffer_pool_size**: buffertpoolen innehåller buffrade data och index. Detta är vanligtvis inställt på 70 procent av fysiskt minne.
* **innodb_log_file_size**: det här är loggstorleken gör om. Du kan använda gör om loggar för att säkerställa att skrivåtgärder är snabba, pålitliga och återställa efter en krasch. Detta är inställt på 512 MB, vilket ger tillräckligt med utrymme för loggning av skrivåtgärder.
* **max_connections**: ibland program inte stänger anslutningar korrekt. Ett större värde ger servern mer tid att återvinna inaktiv anslutningar. Det maximala antalet anslutningar är 10 000, men den rekommendera maximalt är 5 000.
* **Innodb_file_per_table**: den här inställningen aktiverar eller inaktiverar möjligheten för InnoDB att spara tabeller i separata filer. Aktivera alternativet så att flera avancerade administrationsåtgärder kan användas effektivt. Ur en prestanda, den snabba upp överföringen tabell utrymme och optimera prestanda för skräp management. Den rekommenderade inställningen för det här alternativet är Aktiverat.</br></br>
Standardinställningen är på, så ingen åtgärd krävs från MySQL 5.6. Standardinställningen är för tidigare versioner av. Inställningen ska ändras innan data läses in, eftersom endast nya tabeller som påverkas.
* **innodb_flush_log_at_trx_commit**: standardvärdet är 1, med den omfattning som har angetts till 0 ~ 2. Standardvärdet är det lämpligaste alternativet för fristående MySQL DB. Inställningen för 2 kan de flesta dataintegritet och är lämplig för Master i MySQL-kluster. 0 inställningen dataförluster som kan påverka tillförlitlighet (i vissa fall med bättre prestanda) och är lämplig för underordnad i MySQL-kluster.
* **Innodb_log_buffer_size**: Loggningsbufferten kan transaktioner ska köras utan att tömma loggen till disk innan transaktioner skickar. Men om det finns stora binära objekt eller textfältet, cachen ska förbrukas snabbt och ofta disk-i/o utlöses. Det är bättre att öka buffertstorleken på om Innodb_log_waits tillstånd variabeln inte är 0.
* **query_cache_size**: det bästa alternativet är att inaktivera det från början. Query_cache_size 0 (detta är standardinställningen i MySQL 5.6) och använda andra metoder för att snabba upp frågorna.  

Se [bilaga D](#AppendixD) en jämförelse av prestanda före och efter optimeringen.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Aktivera MySQL långsam frågelogg för att analysera flaskhals för prestanda
Med hjälp av den långsam frågeloggen för MySQL kan du identifiera långsamma frågor för MySQL. När du har aktiverat den långsam frågeloggen för MySQL kan du använda MySQL-verktyg som **mysqldumpslow** att identifiera flaskhals för prestanda.  

Som standard är detta inte aktiverad. Aktivera långsam frågelogg kan använda vissa processorresurser. Vi rekommenderar att du aktiverar detta tillfälligt för att felsöka flaskhalsar i prestanda. Aktivera långsam frågelogg:

1. Ändra filen my.cnf genom att lägga till följande rader i slutet:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Starta om MySQL-server.

        service  mysql  restart

3. Kontrollera om inställningen är ticka med hjälp av den **visa** kommando.

![Långsam frågelogg vidare][7]   

![Långsam frågelogg resultat][8]

I det här exemplet ser du att den långsamma query-funktionen har aktiverats. Du kan sedan använda den **mysqldumpslow** verktyg för att fastställa prestandaflaskhalsar och optimera prestanda, till exempel att lägga till index.

## <a name="appendices"></a>Tilläggen
Följande är exempel prestanda testdata som skapas i en riktad laboratoriemiljö. De tillhandahåller grundläggande på trend för prestanda-data med olika metoder för prestandajustering. Resultaten kan variera under olika versioner av miljön eller produkten.

### <a name="AppendixA"></a>Bilaga A  
**Diskprestanda (IOPS) med olika RAID-nivåer**

![IOPs per disk med olika RAID-nivåer][9]

**Test-kommandon**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> Arbetsbelastningen för det här testet använder 64 trådar, försöker komma åt den övre gränsen för RAID.
>
>

### <a name="AppendixB"></a>Bilaga B  
**Jämförelse av MySQL-prestanda (dataflöde) med olika RAID-nivåer**   
(XFS filsystem)

![Jämförelse av MySQL-prestanda med olika RAID-nivåer][10]  
![Jämförelse av MySQL-prestanda med olika RAID-nivåer][11]

**Test-kommandon**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Jämförelse av MySQL-prestanda (OLTP) med olika RAID-nivåer**  
![Jämförelse av MySQL-prestanda (OLTP) med olika RAID-nivåer][12]

**Test-kommandon**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Bilaga C   
**Diskjämförelse prestanda (IOPS) för olika segment-storlekar**  
(XFS filsystem)

![][13]

**Test-kommandon**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Filstorlekar som används för detta test är 30 GB och 1 GB, respektive, med RAID 0 (4 diskar) XFS filsystem.

### <a name="AppendixD"></a>Bilaga D  
**MySQL-prestanda (dataflöde) jämförelse före och efter optimering**  
(XFS filsystem)

![MySQL-prestanda (dataflöde) jämförelse före och efter optimering][14]

**Test-kommandon**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Konfigurationsinställningarna för standard och optimering är följande:**

| Parametrar | Standard | Optimering |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Ingen |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Mer detaljerad [optimering konfigurationsparametrar](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), referera till den [MySQL officiella anvisningarna](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Testmiljö**  

| Maskinvara | Information |
| --- | --- |
| Processor |AMD Opteron (TM)-Processor 4171 HE / 4 kärnor |
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

