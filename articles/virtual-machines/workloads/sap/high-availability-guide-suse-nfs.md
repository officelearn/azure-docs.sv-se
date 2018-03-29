---
title: Hög tillgänglighet för NFS på Azure Virtual Machines på SUSE Linux Enterprise Server | Microsoft Docs
description: Hög tillgänglighet för NFS på Azure Virtual Machines på SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: sedusch
ms.openlocfilehash: b1a7b962d07b64aaa662aab937feed1782851a7b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Hög tillgänglighet för NFS på Azure Virtual Machines på SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurera virtuella datorer, installera kluster framework och installerar en högtillgänglig NFS-server som kan användas för att lagra de delade data på ett system med hög tillgänglighet SAP.
Den här guiden beskriver hur du installerar en högtillgänglig NFS-server som används av två SAP-system, NW1 och NW2. Namnen på de resurser (till exempel virtuella datorer, virtuella nätverk) i exemplet förutsätter att du har använt den [mall för SAP server] [ template-file-server] med resurs-prefix **prod**.

Läs följande SAP anteckningar och papper först

* SAP-kommentar [1928533], som innehåller:
  * Lista över Azure VM-storlekar som stöds för distribution av program
  * Viktiga kapacitetsinformation för Azure VM-storlekar
  * Stöds SAP-programvara och operativsystem (OS) och kombinationer av databasen
  * SAP kernel version som krävs för Windows och Linux i Microsoft Azure

* SAP-kommentar [2015553] listar kraven för stöd för SAP SAP programvarudistributioner i Azure.
* SAP-kommentar [2205917] har rekommenderat OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP-kommentar [1944799] har SAP HANA riktlinjer för SUSE Linux Enterprise Server för SAP-program
* SAP-kommentar [2178632] innehåller detaljerad information om all övervakning mått som rapporterats för SAP i Azure.
* SAP-kommentar [2191498] har Värdagenten för SAP-version som krävs för Linux i Azure.
* SAP-kommentar [2243692] har licensieringsinformation SAP på Linux i Azure.
* SAP-kommentar [1984787] har allmän information om SUSE Linux Enterprise Server 12.
* SAP-kommentar [1999351] innehåller ytterligare felsökningsinformation för Azure förbättrad övervakning av tillägget för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP anteckningar för Linux.
* [Azure virtuella datorer planering och implementering för SAP på Linux][planning-guide]
* [Distribution av Azure virtuella datorer för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP på Linux][dbms-guide]
* [Prestanda för SAP HANA SR optimerade Scenario][suse-hana-ha-guide]  
  Guiden innehåller all information som krävs för att ställa in SAP HANA System Replication lokala. Använd den här guiden som utgångspunkt.
* [Hög NFS lagringsutrymme med DRBD och Pacemaker] [ suse-drbd-guide] guiden innehåller all nödvändig information för att ställa in en NFS-server med hög tillgänglighet. Använd den här guiden som utgångspunkt.


## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet kräver SAP NetWeaver NFS-servern. NFS-servern är konfigurerad i separata kluster och kan användas av flera SAP-system.

![Översikt över SAP NetWeaver hög tillgänglighet](./media/high-availability-guide-nfs/ha-suse-nfs.png)

NFS-servern använder ett dedikerat virtuella värdnamn och virtuella IP-adresser för varje SAP-system som använder den här NFS-servern. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visar konfigurationen av belastningsutjämnaren.        

* Konfiguration för klientdel
  * IP-adressen 10.0.0.4 för NW1
  * IP-adress 10.0.0.5 för NW2
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i klustret för NFS
* Avsökningsport
  * Port 61000 för NW1
  * Port 61001 för NW2
* Regler för belastningsutjämning
  * 2049 TCP för NW1
  * UDP-2049 för NW1
  * 2049 TCP för NW2
  * UDP-2049 för NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurera en NFS-server med hög tillgänglighet

Du kan antingen använda en Azure-mall från github för att distribuera alla nödvändiga Azure-resurser, inklusive de virtuella datorerna, tillgänglighet ange och belastningsutjämnare eller du kan distribuera resurserna manuellt.

### <a name="deploy-linux-via-azure-template"></a>Distribuera Linux via Azure-mall

Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP program 12 som du kan använda för att distribuera nya virtuella datorer.
Du kan använda en av quickstart mallar på github för att distribuera alla nödvändiga resurser. Mallen distribuerar virtuella datorer, belastningsutjämnare, tillgänglighetsuppsättning osv. Följ dessa steg om du vill distribuera mallen:

1. Öppna den [mall för SAP server] [ template-file-server] i Azure-portalen   
1. Ange följande parametrar
   1. Resurs-Prefix  
      Ange det prefix som du vill använda. Värdet används som ett prefix för de resurser som har distribuerats.
   2. SAP System antal ange antalet SAP-system som ska använda den här filservern. Det här distribuerar det finns tillräckligt mycket klientdel konfigurationer, belastningsutjämningsregler, avsökningen portar, diskar osv.
   3. OS-typen  
      Välj en av Linux-distributioner. Det här exemplet väljer du SLES 12
   4. Användarnamn och lösenord för Admin administratör  
      En ny användare skapas som kan användas för att logga in på datorn.
   5. Undernät-ID  
      ID för det undernät som de virtuella datorerna ska anslutas till. Lämna tomt om du vill skapa ett nytt virtuellt nätverk eller välj undernätet i ditt VPN eller Expressroute virtuella nätverk att ansluta den virtuella datorn till ditt lokala nätverk. ID som ser oftast ut så /subscriptions/**&lt;prenumerations-ID&gt;**/resourceGroups/**&lt;resursgruppnamn&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;virtuella nätverksnamnet&gt;**/subnets/**&lt;undernätsnamn&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure-portalen

Du måste först skapa virtuella datorer för det här NFS-klustret. Därefter kan du skapa en belastningsutjämnare och använder de virtuella datorerna i backend-pooler.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en Tillgänglighetsuppsättning  
   Ange högsta uppdateringsdomän
1. Skapa virtuell dator 1   
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1 BYOS-bild https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES för SAP program 12 SP1 (BYOS) används  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Skapa virtuell dator 2   
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1 BYOS-bild https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES för SAP program 12 SP1 (BYOS) används  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Lägg till en datadisk för varje system för SAP både virtuella datorer.
1. Skapa en belastningsutjämnare (internt)  
   1. Skapa IP-adresser för klientdel
      1. IP-adressen 10.0.0.4 för NW1
         1. Öppna belastningsutjämnaren, Välj klientdelens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya klientdelens IP-poolen (till exempel **nw1 klientdel**)
         1. Ange tilldelningen som statisk och ange IP-adress (till exempel **10.0.0.4**)
         1. Klicka på OK         
      1. IP-adress 10.0.0.5 för NW2
         * Upprepa stegen ovan för NW2
   1. Skapa backend-pooler
      1. Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i klustret NFS för NW1
         1. Öppna belastningsutjämnaren, Välj serverdelspooler och klicka på Lägg till
         1. Ange namnet på den nya serverdelspoolen (till exempel **nw1 backend**)
         1. Klicka på Lägg till en virtuell dator
         1. Välj Tillgänglighetsuppsättningen som du skapade tidigare
         1. Välj de virtuella datorerna i klustret för NFS
         1. Klicka på OK
      1. Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i klustret NFS för NW2
         * Upprepa stegen ovan för att skapa en serverdelspool för NW2
   1. Skapa hälsoavsökningar
      1. Port 61000 för NW1
         1. Öppna belastningsutjämnaren, Välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **nw1 hp**)
         1. Välj TCP som protokoll, port 610**00**, hålla intervallet 5 och tröskelvärde för ohälsosamt värde 2
         1. Klicka på OK
      1. Port 61001 för NW2
         * Upprepa stegen ovan för att skapa en hälsoavsökningen för NW2
   1. Regler för belastningsutjämning
      1. 2049 TCP för NW1
         1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
         1. Ange namnet på den nya regeln för belastningsutjämnare (till exempel **nw1-lb-2049**)
         1. Välj klientdelens IP-adress, serverdelspool och hälsoavsökningen som du skapade tidigare (till exempel **nw1 klientdel**)
         1. Behåll protokollet **TCP**, ange port **2049**
         1. Öka tidsgränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK    
      1. UDP-2049 för NW1
         * Upprepa stegen ovan för- och UDP-port 2049 för NW1
      1. 2049 TCP för NW2
         * Upprepa stegen ovan för- och TCP-port 2049 för NW2
      1. UDP-2049 för NW2
         * Upprepa stegen ovan för- och UDP-port 2049 för NW2

### <a name="create-pacemaker-cluster"></a>Skapa Pacemaker kluster

Följ stegen i [konfigurerar Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) att skapa ett grundläggande Pacemaker kluster för NFS-server.

### <a name="configure-nfs-server"></a>Konfigurera NFS-servern

Följande objekt är prefixet antingen **[A]** - gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Installationsprogrammet värdnamnsmatchning   

   Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Aktivera NFS-servern

   Skapa root export-post, startar NFS-server och aktivera autostart

   <pre><code>
   sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/

   sudo systemctl enable nfsserver
   sudo service nfsserver restart
   </code></pre>

1. **[A]**  Installera drbd komponenter

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Skapa en partition för drbd-enheter

   Visa en lista över alla tillgängliga datadiskar

   <pre><code>
   sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Exempel på utdata
   
   ```
   lun0  lun1
   ```

   Skapa partitioner för varje datadisk

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  Skapa LVM konfigurationer

   Visa en lista över alla tillgängliga partitioner

   <pre><code>
   ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Exempel på utdata
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Skapa LVM volymer för varje partition

   <pre><code>
   sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Konfigurera drbd

   <pre><code>
   sudo vi /etc/drbd.conf
   </code></pre>

   Kontrollera att filen drbd.conf innehåller följande två rader

   <pre><code>
   include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Ändra globala drbd-konfiguration

   <pre><code>
   sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Lägg till följande poster i avsnittet hanterare och net.

   <pre><code>
   global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             resync-rate 50M;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
        }
   }
   </code></pre>

1. **[A]**  Skapa NFS drbd enheter

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd enheten och avsluta

   <pre><code>
   resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd enheten och avsluta

   <pre><code>
   resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Skapa drbd enheten och starta den

   <pre><code>
   sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Hoppa över inledande synkronisering

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Anger den primära noden

   <pre><code>
   sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Vänta tills nya drbd enheter som ska synkroniseras

   <pre><code>
   sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Skapa filsystem på drbd-enheter

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]**  Konfigurera drbd med delad hjärna identifiering

   När du använder drbd för att synkronisera data från en värd till en annan, kan det uppstå en så kallad delad hjärna. En delad hjärna är ett scenario där båda klusternoderna upphöja drbd enheten så att den primära servern och gick synkroniserat. Det kan vara mycket sällsynt situation men du ändå vill hantera och lösa en delad hjärna så snabbt som möjligt. Det är därför viktigt att du meddelas när en delad hjärna inträffade.

   Läs [officiella drbd dokumentationen](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) på hur du ställer in en delad hjärna avisering.

   Det är också möjligt att automatiskt återställa från ett scenario med delad hjärna. Mer information finns [automatisk delad hjärna recovery principer](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurera klustret Framework

1. **[1]**  Lägga till NFS drbd enheter för SAP NW1 till klusterkonfigurationen

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=3 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=4 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=5 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=6 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=7 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=8 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> exportfs_<b>NW1</b>_sidsys \
     exportfs_<b>NW1</b>_sapmntsid exportfs_<b>NW1</b>_trans exportfs_<b>NW1</b>_ASCS \
     exportfs_<b>NW1</b>_ASCSERS exportfs_<b>NW1</b>_SCS exportfs_<b>NW1</b>_SCSERS \
     nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Lägga till NFS drbd enheter för SAP NW2 till klusterkonfigurationen

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true   
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=9 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=10 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=11 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=12 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=13 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=14 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=15 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=16 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> exportfs_<b>NW2</b>_sidsys \
     exportfs_<b>NW2</b>_sapmntsid exportfs_<b>NW2</b>_trans exportfs_<b>NW2</b>_ASCS \
     exportfs_<b>NW2</b>_ASCSERS exportfs_<b>NW2</b>_SCS exportfs_<b>NW2</b>_SCSERS \
     nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Inaktivera underhållsläge
   
   <pre><code>
   sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Nästa steg
* [Installera SAP ASCS och databas](high-availability-guide-suse.md)
* [Azure virtuella datorer planering och implementering för SAP][planning-guide]
* [Distribution av Azure virtuella datorer för SAP][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA på virtuella Azure-datorer med hög tillgänglighet finns [hög tillgänglighet för SAP HANA på Azure Virtual Machines (virtuella datorer)][sap-hana-ha]
