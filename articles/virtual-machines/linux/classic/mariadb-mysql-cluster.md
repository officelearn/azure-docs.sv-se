---
title: Kör en MariaDB (MySQL)-kluster i Azure | Microsoft Docs
description: Skapa en MariaDB + Galera MySQL-kluster på Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: sabbour
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 4a3eede532345f8628af1722a06531571f01afbf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>MariaDB (MySQL) kluster: Azure självstudiekursen
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager](../../../resource-manager-deployment-model.md) och klassisk. Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Azure Resource Manager-modellen.

> [!NOTE]
> MariaDB Enterprise kluster är nu tillgänglig i Azure Marketplace. Nytt erbjudande distribuera automatiskt ett MariaDB Galera kluster på Azure Resource Manager. Du bör använda ett nytt erbjudande från [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/).
>
>

Den här artikeln visar hur du skapar flera Master [Galera](http://galeracluster.com/products/) kluster med [MariaDBs](https://mariadb.org/en/about/) (en robust, skalbara och tillförlitliga gör att installera ersättning för MySQL) ska fungera i en miljö med hög tillgänglighet på virtuella Azure-datorer.

## <a name="architecture-overview"></a>Översikt över arkitekturen
Den här artikeln beskrivs hur du utför följande steg:

- Skapa ett kluster med tre noder.
- Avgränsa data diskarna från den OS-disken.
- Skapa datadiskar i RAID-0/stripe inställningen för att öka IOPS.
- Använd Azure belastningsutjämnare för att utjämna belastningen för tre noder.
- Skapa en VM-avbildning som innehåller MariaDB + Galera för att minimera återkommande arbete, och använda den för att skapa det andra klustret virtuella datorer.

![Systemarkitektur](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> Det här avsnittet använder den [Azure CLI](../../../cli-install-nodejs.md) verktyg, så se till att ladda ned dem och Anslut dem till din Azure-prenumeration enligt instruktionerna. Om du behöver en referens till de Azure CLI-kommandona finns i [Azure CLI kommandoreferens](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Du måste också [skapa en SSH-nyckel för autentisering] och Anteckna platsen för filen .pem.
>
>

## <a name="create-the-template"></a>Skapa mallen
### <a name="infrastructure"></a>Infrastruktur
1. Skapa en tillhörighetsgrupp för att lagra resurser tillsammans.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Skapa ett virtuellt nätverk.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Skapa ett lagringskonto som värd för alla våra diskar. Du bör placera mer än 40 hårt belastat diskar på samma lagringskonto för att undvika att träffa den 20 000 IOPS konto lagringsgränsen. I det här fallet är det bra under denna gräns så att du lagrar allt på samma konto för enkelhetens skull.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Hitta namnet på den CentOS 7 virtuella datoravbildningen.

        azure vm image list | findstr CentOS
   Utdata blir något som liknar `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Du kan använda det namnet i följande steg.
5. Skapa mall för virtuell dator och Ersätt /path/to/key.pem med sökvägen där du sparade den genererade .pem SSH-nyckeln.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Koppla fyra 500 GB datadiskar till den virtuella datorn för användning i RAID-konfigurationen.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Använda SSH för att logga in på mallen för virtuell dator som du skapade i mariadbhatemplate.cloudapp.net:22 och ansluta med hjälp av din privata nyckel.

### <a name="software"></a>Programvara
1. Hämta roten.

        sudo su

2. Installera stöd för RAID:

    a. Installera mdadm.

              yum install mdadm

    b. Skapa RAID0/stripe-konfigurationen med ett EXT4 filsystem.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Skapa katalogen montera punkt.

              mkdir /mnt/data
    d. Hämta UUID för nyskapade RAID-enhet.

              blkid | grep /dev/md0
    e. Redigera /etc/fstab.

              vi /etc/fstab
    f. Lägga till enheten om du vill aktivera automatisk montering på omstart, ersätta UUID med värdet från den tidigare **blkid** kommando.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Montera den nya partitionen.

              mount /mnt/data

3. Installera MariaDB.

    a. Skapa filen MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Fyll filen lagringsplatsen med följande innehåll:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Ta bort befintliga username@Domain och mariadb-bibliotek för att undvika konflikter.

           yum remove postfix mariadb-libs-*
    d. Installera MariaDB med Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Flytta datakatalog MySQL till RAID block-enhet.

    a. Kopiera den aktuella MySQL-katalogen till den nya platsen och ta bort den gamla katalogen.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Ange behörigheter för den nya katalogen därefter.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Skapa en symlink som den gamla katalogen pekar till den nya platsen på den RAID-partitionen.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Eftersom [SELinux kommer att störa klusteråtgärder](http://galeracluster.com/documentation-webpages/configuration.html#selinux), är det nödvändigt att inaktivera för den aktuella sessionen. Redigera `/etc/selinux/config` att inaktivera för efterföljande omstart.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Validera MySQL körs.

   a. Starta MySQL.

           service mysql start
   b. Skydda MySQL-installationen, ange rotlösenordet, ta bort anonyma användare om du vill inaktivera fjärråtkomst rot inloggning och ta bort testdatabasen.

           mysql_secure_installation
   c. Skapa en användare på databasen för klusteråtgärder och eventuellt för dina program.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Stoppa MySQL.

            service mysql stop
7. Skapa en platshållare för konfigurationen.

   a. Redigera MySQL-konfiguration om du vill skapa en platshållare för för klustret. Ersätt inte den **`<Variables>`** eller ta bort kommentarerna nu. Som sker när du har skapat en virtuell dator från den här mallen.

            vi /etc/my.cnf.d/server.cnf
   b. Redigera den **[galera]** avsnittet och ta bort.

   c. Redigera den **[mariadb]** avsnitt.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. Öppna portar i brandväggen som krävs med hjälp av FirewallD på CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Läs in brandväggen: `firewall-cmd --reload`

9. Optimera systemets prestanda. Mer information finns i [strategi för prestandajustering](optimize-mysql.md).

   a. Redigera konfigurationsfilen MySQL igen.

            vi /etc/my.cnf.d/server.cnf
   b. Redigera den **[mariadb]** avsnittet och Lägg till följande innehåll:

   > [!NOTE]
   > Vi rekommenderar att innodb\_buffert\_pool_size är 70 procent av den Virtuella datorns minne. I det här exemplet har det ställts in på 2,45 GB för medelstora virtuella Azure-datorn med 3.5 GB RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Stoppa MySQL, inaktivera MySQL-tjänsten körs på Start för att undvika störningar i klustret när du lägger till en nod och ta bort etableringen av datorn.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Avbilda den virtuella datorn via portalen. (För närvarande [utfärda #1268 i Azure CLI-verktygen](https://github.com/Azure/azure-xplat-cli/issues/1268) handlar om att bilder som avbildas av Azure CLI-verktygen inte kan fångas bifogade datadiskar.)

    a. Stäng av datorn via portalen.

    b. Klicka på **avbilda** och ange Avbildningsnamnet som **mariadb galera avbildning**. Ange en beskrivning och markera ”jag har kört waagent”.
      
      ![Avbilda den virtuella datorn](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Skapa klustret
Skapa tre virtuella datorer med hjälp av mallen du skapat, konfigurera och starta klustret.

1. Skapa den första CentOS 7 Virtuella mariadb galera avbildning-avbildningen som du skapat med följande information:

 - Virtuella nätverksnamnet: mariadbvnet
 - Undernät: mariadb
 - Datorn storlek: medelstor
 - Molntjänstnamnet: mariadbha (eller namnet som du vill att komma åt via mariadbha.cloudapp.net)
 - Datornamn: mariadb1
 - Användarnamn: azureuser
 - SSH-åtkomst: aktiverat
 - Skicka SSH certifikat PEM-filen och ersätta /path/to/key.pem med sökvägen där du sparade den genererade .pem SSH-nyckeln.

   > [!NOTE]
   > Följande kommandon delas över flera rader för tydlighetens skull, men du bör ange varje som en rad.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. Skapa två flera virtuella datorer genom att ansluta dem till Molntjänsten mariadbha. Ändra namnet på virtuella datorn och SSH-port till ett unikt portnummer som inte står i konflikt med andra virtuella datorer i samma molntjänst.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  För MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. Du behöver hämta interna IP-adressen för var och en av de tre virtuella datorerna för nästa steg:

    ![Hämtar IP-adress](./media/mariadb-mysql-cluster/IP.png)
4. Använda SSH för att logga in i tre virtuella datorer och redigera konfigurationsfilen för var och en av dem.

        sudo vi /etc/my.cnf.d/server.cnf

    Ta bort kommentarerna **`wsrep_cluster_name`** och **`wsrep_cluster_address`** genom att ta bort den **#** i början av raden.
    Dessutom kan ersätta **`<ServerIP>`** i **`wsrep_node_address`** och **`<NodeName>`** i **`wsrep_node_name`** med den Virtuella IP-adress, namn, och ta bort kommentarerna samt dessa rader.
5. Starta klustret på MariaDB1 och låt den körs vid start.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Starta MySQL på MariaDB2 och MariaDB3 och låt den körs vid start.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Belastningsutjämning klustret
När du har skapat de klustrade virtuella datorerna kan du lagt till dem i en tillgänglighetsuppsättning kallas clusteravset så att de placerades i olika fel- och update-domäner och att Azure aldrig har Underhåll på alla datorer på samma gång. Den här konfigurationen uppfyller kraven som stöds av Azure servicenivåavtalet (SLA).

Använda Azure belastningsutjämnare för att utjämna begäranden mellan tre noder.

Kör följande kommandon på datorn med hjälp av Azure CLI.

Parametrarna kommandostruktur är: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

CLI anger avsökningsintervall för load balancer till 15 sekunder, vilket kan vara lite för långt. Ändra den i portalen under **slutpunkter** för någon av de virtuella datorerna.

![Redigera slutpunkt](./media/mariadb-mysql-cluster/Endpoint.PNG)

Välj **konfigurera om den belastningsutjämnade uppsättningen**.

![Konfigurera om den belastningsutjämnade uppsättningen](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Ändra **avsökning intervall** till 5 sekunder och spara ändringarna.

![Ändra avsökningsintervall](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Validera att klustret
Det tunga arbetet görs. Klustret ska finnas nu tillgänglig för `mariadbha.cloudapp.net:3306`, vilket träffar belastningen belastningsutjämnare och flöde begäranden mellan de tre virtuella datorerna smidigt och effektivt sätt.

Använd din favorit MySQL-klient för att ansluta eller ansluter från en av de virtuella datorerna till att kontrollera att klustret fungerar.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Sedan skapar en databas och fylla det med vissa data.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Den databas du skapade returnerar följande tabell:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
Du har skapat en tre noder MariaDB + Galera Högtillgängliga kluster på Azure virtuella datorer körs CentOS 7 i den här artikeln. De virtuella datorerna belastningsutjämnas med Azure belastningsutjämnare.

Du kanske vill titta på [ett annat sätt att MySQL-kluster på Linux](mysql-cluster.md) och sätt att [optimera och testa MySQL prestanda på virtuella Azure Linux-datorer](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[skapa en SSH-nyckel för autentisering]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
