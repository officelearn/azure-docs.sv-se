---
title: Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server | Microsoft Docs
description: Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: a91bc1cbb72427205cc558a4b5e655f4aa8083b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710730"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Den här artikeln beskriver hur du distribuerar de virtuella datorerna, konfigurera virtuella datorer, installera kluster framework och installera en högtillgänglig NFS-server som kan användas för lagring av delade data för en högtillgänglig SAP-system.
Den här guiden beskriver hur du konfigurerar en högtillgänglig NFS-server som används av två SAP-system, NW1 och NW2. Namnen på resurserna (till exempel virtuella datorer, virtuella nätverk) i det här exemplet förutsätter att du har använt den [SAP server Filmall] [ template-file-server] med resursen prefix **prod**.

Läs följande SAP Notes och papers först

* SAP-kommentar [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-program
  * Viktiga kapacitetsinformation för Azure VM-storlekar
  * SAP-program som stöds, och operativsystem (OS) och kombinationer av databas
  * Nödvändiga SAP kernel-version för Windows och Linux på Microsoft Azure

* SAP-kommentar [2015553] visar en lista över kraven för distribution av SAP-stöd för SAP-programvara i Azure.
* SAP-kommentar [2205917] rekommenderar OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP-kommentar [1944799] har SAP HANA riktlinjer för SUSE Linux Enterprise Server för SAP-program
* SAP-kommentar [2178632] mer information om all övervakning mått som rapporterats för SAP i Azure.
* SAP-kommentar [2191498] har Värdagenten för SAP-version som krävs för Linux i Azure.
* SAP-kommentar [2243692] har licensieringsinformation SAP på Linux i Azure.
* SAP-kommentar [1984787] har allmän information om SUSE Linux Enterprise Server 12.
* SAP-kommentar [1999351] innehåller ytterligare felsökningsinformation för Azure förbättrad övervakning av tillägget för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Azure virtuella datorer, planering och implementering av SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SUSE Linux Enterprise hög tillgänglighet tillägget 12 SP3 bästa guider med metodtips][sles-hae-guides]
  * Med hög tillgänglighet NFS-lagring med DRBD och Pacemaker
* [SUSE Linux Enterprise Server för SAP-program 12 SP3 bästa guider med metodtips][sles-for-sap-bp]
* [SUSE hög tillgänglighet tillägget 12 SP3 viktig information][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet kräver SAP NetWeaver NFS-servern. NFS-servern har konfigurerats i ett separat kluster och kan användas av flera SAP-system.

![Översikt över SAP NetWeaver hög tillgänglighet](./media/high-availability-guide-nfs/ha-suse-nfs.png)

NFS-servern använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser för varje SAP-system som använder den här NFS-servern. På Azure måste en belastningsutjämnare använda en virtuell IP-adress. I följande lista visar konfigurationen för belastningsutjämnaren.        

* Konfiguration för klientdel
  * IP-adressen 10.0.0.4 för NW1
  * IP-adress 10.0.0.5 för NW2
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska vara en del av NFS-kluster
* Avsökningsport
  * Port 61000 för NW1
  * Port 61001 för NW2
* Med regler
  * 2049 TCP för NW1
  * 2049 UDP för NW1
  * 2049 TCP för NW2
  * 2049 UDP för NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurera en NFS-server med hög tillgänglighet

Du kan antingen använda en Azure-mall från GitHub för att distribuera alla nödvändiga Azure-resurser, inklusive de virtuella datorerna, tillgänglighet, ange och belastningsutjämnare eller du kan distribuera resurserna manuellt.

### <a name="deploy-linux-via-azure-template"></a>Distribuera Linux via Azure-mall

Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella datorer.
Du kan använda en av snabbstartsmallarna på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar virtuella datorer, belastningsutjämnare, tillgänglighetsuppsättning osv. Följ dessa steg om du vill distribuera mallen:

1. Öppna den [SAP server Filmall] [ template-file-server] i Azure portal   
1. Ange följande parametrar
   1. Resurs-Prefix  
      Ange det prefix som du vill använda. Värdet används som ett prefix för de resurser som distribueras.
   2. Antal för SAP-System  
      Ange hur många SAP-system som ska använda den här filservern. Detta kommer att distribuera det finns tillräckligt mycket av konfigurationer för klientdel, belastningsutjämningsregler, avsökning ports är diskar osv.
   3. OS-typ  
      Välj en av Linux-distributioner. I det här exemplet väljer du SLES 12
   4. Administratörens användarnamn och lösenord för serveradministratören  
      En ny användare skapas som kan användas för att logga in på datorn.
   5. Undernät-ID  
      Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T ser oftast ut så /subscriptions/**&lt;prenumerations-ID&gt;**/resourceGroups/**&lt;Resursgruppsnamn&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;virtuella nätverksnamnet&gt;**/subnets/**&lt;undernätsnamn&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure-portalen

Du måste först skapa de virtuella datorerna för det här NFS-klustret. Därefter kan du skapa en belastningsutjämnare och använder du virtuella datorer i backend-pooler.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en Tillgänglighetsuppsättning  
   Ställ in max uppdateringsdomän
1. Skapa virtuell dator 1 Använd minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3 BYOS bild SLES för SAP program 12 SP3 (BYOS) används  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Skapa virtuell dator 2 med minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3 BYOS-bild  
   SLES för SAP program 12 SP3 (BYOS) används  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Lägga till en datadisk för alla SAP-system till båda de virtuella datorerna.
1. Skapa en belastningsutjämnare (internt)  
   1. Skapa frontend-IP-adresser
      1. IP-adressen 10.0.0.4 för NW1
         1. Öppna belastningsutjämnaren, Välj IP-adresspool på klientdelen och klicka på Lägg till
         1. Ange namnet på den nya frontend IP-poolen (till exempel **nw1 klientdel**)
         1. Ange tilldelningen till statiskt och ange IP-adressen (till exempel **10.0.0.4**)
         1. Klicka på OK
      1. IP-adress 10.0.0.5 för NW2
         * Upprepa stegen ovan för NW2
   1. Skapa backend-adresspooler
      1. Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i klustret NFS för NW1
         1. Öppna belastningsutjämnaren, Välj serverdelspooler och klicka på Lägg till
         1. Ange namnet på den nya backend-poolen (till exempel **nw1-backend**)
         1. Klicka på Lägg till en virtuell dator
         1. Välj Tillgänglighetsuppsättning som du skapade tidigare
         1. Välj de virtuella datorerna i klustret för NFS
         1. Klicka på OK
      1. Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i klustret NFS för NW2
         * Upprepa stegen ovan för att skapa en serverdelspool för NW2
   1. Skapa hälsoavsökningarna
      1. Port 61000 för NW1
         1. Öppna belastningsutjämnaren, Välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **nw1 hp**)
         1. Välj TCP som protokoll, port 610**00**, se till att intervallet 5 och tröskelvärde för ej felfri 2
         1. Klicka på OK
      1. Port 61001 för NW2
         * Upprepa stegen ovan för att skapa en hälsoavsökning för NW2
   1. Med regler
      1. 2049 TCP för NW1
         1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
         1. Ange namnet på den nya belastningsutjämningsregeln (till exempel **nw1-lb-2049**)
         1. Välj klientdelens IP-adress, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **nw1 klientdel**)
         1. Behåll protokollet **TCP**, ange port **2049**
         1. Öka tidsgränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
      1. 2049 UDP för NW1
         * Upprepa stegen ovan för- och UDP-port 2049 för NW1
      1. 2049 TCP för NW2
         * Upprepa stegen ovan för port 2049 och TCP för NW2
      1. 2049 UDP för NW2
         * Upprepa stegen ovan för- och UDP-port 2049 för NW2

> [!IMPORTANT]
> Aktivera inte TCP tidsstämplarna för virtuella Azure-datorer är placerade bakom Azure Load Balancer. Aktivera TCP tidsstämplar genereras hälsoavsökningar misslyckas. Ange parametern **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [hälsoavsökningar för belastningsutjämnaren](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Skapa Pacemaker kluster

Följ stegen i [konfigurerar Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) att skapa ett grundläggande Pacemaker kluster för den här NFS-servern.

### <a name="configure-nfs-server"></a>Konfigurera NFS-server

Följande objekt har prefixet antingen **[A]** – gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Konfigurera matcha värdnamn

   Du kan använda en DNS-server, eller så kan du ändra i/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet till matchar din miljö
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Aktivera NFS-server

   Skapa NFS export rot

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]**  Installera drbd komponenter

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Skapa en partition för drbd-enheter

   Lista över alla tillgängliga datadiskar

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Exempel på utdata
   
   ```
   lun0  lun1
   ```

   Skapa partitioner för varje datadisk

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  Skapa LVM-konfigurationer

   Lista över alla tillgängliga partitioner

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Exempel på utdata
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Skapa LVM volymer för varje partition

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Konfigurera drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Kontrollera att filen drbd.conf innehåller följande två rader

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Ändra globala drbd-konfiguration

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Lägg till följande poster-hanteraren och net avsnitt.

   <pre><code>global {
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
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]**  Skapa NFS drbd enheter

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd enheten och avsluta

   <pre><code>resource <b>NW1</b>-nfs {
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

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd enheten och avsluta

   <pre><code>resource <b>NW2</b>-nfs {
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

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Hoppa över inledande synkronisering

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Ange den primära noden

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Vänta tills de nya drbd enheterna synkroniseras

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Skapa filsystem på drbd-enheter

   <pre><code>sudo mkfs.xfs /dev/drbd0
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

1. **[A]**  Konfigurera drbd split-brain identifiering

   När du använder drbd för att synkronisera data från en värd till en annan, kan en så kallad split-brain inträffa. En delad hjärna är ett scenario där båda klusternoderna befordras drbd enheten för att vara primärt och gick synkroniserad. Det kan vara en sällsynta situationen men du ändå vill hantera och lösa en delad hjärna så fort som möjligt. Det är därför viktigt att du meddelas när en split-brain inträffade.

   Läs [officiella drbd dokumentationen](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) på hur du ställer in ett meddelande om delad hjärna.

   Det är också möjligt att återställa automatiskt från ett scenario med delad hjärna. Mer information finns [automatisk delad hjärna recovery principer](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurera klustret Framework

1. **[1]**  Lägg till enheter för NFS-drbd för SAP-system NW1 klusterkonfigurationen

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

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
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Lägg till enheter för NFS-drbd för SAP-system NW2 klusterkonfigurationen

   <pre><code># Enable maintenance mode
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
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Inaktivera underhållsläge
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Installera SAP ASCS och databas](high-availability-guide-suse.md)
* [Azure virtuella datorer, planering och implementering av SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure (stora instanser) i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure Virtual Machines i [hög tillgänglighet för SAP HANA på Azure Virtual Machines (VM)][sap-hana-ha]
