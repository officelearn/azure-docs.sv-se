---
title: "Clusterize MySQL med belastningsutjämnade uppsättningar | Microsoft Docs"
description: "Konfigurera en belastningsutjämnad hög tillgänglighet Linux MySQL-kluster skapas med den klassiska distributionsmodellen på Azure"
services: virtual-machines-linux
documentationcenter: 
author: bureado
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: 4eaf86c9ac3e4dc2b51b88383626eda774cab0e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Använda belastningsutjämnade uppsättningar till clusterize MySQL på Linux
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager](../../../resource-manager-deployment-model.md) och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. En [Resource Manager-mall](https://azure.microsoft.com/documentation/templates/mysql-replication/) är tillgänglig om du behöver distribuera en MySQL-kluster.

Den här artikeln utforskar och visar olika metoder som är tillgängliga för distribution av Linux-baserade tjänster i Microsoft Azure, utforska MySQL hög tillgänglighet som en introduktion med hög tillgänglighet. En video som visar den här metoden är tillgängligt på [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Vi kommer disposition delat ingenting, två noder, single master MySQL lösning för hög tillgänglighet baserat på DRBD, Corosync och Pacemaker. Endast en nod kör MySQL i taget. Läsning och skrivning från DRBD resursen är också begränsad till endast en nod i taget.

Det finns inget behov av en VIP-lösning som LVS, eftersom du använder belastningsutjämnade uppsättningar i Microsoft Azure för att tillhandahålla resursallokering funktioner och slutpunkten identifiering, borttagning och korrekt återställning av VIP. VIP-Adressen är en globalt dirigeras IPv4-adress som tilldelats av Microsoft Azure när du skapar Molntjänsten.

Det finns andra möjliga arkitekturer för MySQL, inklusive nästa arbetsdag kluster, Percona, Galera och flera mellanprogram lösningar, inklusive minst en tillgänglig som en virtuell dator på [VM anläggningen](http://vmdepot.msopentech.com). Så länge dessa lösningar kan replikera på unicast kontra multicast eller broadcast inte och på delad lagring eller flera nätverksgränssnitt, bör scenarier vara enkelt att distribuera på Microsoft Azure.

Dessa kluster arkitekturer kan utökas med andra produkter som PostgreSQL och OpenLDAP på ett liknande sätt. Till exempel belastningsutjämning proceduren med delat ingenting har har testats med flera master OpenLDAP och du kan titta på den på vår Channel 9-bloggen.

## <a name="get-ready"></a>Gör dig redo
Du behöver följande resurser och funktioner:

  - Microsoft Azure-konto med en giltig prenumeration kan du skapa minst två virtuella datorer (XS användes i det här exemplet)
  - Ett nätverk och ett undernät
  - En tillhörighetsgrupp
  - En tillgänglighetsuppsättning
  - Möjligheten att skapa virtuella hårddiskar i samma region som Molntjänsten och koppla dem till den virtuella Linux-datorer

### <a name="tested-environment"></a>Testad miljö
* Ubuntu 13.10
  * DRBD
  * MySQL-Server
  * Corosync och Pacemaker

### <a name="affinity-group"></a>Tillhörighetsgruppen
Skapa en tillhörighetsgrupp för lösningen genom att logga in på den klassiska Azure-portalen att välja **inställningar**, och skapa en tillhörighetsgrupp. Resurserna som skapas senare ska tilldelas den här tillhörighetsgrupp.

### <a name="networks"></a>Nätverk
Ett nytt nätverk skapas och skapas ett undernät i nätverket. Det här exemplet använder ett 10.10.10.0/24 nätverk med endast en /24 undernät i.

### <a name="virtual-machines"></a>Virtuella datorer
Den första virtuella datorn 13.10 Ubuntu skapas med hjälp av en bild Endorsed Ubuntu galleriet och kallas `hadb01`. En ny molntjänst skapas i den process som kallas hadb. Det här namnet visas delade, Utjämning av nätverksbelastning art som tjänsten ska ha när du lägger till fler resurser. Skapandet av `hadb01` är primärdomänkontrollant och slutförda med hjälp av portalen. En slutpunkt för SSH skapas automatiskt och det nya nätverket är markerad. Nu kan du skapa en tillgänglighetsuppsättning för de virtuella datorerna.

Efter den första virtuella datorn skapas (tekniskt sett när Molntjänsten skapas), kan du skapa den andra Virtuellt `hadb02`. För andra Virtuella Ubuntu 13.10 VM från galleriet med hjälp av portalen, men använda en befintlig molntjänst `hadb.cloudapp.net`, i stället för att skapa en ny. Ange nätverks- och tillgänglighet måste väljas automatiskt. En SSH-slutpunkt skapas för.

När båda VM: ar har skapats, anteckna SSH-porten för `hadb01` (TCP 22) och `hadb02` (tilldelade automatiskt av Azure).

### <a name="attached-storage"></a>Direktansluten lagring
Koppla en ny disk till både virtuella datorer och skapa 5 GB-diskar i processen. Diskarna som finns i VHD-behållare för huvudsakliga operativsystem-diskar. När diskar har skapats och ansluten, är det behöver inte starta om Linux eftersom kernel visas den nya enheten. Den här enheten är vanligtvis `/dev/sdc`. Kontrollera `dmesg` för utdata.

Skapa en partition på varje virtuell dator med hjälp av `cfdisk` (primära Linux partition) och Skriv den nya partitionstabellen. Skapa ett filsystem på den här partitionen.

## <a name="set-up-the-cluster"></a>Konfigurera klustret
Använd LGH för att installera Corosync och Pacemaker DRBD på båda Ubuntu virtuella datorer. Att göra det med `apt-get`, kör följande kod:

    sudo apt-get install corosync pacemaker drbd8-utils.

Installera inte MySQL just nu. Debian och Ubuntu installationsskript initiera en MySQL-datakatalog på `/var/lib/mysql`, men eftersom katalogen kommer ersättas av ett DRBD filsystem, måste du installera MySQL senare.

Kontrollera (med hjälp av `/sbin/ifconfig`) att både virtuella datorer använder adresser i undernätet 10.10.10.0/24 och att de kan pinga varandra efter namn. Du kan också använda `ssh-keygen` och `ssh-copy-id` att kontrollera att båda VM: ar kan kommunicera via SSH utan att kräva ett lösenord.

### <a name="set-up-drbd"></a>Ställ in DRBD
Skapa en resurs för DRBD som använder den underliggande `/dev/sdc1` partition för att skapa en `/dev/drbd1` resurs som formaterats med ext3 och används i både primära och sekundära noder.

1. Öppna `/etc/drbd.d/r0.res` och kopiera följande resursdefinitionen på både virtuella datorer:

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Initiera resursen med hjälp av `drbdadm` på både virtuella datorer:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. På den primära virtuella datorn (`hadb01`), force ägarskapet (primär) för resursen DRBD:

        sudo drbdadm primary --force r0

Om du undersöker innehållet i proc/drbd (`sudo cat /proc/drbd`) på båda VM: ar, bör du se `Primary/Secondary` på `hadb01` och `Secondary/Primary` på `hadb02`och konsekvent med lösningen nu. 5 GB disk är synkroniserad över nätverket 10.10.10.0/24 utan kostnad för kunder.

När disken synkroniseras, kan du skapa filsystemet på `hadb01`. I testsyfte kan vi använde ext2, men följande kod skapar ett ext3 filsystem:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Montera DRBD resursen
Nu är du redo att montera DRBD resurser på `hadb01`. Debian och derivat Använd `/var/lib/mysql` som Mysqls datakatalog. Eftersom du inte har installerat MySQL skapa katalogen och montera DRBD resursen. Kör följande kod för att utföra det här alternativet `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Ställ in MySQL
Nu är du redo att installera MySQL på `hadb01`:

    sudo apt-get install mysql-server

För `hadb02`, har du två alternativ. Du kan installera mysql-servern, vilket skapar /var/lib/mysql, Fyll den med en ny datakatalog, och ta sedan bort innehållet. Kör följande kod för att utföra det här alternativet `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

Det andra alternativet är ska gå över till `hadb02` och sedan installera mysql-server det. Installationsskript ser den befintliga installationen och påverkas inte.

Kör följande kod `hadb01`:

    sudo drbdadm secondary –force r0

Kör följande kod `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Om du inte tänker redundans DRBD nu, är det första alternativet enklare även om tvekan mindre elegant. När du har du skapat kan börja du arbeta på MySQL-databas. Kör följande kod `hadb02` (eller vilken en av servrarna är aktiv, enligt DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Det här sista instruktionen inaktiverar autentisering för rotanvändaren i den här tabellen. Detta bör ersättas med din produktion klass ge instruktioner och ingår endast som illustration.

Om du vill göra frågor från utanför de virtuella datorerna (vilket är syftet med den här guiden) måste du också aktivera nätverk för MySQL. Öppna på båda VM: ar, `/etc/mysql/my.cnf` och gå till `bind-address`. Ändra adressen från 127.0.0.1 som 0.0.0.0. När du har sparat filen utfärda en `sudo service mysql restart` på din aktuella primära.

### <a name="create-the-mysql-load-balanced-set"></a>Skapa MySQL belastningsutjämnad uppsättning
Gå tillbaka till portalen, går du till `hadb01`, och välj **slutpunkter**. Om du vill skapa en slutpunkt, välj MySQL (TCP 3306) från den nedrullningsbara listrutan och välj **skapa nya belastningsutjämnade uppsättningen**. Namnge belastningsutjämnade slutpunkten `lb-mysql`. Ange **tid** till 5 sekunder minsta.

När du skapar slutpunkten, går du till `hadb02`, Välj **slutpunkter**, och skapa en slutpunkt. Välj `lb-mysql`, och välj sedan MySQL från den nedrullningsbara listan. Du kan också använda Azure CLI för det här steget.

Nu har du allt du behöver för manuell åtgärd i klustret.

### <a name="test-the-load-balanced-set"></a>Testa den belastningsutjämnade uppsättningen
Testerna kan utföras från en extern dator med hjälp av en MySQL-klient eller med hjälp av vissa program som körs som en Azure-webbplats phpMyAdmin. I så fall måste använt du Mysqls kommandoradsverktyget på en annan Linux ruta:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Manuellt växling
Du kan simulera redundans genom att stänga av MySQL, växla DRBDS primära och starta MySQL igen.

Kör följande kod för att utföra den här uppgiften på hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Klicka på hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

När du manuellt växla över du kan upprepa remote frågan och bör fungerar perfekt.

## <a name="set-up-corosync"></a>Ställ in Corosync
Corosync är den underliggande klusterinfrastruktur som krävs för Pacemaker ska fungera. Corosync är en delning av CRM-funktioner medan Pacemaker är mer liknar pulsslag i funktionen för pulsslag (och andra metoder som Ultramonkey).

Den huvudsakliga begränsningen för Corosync i Azure är att Corosync föredrar multicast över broadcast över unicast-kommunikation, men Microsoft Azure-nätverk stöder endast unicast.

Lyckligtvis har Corosync en fungerande unicast-läge. Endast verkliga begränsningen är eftersom noderna inte kan kommunicera med varandra, måste du definiera noderna i konfigurationsfilerna, inklusive deras IP-adresser. Vi kan använda Corosync exempel filer för unicast- och ändrar binda adress, nod listor och loggning kataloger (Ubuntu använder `/var/log/corosync` medan exemplet filer Använd `/var/log/cluster`), och aktivera kvorum verktyg.

> [!NOTE]
> Använd följande `transport: udpu` direktiv och manuellt definierade IP-adresser för båda noderna.

Kör följande kod `/etc/corosync/corosync.conf` för båda noderna:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Kopiera konfigurationsfilen på både virtuella datorer och starta Corosync i båda noderna:

    sudo service start corosync

Klustret ska fastställas i den aktuella ringen strax efter att starta tjänsten och kvorum bör bildas. Vi kan kontrollera den här funktionen genom att granska loggarna eller genom att köra följande kod:

    sudo corosync-quorumtool –l

Du kommer se utdata som liknar följande bild:

![exempel på utdata från corosync quorumtool - l](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Ställ in Pacemaker
Pacemaker använder klustret för att övervaka resurser, definiera när primärfärgerna kraschar och växla resurserna till sekundärservrar. Du kan definiera resurser från en uppsättning tillgängliga skript eller LSB (init-liknande) skript, bland andra alternativ.

Vi vill Pacemaker till ”äger” DRBD resursen, monteringspunkten och MySQL-tjänsten. Om Pacemaker kan aktivera och inaktivera DRBD, montera och demontera den, och sedan starta och stoppa MySQL i rätt ordning när något händer med primärt, är installationen klar.

När du installerar Pacemaker ska konfigurationen vara enkelt, exempelvis:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Kontrollera konfigurationen genom att köra `sudo crm configure show`.
2. Skapa en fil (t.ex. `/tmp/cluster.conf`) med följande resurser:

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Läsa in filen i konfigurationen. Du behöver bara göra detta på en nod.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Kontrollera att Pacemaker startar vid start i båda noderna:

        sudo update-rc.d pacemaker defaults

5. Med hjälp av `sudo crm_mon –L`, kontrollera att en av noderna har bli hanterare för klustret och kör alla resurser. Du kan använda montera och ps för att kontrollera att resurserna som körs.

Följande skärmbild visar `crm_mon` med en nod som stoppats (avsluta genom att välja Ctrl + C):

![crm_mon noden stoppades](./media/mysql-cluster/image002.png)

Den här skärmbilden visar noder, en överordnad och en slav:

![crm_mon operativa över-/ underordnade](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Testning
Du är redo för att simulera en automatisk redundans. Det finns två sätt att göra detta: mjuk och hårddisk.

Det mjuka sättet klustrets avstängning funktionen: ``crm_standby -U `uname -n` -v on``. Om du använder du i bakgrunden, tar underordnade över. Kom ihåg att konfigurera detta OFF. Om du inte visas crm_mon en nod i vänteläge.

Hur hårddisk stängs av den primära virtuella datorn (hadb01) via portalen eller genom att ändra är på den virtuella datorn (det vill säga stopp, avstängning). Detta hjälper Corosync och Pacemaker av signalering som huvudservern gå nedåt. Du kan testa detta (användbart för underhållsfönster), men du kan också tvinga scenariot genom att frysa den virtuella datorn.

## <a name="stonith"></a>STONITH
Det bör vara möjligt att utfärda en VM avstängning via Azure CLI i stället för ett STONITH skript som styr en fysisk enhet. Du kan använda `/usr/lib/stonith/plugins/external/ssh` som en bas- och aktivera STONITH i klustrets konfiguration. Azure CLI globalt ska installeras och publiceringsinställningarna och profil ska läsas in för användaren i klustret.

Exempelkod för resursen är tillgänglig på [GitHub](https://github.com/bureado/aztonith). Ändra klustrets konfiguration genom att lägga till följande `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> Skriptet utföra inte upp/ned kontroller. Den ursprungliga SSH-resursen hade 15 ping-kontroller, men tiden för återställning för en virtuell dator i Azure kan vara mer variabel.

## <a name="limitations"></a>Begränsningar
Följande begränsningar:

* Linbit DRBD resurs skriptet som hanterar DRBD som en resurs i Pacemaker använder `drbdadm down` när du stänger en nod, även om noden ska bara i vänteläge. Detta är inte idealiskt eftersom underordnade inte kommer att synkronisera resursen DRBD medan huvudservern hämtar skrivningar. Om huvudservern inte misslyckas graciously, kan underordnade ta över en äldre tillståndet för filsystemet. Det finns två möjliga sätt att lösa det här:
  * Framtvinga en `drbdadm up r0` i alla noder i klustret via ett lokalt (inte clusterized) watchdog
  * Redigera skriptet linbit DRBD att se till att `down` inte anropas`/usr/lib/ocf/resource.d/linbit/drbd`
* Belastningsutjämnaren måste minst fem sekunder att svara så att program ska vara kluster och vara mer tolerant för timeout. Andra arkitekturer som app-köer och fråga middlewares kan också.
* Inställning av MySQL är nödvändigt för att säkerställa att skrivning utförs i en hanterbar takt och cacheminnen rensade så ofta som möjligt för att minimera minne på disk.
* Skriva prestanda beror på virtuell dator sammankoppling i den virtuella växeln eftersom det här är den mekanism som används av DRBD för att replikera enheten.
