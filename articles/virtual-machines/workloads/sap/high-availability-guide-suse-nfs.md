---
title: Hög tillgänglighet för NFS på virtuella Azure-datorer på SLES | Microsoft-dokument
description: Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 4dce0a675f5841591da00a322b72718964d382ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348879"
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

I den här artikeln beskrivs hur du distribuerar de virtuella datorerna, konfigurerar virtuella datorer, installerar klusterramverket och installerar en NFS-server med hög tillgänglighet som kan användas för att lagra delade data från ett SAP-system med hög tillgänglighet.
I den här guiden beskrivs hur du konfigurerar en NFS-server med högtillgänge som används av två SAP-system, NW1 och NW2. Namnen på resurserna (till exempel virtuella datorer, virtuella nätverk) i exemplet förutsätter att du har använt [SAP-filservermallen][template-file-server] med resursprefix **prod**.

Läs följande SAP-anteckningar och -dokument först

* SAP Note [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-programvara
  * Viktig kapacitetsinformation för Azure VM-storlekar
  * Sap-programvara och kombinationer av operativsystem (operativsystem) och databas
  * Nödvändig SAP-kärnversion för Windows och Linux på Microsoft Azure

* SAP Note [2015553] listar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure.
* SAP Note [2205917] har rekommenderat OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP Note [1944799] har SAP HANA Riktlinjer för SUSE Linux Enterprise Server för SAP-program
* SAP Note [2178632] har detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [2191498] har den nödvändiga SAP Host Agent-versionen för Linux i Azure.
* SAP Note [2243692] har information om SAP-licensiering på Linux i Azure.
* SAP Note [1984787] har allmän information om SUSE Linux Enterprise Server 12.
* SAP Note [1999351] har ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Planering och implementering av Virtuella Azure-datorer för SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SUSE Linux Enterprise Hög tillgänglighet Extension 12 SP3 bästa praxis guider][sles-hae-guides]
  * NFS-lagring med högtillgänge med DRBD och Pacemaker
* [SUSE Linux Enterprise Server för SAP-program 12 SP3 bästa praxis guider][sles-for-sap-bp]
* [SUSE hög tillgänglighet Tillägg 12 SP3 viktig information][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet kräver SAP NetWeaver en NFS-server. NFS-servern är konfigurerad i ett separat kluster och kan användas av flera SAP-system.

![ÖVERSIKT ÖVER HÖG TILLGÄNGLIGHET I SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

NFS-servern använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser för varje SAP-system som använder den här NFS-servern. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Följande lista visar konfigurationen av belastningsutjämnaren.        

* Frontend-konfiguration
  * IP-adress 10.0.0.4 för NW1
  * IP-adress 10.0.0.5 för NW2
* Serverningskonfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i NFS-klustret
* Avsökningsport
  * Port 61000 för NW1
  * Port 61001 för NW2
* Regler för belastningsutjämning (om du använder grundläggande belastningsutjämnare)
  * 2049 TCP för NW1
  * 2049 UDP för NW1
  * 2049 TCP för NW2
  * 2049 UDP för NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurera en NFS-server med högtillgänge

Du kan antingen använda en Azure-mall från GitHub för att distribuera alla nödvändiga Azure-resurser, inklusive virtuella datorer, tillgänglighetsuppsättning och belastningsutjämnare eller så kan du distribuera resurserna manuellt.

### <a name="deploy-linux-via-azure-template"></a>Distribuera Linux via Azure-mall

Azure Marketplace innehåller en avbildning för SUSE Linux Enterprise Server för SAP Applications 12 som du kan använda för att distribuera nya virtuella datorer.
Du kan använda en av snabbstartsmallarna på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighetsuppsättningen etc. Så här distribuerar du mallen:

1. Öppna [SAP-filservermallen][template-file-server] i Azure-portalen   
1. Ange följande parametrar
   1. Resursprefix  
      Ange det prefix som du vill använda. Värdet används som prefix för de resurser som distribueras.
   2. Antal SAP-system  
      Ange antalet SAP-system som ska använda den här filservern. Detta kommer att distribuera den erforderliga mängden frontend konfigurationer, belastningsutjämning regler, avsökningsportar, diskar etc.
   3. Typ av os  
      Välj en av Linux-distributionerna. I det här exemplet väljer du SLES 12
   4. Administratörsanvändarnamn och administratörslösenord  
      En ny användare skapas som kan användas för att logga in på datorn.
   5. Nät-ID  
      Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät som definierats ska den virtuella datorn tilldelas, namnge ID:t för det specifika undernätet. ID:t ser vanligtvis ut som /subscriptions/subscription**&lt;ID&gt;**/resourceGroups/**&lt;resource group name&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;virtual network name&gt;**/subnets/**&lt;undernätsnamn&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure Portal

Du måste först skapa de virtuella datorerna för det här NFS-klustret. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i serverdapoolerna.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en tillgänglighetsuppsättning  
   Ange maxuppdateringsdomän
1. Skapa virtuell dator 1 Använd minst SLES4SAP 12 SP3, i det här exemplet används SLES4SAP 12 SP3 BYOS-avbildningen SLES For SAP Applications 12 SP3 (BYOS)  
   Välj Tillgänglighetsuppsättning som skapats tidigare  
1. Skapa virtuell dator 2 Använd minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3 BYOS-avbildningen  
   SLES För SAP Applications 12 SP3 (BYOS) används  
   Välj Tillgänglighetsuppsättning som skapats tidigare  
1. Lägg till en datadisk för varje SAP-system till båda virtuella datorer.
1. Skapa en belastningsutjämnare (intern). Vi rekommenderar [standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).  
   1. Följ dessa instruktioner för att skapa standard belastningsutjämnare:
      1. Skapa IP-adresser för klientdel
         1. IP-adress 10.0.0.4 för NW1
            1. Öppna belastningsutjämnaren, välj IP-pool med klientdel och klicka på Lägg till
            1. Ange namnet på den nya ip-poolen för frontend (till exempel **nw1-frontend**)
            1. Ange tilldelningen till Statisk och ange IP-adressen (till exempel **10.0.0.4**)
            1. Klicka på OK
         1. IP-adress 10.0.0.5 för NW2
            * Upprepa stegen ovan för NW2
      1. Skapa backend-poolerna
         1. Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i NFS-klustret
            1. Öppna belastningsutjämnaren, välj backend-pooler och klicka på Lägg till
            1. Ange namnet på den nya backend-poolen (till exempel **nw-backend**)
            1. Välj virtuellt nätverk
            1. Klicka på Lägg till en virtuell dator
            1. Välj de virtuella datorerna i NFS-klustret och deras IP-adresser.
            1. Klicka på Add (Lägg till).
      1. Skapa hälsoavsökningar
         1. Port 61000 för NW1
            1. Öppna belastningsutjämnaren, välj hälsoavsökningar och klicka på Lägg till
            1. Ange namnet på den nya hälsoavsökningen (till exempel **nw1-hp**)
            1. Välj TCP som protokoll, port 610**00**, behåll intervall 5 och felfritt tröskelvärde 2
            1. Klicka på OK
         1. Port 61001 för NW2
            * Upprepa stegen ovan för att skapa en hälsoavsökning för NW2
      1. Regler för belastningsutjämning
         1. Öppna belastningsutjämnaren, välj belastningsutjämningsregler och klicka på Lägg till
         1. Ange namnet på den nya belastningsutjämnarens regel (till exempel **nw1-lb**)
         1. Välj den klient-IP-adress, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **nw1-frontend**. **nw-backend** och **nw1-hp**)
         1. Välj **HA-portar**.
         1. Öka tidsgränsen för inaktiv tid till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
         * Upprepa stegen ovan för att skapa belastningsutjämningsregel för NW2
   1. Om ditt scenario kräver grundläggande belastningsutjämnare följer du följande anvisningar:
      1. Skapa IP-adresser för klientdel
         1. IP-adress 10.0.0.4 för NW1
            1. Öppna belastningsutjämnaren, välj IP-pool med klientdel och klicka på Lägg till
            1. Ange namnet på den nya ip-poolen för frontend (till exempel **nw1-frontend**)
            1. Ange tilldelningen till Statisk och ange IP-adressen (till exempel **10.0.0.4**)
            1. Klicka på OK
         1. IP-adress 10.0.0.5 för NW2
            * Upprepa stegen ovan för NW2
      1. Skapa backend-poolerna
         1. Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i NFS-klustret
            1. Öppna belastningsutjämnaren, välj backend-pooler och klicka på Lägg till
            1. Ange namnet på den nya backend-poolen (till exempel **nw-backend**)
            1. Klicka på Lägg till en virtuell dator
            1. Välj den tillgänglighetsuppsättning som du skapade tidigare
            1. Välj virtuella datorer i NFS-klustret
            1. Klicka på OK
      1. Skapa hälsoavsökningar
         1. Port 61000 för NW1
            1. Öppna belastningsutjämnaren, välj hälsoavsökningar och klicka på Lägg till
            1. Ange namnet på den nya hälsoavsökningen (till exempel **nw1-hp**)
            1. Välj TCP som protokoll, port 610**00**, behåll intervall 5 och felfritt tröskelvärde 2
            1. Klicka på OK
         1. Port 61001 för NW2
            * Upprepa stegen ovan för att skapa en hälsoavsökning för NW2
      1. Regler för belastningsutjämning
         1. 2049 TCP för NW1
            1. Öppna belastningsutjämnaren, välj belastningsutjämningsregler och klicka på Lägg till
            1. Ange namnet på den nya belastningsutjämnarens regel (till exempel **nw1-lb-2049**)
            1. Välj den klient-IP-adress, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **nw1-frontend**)
            1. Behåll protokoll **TCP**, ange port **2049**
            1. Öka tidsgränsen för inaktiv tid till 30 minuter
            1. **Se till att aktivera flytande IP**
            1. Klicka på OK
         1. 2049 UDP för NW1
            * Upprepa stegen ovan för port 2049 och UDP för NW1
         1. 2049 TCP för NW2
            * Upprepa stegen ovan för port 2049 och TCP för NW2
         1. 2049 UDP för NW2
            * Upprepa stegen ovan för port 2049 och UDP för NW2

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i serverdelspoolen för intern (ingen offentlig IP-adress) Standard Azure load balancer, kommer det inte att finnas någon utgående internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutning finns [i Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placeras bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange parameter **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [belastningsutjämnares hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Skapa pacemakerkluster

Följ stegen i [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande Pacemaker-kluster för den här NFS-servern.

### <a name="configure-nfs-server"></a>Konfigurera NFS-server

Följande objekt föregås av antingen **[A]** - som gäller för alla noder, **[1]** - endast gäller för nod 1 eller **[2]** - endast gäller för nod 2.

1. **[A]** Namnmatchning för installationsprogrammet

   Du kan antingen använda en DNS-server eller ändra /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Infoga följande rader till /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Aktivera NFS-server

   Skapa rot NFS-exportposten

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Installera drbd-komponenter

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Skapa en partition för drbd-enheterna

   Lista alla tillgängliga datadiskar

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

1. **[A]** Skapa LVM-konfigurationer

   Lista alla tillgängliga partitioner

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Exempel på utdata
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Skapa LVM-volymer för varje partition

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Konfigurera drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Kontrollera att filen drbd.conf innehåller följande två rader

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Ändra den globala drbd-konfigurationen

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Lägg till följande poster i hanteraren och nettoavsnittet.

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

1. **[A]** Skapa NFS-drbd-enheter

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Sätt i konfigurationen för den nya drbd-enheten och avsluta

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

   Sätt i konfigurationen för den nya drbd-enheten och avsluta

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

   Skapa drbd-enheten och starta den

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Hoppa över den första synkroniseringen

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Ange den primära noden

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Vänta tills de nya drbd-enheterna synkroniseras

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Skapa filsystem på drbd-enheterna

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

1. **[A]** Installation drbd split-brain detection

   När du använder drbd för att synkronisera data från en värd till en annan, kan en så kallad delad hjärna uppstå. En delad hjärna är ett scenario där båda klusternoderna befordrade drbd-enheten till den primära och gick ur synk. Det kan vara en sällsynt situation men du vill fortfarande hantera och lösa en delad hjärna så fort som möjligt. Det är därför viktigt att bli meddelad när en delad hjärna hände.

   Läs [den officiella drbd dokumentation](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) om hur man ställer in en delad hjärna anmälan.

   Det är också möjligt att automatiskt återhämta sig från en delad hjärna scenario. Mer information finns i [Automatisk policy för återställning av hjärnan](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurera klusterramverk

1. **[1]** Lägg till NFS-drbd-enheterna för SAP-system NW1 i klusterkonfigurationen

   > [!IMPORTANT]
   > Nyligen genomförda tester visade situationer, där netcat slutar svara på förfrågningar på grund av eftersläpning och dess begränsning av hantering endast en anslutning. Netcat-resursen slutar lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
   > För befintliga Pacemaker-kluster rekommenderade vi tidigare att ersätta netcat med socat. För närvarande rekommenderar vi att du använder azure-lb-resursagent, som är en del av paketets resursagenter, med följande paketversionskrav:
   > - För SLES 12 SP4/SP5 måste versionen vara minst resursagenter-4.3.018.a7fb5035-3.30.1.  
   > - För SLES 15/15 SP1 måste versionen vara minst resursagenter-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Observera att ändringen kräver korta driftstopp.  
   > För befintliga Pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att växla omedelbart till azure-lb-resursagenten.

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
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Lägg till NFS-drbd-enheterna för SAP-systemet NW2 i klusterkonfigurationen

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
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   Alternativet `crossmnt` i `exportfs` klusterresurserna finns i vår dokumentation för bakåtkompatibilitet med äldre SLES-versioner.  

1. **[1]** Inaktivera underhållsläge
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Installera SAP ASCS och databasen](high-availability-guide-suse.md)
* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (virtuella datorer)][sap-hana-ha]
