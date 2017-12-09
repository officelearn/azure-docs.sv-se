---
title: "Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för SAP-program | Microsoft Docs"
description: "Hög tillgänglighet guide för SAP NetWeaver på SUSE Linux Enterprise Server för SAP-program"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: ed728011f2cb7b6108e19a916010fd5447c07093
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines på SUSE Linux Enterprise Server för SAP-program

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

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurera virtuella datorer, installera kluster framework och installera en högtillgänglig SAP NetWeaver 7,50.
I exempelkonfigurationer installationskommandon osv. ASCS-instansnummer 00, ÄNDARE instansnummer 02 och SAP System-ID NWS används. Namnen på de resurser (till exempel virtuella datorer, virtuella nätverk) i exemplet förutsätter att du har använt den [konvergerat mallen] [ template-converged] med SAP system-ID NWS att skapa resurser.

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

![Översikt över SAP NetWeaver hög tillgänglighet](./media/high-availability-guide-suse/img_001.png)

NFS-servern, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ÄNDARE och SAP HANA-databas ska du använda virtuella värdnamn och virtuella IP-adresser. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visar konfigurationen av belastningsutjämnaren.

### <a name="nfs-server"></a>NFS-Server
* Konfiguration för klientdel
  * IP-adressen 10.0.0.4
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i klustret för NFS
* Avsökningsport
  * Port 61000
* Regler för belastningsutjämning
  * 2049 TCP 
  * 2049 UDP

### <a name="ascs"></a>(A) SCS
* Konfiguration för klientdel
  * IP-adress 10.0.0.10
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i (A) SCS/ÄNDARE kluster
* Avsökningsport
  * Port 620**&lt;nr&gt;**
* Regler för belastningsutjämning
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;**  TCP
  * 81**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;**13 TCP
  * 5**&lt;nr&gt;**14 TCP
  * 5**&lt;nr&gt;**16 TCP

### <a name="ers"></a>ÄNDARE
* Konfiguration för klientdel
  * IP-adress 10.0.0.11
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i (A) SCS/ÄNDARE kluster
* Avsökningsport
  * Port 621**&lt;nr&gt;**
* Regler för belastningsutjämning
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;**13 TCP
  * 5**&lt;nr&gt;**14 TCP
  * 5**&lt;nr&gt;**16 TCP

### <a name="sap-hana"></a>SAP HANA
* Konfiguration för klientdel
  * IP-adressen 10.0.0.12
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i klustret HANA
* Avsökningsport
  * Port 625**&lt;nr&gt;**
* Regler för belastningsutjämning
  * 3**&lt;nr&gt;**15 TCP
  * 3**&lt;nr&gt;**17 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Hur du konfigurerar en NFS-server med hög tillgänglighet

### <a name="deploying-linux"></a>Distribuera Linux

Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP program 12 som du kan använda för att distribuera nya virtuella datorer.
Du kan använda en av Snabbstart mallar på github för att distribuera alla nödvändiga resurser. Mallen distribuerar virtuella datorer, belastningsutjämnare, tillgänglighetsuppsättning osv. Följ dessa steg om du vill distribuera mallen:

1. Öppna den [mall för SAP server] [ template-file-server] i Azure-portalen   
1. Ange följande parametrar
   1. Resurs-Prefix  
      Ange det prefix som du vill använda. Värdet används som ett prefix för de resurser som har distribuerats.
   2. OS-typen  
      Välj en av Linux-distributioner. Det här exemplet väljer du SLES 12
   3. Användarnamn och lösenord för Admin administratör  
      En ny användare skapas som kan användas för att logga in på datorn.
   4. Undernät-Id  
      ID för det undernät som de virtuella datorerna ska anslutas till. Lämna tomt om du vill skapa ett nytt virtuellt nätverk eller välj undernätet i ditt VPN eller Expressroute virtuella nätverk att ansluta den virtuella datorn till ditt lokala nätverk. ID som ser oftast ut så /subscriptions/**&lt;prenumerations-id&gt;**/resourceGroups/**&lt;resursgruppnamn&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;virtuella nätverksnamnet&gt;**/subnets/**&lt;undernätsnamn&gt;**

### <a name="installation"></a>Installation

Följande objekt är prefixet antingen **[A]** - gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Uppdatera SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code>
   sudo ssh-keygen -tdsa
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

2. **[2]**  Aktivera ssh-åtkomst

   <pre><code>
   sudo ssh-keygen -tdsa

   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Installera HA filnamnstillägget
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Installationsprogrammet värdnamnsmatchning   

   Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   </code></pre>

1. **[1]**  Installera kluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> ENTER
   # Multicast port [5405] -> ENTER
   # Do you wish to use SBD? [y/N] -> N
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]**  Lägg till nod i klustret
   
   <pre><code> 
   sudo ha-cluster-join

   # WARNING: NTP is not configured to start at system boot.
   # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.10
   # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Ändra hacluster lösenord till samma lösenord

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Konfigurera corosync om du vill använda andra transport och lägga till nodelist. Klustret fungerar inte på annat sätt.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Lägg till följande fetstil innehåll i filen.
   
   <pre><code> 
   [...]
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-nfs-0</b>
      ring0_addr:10.0.0.5
     }
     node {
      # IP address of <b>prod-nfs-1</b>
      ring0_addr:10.0.0.6
     } 
   }</b>
   logging {
     [...]
   </code></pre>

   Starta om tjänsten corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[A]**  Installera drbd komponenter

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Skapa en partition för drbd-enhet

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdc'
   </code></pre>

1. **[A]**  Skapa LVM konfigurationer

   <pre><code>
   sudo pvcreate /dev/sdc1   
   sudo vgcreate vg_NFS /dev/sdc1
   sudo lvcreate -l 100%FREE -n <b>NWS</b> vg_NFS
   </code></pre>

1. **[A]**  Skapa NFS drbd enhet

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_nfs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd enheten och avsluta

   <pre><code>
   resource <b>NWS</b>_nfs {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>prod-nfs-0</b> {
         address   <b>10.0.0.5</b>:7790;
         device    /dev/drbd0;
         disk      /dev/vg_NFS/NWS;
         meta-disk internal;
      }
      on <b>prod-nfs-1</b> {
         address   <b>10.0.0.6</b>:7790;
         device    /dev/drbd0;
         disk      /dev/vg_NFS/NWS;
         meta-disk internal;
      }
   }
   </code></pre>

   Skapa drbd enheten och starta den

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_nfs
   sudo drbdadm up <b>NWS</b>_nfs
   </code></pre>

1. **[1]**  Hoppa över inledande synkronisering

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_nfs
   </code></pre>

1. **[1]**  Anger den primära noden

   <pre><code>
   sudo drbdadm primary --force <b>NWS</b>_nfs
   </code></pre>

1. **[1]**  Vänta tills nya drbd enheter som ska synkroniseras

   <pre><code>
   sudo cat /proc/drbd

   # version: 8.4.6 (api:1/proto:86-101)
   # GIT-hash: 833d830e0152d1e457fa7856e71e11248ccf3f70 build by abuild@sheep14, 2016-05-09 23:14:56
   # 0: cs:Connected ro:Primary/Secondary ds:UpToDate/UpToDate C r-----
   #    ns:0 nr:0 dw:0 dr:912 al:8 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   </code></pre>

1. **[1]**  Skapa filsystem på drbd-enheter

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   </code></pre>


### <a name="configure-cluster-framework"></a>Konfigurera klustret Framework

1. **[1]**  Ändra standardinställningarna

   <pre><code>
   sudo crm configure

   crm(live)configure# rsc_defaults resource-stickiness="1"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Lägger till NFS drbd enhet klusterkonfigurationen

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_nfs drbd_<b>NWS</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Skapa NFS-servern

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive nfsserver \
     systemd:nfs-server \
     op monitor interval="30s"

   crm(live)configure# clone cl-nfsserver nfsserver interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Skapa filsystem för NFS-resurser

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive fs_<b>NWS</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NWS</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# group g-<b>NWS</b>_nfs fs_<b>NWS</b>_sapmnt

   crm(live)configure# order o-<b>NWS</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NWS</b>_nfs:promote g-<b>NWS</b>_nfs:start
   
   crm(live)configure# colocation col-<b>NWS</b>_nfs_on_drbd inf: \
     g-<b>NWS</b>_nfs ms-drbd_<b>NWS</b>_nfs:Master

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Skapa NFS exporterar

   <pre><code>
   sudo mkdir /srv/nfs/<b>NWS</b>/sidsys
   sudo mkdir /srv/nfs/<b>NWS</b>/sapmntsid
   sudo mkdir /srv/nfs/<b>NWS</b>/trans

   sudo crm configure

   crm(live)configure# primitive exportfs_<b>NWS</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NWS</b>" \
     options="rw,no_root_squash" \
     clientspec="*" fsid=0 \
     wait_for_leasetime_on_stop=true \
     op monitor interval="30s"

   crm(live)configure# modgroup g-<b>NWS</b>_nfs add exportfs_<b>NWS</b>

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Skapa en virtuell IP-resurs och hälsoavsökningen för den interna belastningsutjämnaren

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive vip_<b>NWS</b>_nfs IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=24 \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_nfs anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 610<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0

   crm(live)configure# modgroup g-<b>NWS</b>_nfs add nc_<b>NWS</b>_nfs
   crm(live)configure# modgroup g-<b>NWS</b>_nfs add vip_<b>NWS</b>_nfs

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

### <a name="create-stonith-device"></a>Skapa STONITH enhet

STONITH enheten använder ett huvudnamn för tjänsten för att godkänna mot Microsoft Azure. Följ dessa steg om du vill skapa ett huvudnamn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaperna och Skriv ned Directory-Id. Det här är den **klient-id**.
1. Klicka på appen registreringar
1. Klicka på Lägg till
1. Ange ett namn, Välj typ av program ”Web app/API”, ange en inloggnings-URL (till exempel http://localhost) och klicka på Skapa
1. URL för inloggning används inte och kan vara en giltig URL
1. Välj den nya appen och klicka på nycklar på fliken Inställningar
1. Ange en beskrivning för en ny nyckel, Välj ”upphör aldrig att gälla” och klicka på Spara
1. Anteckna värdet. Den används som den **lösenord** för tjänstens huvudnamn
1. Skriv ned det program-Id. Den används som användarnamnet (**inloggnings-id** i stegen nedan) för tjänstens huvudnamn

Tjänstens huvudnamn har inte behörighet att komma åt Azure-resurser som standard. Du behöver ge behörighet att starta och stoppa tjänstens huvudnamn (frigöra) alla virtuella datorer i klustret.

1. Gå till https://portal.azure.com
1. Öppna bladet alla resurser
1. Välj den virtuella datorn
1. Klicka på åtkomstkontroll (IAM)
1. Klicka på Lägg till
1. Välj rollen ägare
1. Ange namnet på programmet som du skapade ovan
1. Klicka på OK

#### <a name="1-create-the-stonith-devices"></a>**[1]**  Skapa STONITH-enheter

När du har redigerat behörigheter för de virtuella datorerna kan du konfigurera STONITH-enheter i klustret.

<pre><code>
sudo crm configure

# replace the bold string with your subscription id, resource group, tenant id, service principal id and password

crm(live)configure# primitive rsc_st_azure_1 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# primitive rsc_st_azure_2 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started

crm(live)configure# commit
crm(live)configure# exit
</code></pre>

#### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Aktivera användning av en STONITH-enhet

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>

## <a name="setting-up-ascs"></a>Skapa (A) SCS

### <a name="deploying-linux"></a>Distribuera Linux

Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP program 12 som du kan använda för att distribuera nya virtuella datorer. Marketplace-avbildning innehåller resurs-agent för SAP NetWeaver.

Du kan använda en av Snabbstart mallar på github för att distribuera alla nödvändiga resurser. Mallen distribuerar virtuella datorer, belastningsutjämnare, tillgänglighetsuppsättning osv. Följ dessa steg om du vill distribuera mallen:

1. Öppna den [ASCS/SCS Multi SID-mall] [ template-multisid-xscs] eller [konvergerat mallen] [ template-converged] på Azure-portalen i ASCS/SCS endast skapas på belastningsutjämning regler för SAP NetWeaver ASCS/SCS och ÄNDARE (endast Linux) instanser medan mallen konvergerade skapar även reglerna för belastningsutjämning för en databas (till exempel Microsoft SQL Server eller SAP HANA). Om du planerar att installera ett SAP NetWeaver baserat system och du även vill installera databasen på samma datorer använder den [konvergerat mallen][template-converged].
1. Ange följande parametrar
   1. Resursen Prefix (endast mall ASCS/SCS Multi SID)  
      Ange det prefix som du vill använda. Värdet används som ett prefix för de resurser som har distribuerats.
   3. SAP System-Id (endast konvergerade mall)  
      Ange SAP-systemet Id för SAP-system som du vill installera. Id som används som ett prefix för de resurser som har distribuerats.
   4. Stack-typ  
      Välj typ för SAP NetWeaver stack
   5. OS-typen  
      Välj en av Linux-distributioner. Det här exemplet väljer du SLES 12 BYOS
   6. DB-typ  
      Välj HANA
   7. Storlek för SAP-System  
      Mängden SAP som innehåller det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegreraren
   8. Systemets tillgänglighet  
      Välj hög tillgänglighet
   9. Användarnamn och lösenord för Admin administratör  
      En ny användare skapas som kan användas för att logga in på datorn.
   10. Undernät-Id  
   ID för det undernät som de virtuella datorerna ska anslutas till.  Lämna tomt om du vill skapa ett nytt virtuellt nätverk eller Välj samma undernät som du användas eller skapas som en del av distributionen av NFS-servern. ID som ser oftast ut så /subscriptions/**&lt;prenumerations-id&gt;**/resourceGroups/**&lt;resursgruppnamn&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;virtuella nätverksnamnet&gt;**/subnets/**&lt;undernätsnamn&gt;**

### <a name="installation"></a>Installation

Följande objekt är prefixet antingen **[A]** - gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Uppdatera SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code>
   sudo ssh-keygen -tdsa
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

2. **[2]**  Aktivera ssh-åtkomst

   <pre><code>
   sudo ssh-keygen -tdsa

   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Installera HA filnamnstillägget
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Uppdatering SAP resurs agenter  
   
   En korrigering för resurs-agenter paket krävs för att använda den nya konfigurationen som beskrivs i den här artikeln. Du kan kontrollera, om uppdateringen redan är installerad med följande kommando

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Resultatet bör likna

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Om kommandot grep inte hittar parametern IS_ERS, måste du installera uppdateringen på [SUSE hämtningssidan](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]**  Installationsprogrammet värdnamnsmatchning   

   Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.10 nws-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.11 nws-ers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.12 nws-db</b>
   </code></pre>

1. **[1]**  Installera kluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> ENTER
   # Multicast port [5405] -> ENTER
   # Do you wish to use SBD? [y/N] -> N
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]**  Lägg till nod i klustret
   
   <pre><code> 
   sudo ha-cluster-join

   # WARNING: NTP is not configured to start at system boot.
   # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.10
   # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Ändra hacluster lösenord till samma lösenord

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Konfigurera corosync om du vill använda andra transport och lägga till nodelist. Klustret fungerar inte på annat sätt.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Lägg till följande fetstil innehåll i filen.
   
   <pre><code> 
   [...]
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>nws-cl-0</b>
      ring0_addr:     10.0.0.14
     }
     node {
      # IP address of <b>nws-cl-1</b>
      ring0_addr:     10.0.0.13
     } 
   }</b>
   logging {
     [...]
   </code></pre>

   Starta om tjänsten corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[A]**  Installera drbd komponenter

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Skapa en partition för drbd-enhet

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdc'
   </code></pre>

1. **[A]**  Skapa LVM konfigurationer

   <pre><code>
   sudo pvcreate /dev/sdc1   
   sudo vgcreate vg_<b>NWS</b> /dev/sdc1
   sudo lvcreate -l 50%FREE -n <b>NWS</b>_ASCS vg_<b>NWS</b>
   sudo lvcreate -l 50%FREE -n <b>NWS</b>_ERS vg_<b>NWS</b>
   </code></pre>

1. **[A]**  Skapa SCS drbd enhet

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_ascs.res
   </code></pre>

   Infoga konfigurationen för den nya drbd enheten och avsluta

   <pre><code>
   resource <b>NWS</b>_ascs {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>nws-cl-0</b> {
         address   <b>10.0.0.14</b>:7791;
         device    /dev/drbd0;
         disk      /dev/vg_NWS/NWS_ASCS;
         meta-disk internal;
      }
      on <b>nws-cl-1</b> {
         address   <b>10.0.0.13</b>:7791;
         device    /dev/drbd0;
         disk      /dev/vg_NWS/NWS_ASCS;
         meta-disk internal;
      }
   }
   </code></pre>

   Skapa drbd enheten och starta den

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_ascs
   sudo drbdadm up <b>NWS</b>_ascs
   </code></pre>

1. **[A]**  Skapa ÄNDARE drbd enhet

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_ers.res
   </code></pre>

   Infoga konfigurationen för den nya drbd enheten och avsluta

   <pre><code>
   resource <b>NWS</b>_ers {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>nws-cl-0</b> {
         address   <b>10.0.0.14</b>:7792;
         device    /dev/drbd1;
         disk      /dev/vg_NWS/NWS_ERS;
         meta-disk internal;
      }
      on <b>nws-cl-1</b> {
         address   <b>10.0.0.13</b>:7792;
         device    /dev/drbd1;
         disk      /dev/vg_NWS/NWS_ERS;
         meta-disk internal;
      }
   }
   </code></pre>

   Skapa drbd enheten och starta den

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_ers
   sudo drbdadm up <b>NWS</b>_ers
   </code></pre>

1. **[1]**  Hoppa över inledande synkronisering

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_ascs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_ers
   </code></pre>

1. **[1]**  Anger den primära noden

   <pre><code>
   sudo drbdadm primary --force <b>NWS</b>_ascs
   sudo drbdadm primary --force <b>NWS</b>_ers
   </code></pre>

1. **[1]**  Vänta tills nya drbd enheter som ska synkroniseras

   <pre><code>
   sudo cat /proc/drbd

   # version: 8.4.6 (api:1/proto:86-101)
   # GIT-hash: 833d830e0152d1e457fa7856e71e11248ccf3f70 build by abuild@sheep14, 2016-05-09 23:14:56
   # 0: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:93991268 nr:0 dw:93991268 dr:93944920 al:383 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   # 1: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:6047920 nr:0 dw:6047920 dr:6039112 al:34 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   # 2: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:5142732 nr:0 dw:5142732 dr:5133924 al:30 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   </code></pre>

1. **[1]**  Skapa filsystem på drbd-enheter

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkfs.xfs /dev/drbd1
   </code></pre>


### <a name="configure-cluster-framework"></a>Konfigurera klustret Framework

**[1]**  Ändra standardinställningarna

   <pre><code>
   sudo crm configure

   crm(live)configure# rsc_defaults resource-stickiness="1"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Förbereda för SAP NetWeaver installation

1. **[A]**  Skapa delade kataloger

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NWS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NWS</b>/SYS

   sudo chattr +i /sapmnt/<b>NWS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NWS</b>/SYS
   </code></pre>

1. **[A]**  Konfigurera autofs
 
   <pre><code>
   sudo vi /etc/auto.master

   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Skapa en fil med

   <pre><code>
   sudo vi /etc/auto.direct

   # Add the following lines to the file, save and exit
   /sapmnt/<b>NWS</b> -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/trans
   /usr/sap/<b>NWS</b>/SYS -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sidsys
   </code></pre>

   Starta om autofs montera nya resurser

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Konfigurera växla fil
 
   <pre><code>
   sudo vi /etc/waagent.conf

   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>

   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starta om agenten för att aktivera ändringen

   <pre><code>
   sudo service waagent restart
   </code></pre>

### <a name="installing-sap-netweaver-ascsers"></a>Installera SAP NetWeaver ASCS/ÄNDARE

1. **[1]**  Skapa en virtuell IP-resurs och hälsoavsökningen för den interna belastningsutjämnaren

   <pre><code>
   sudo crm node standby <b>nws-cl-1</b>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_ASCS \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_ascs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_ASCS drbd_<b>NWS</b>_ASCS \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true"

   crm(live)configure# primitive fs_<b>NWS</b>_ASCS \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/usr/sap/<b>NWS</b>/ASCS<b>00</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# primitive vip_<b>NWS</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.10</b> cidr_netmask=24 \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   crm(live)configure# group g-<b>NWS</b>_ASCS nc_<b>NWS</b>_ASCS vip_<b>NWS</b>_ASCS fs_<b>NWS</b>_ASCS \
      meta resource-stickiness=3000

   crm(live)configure# order o-<b>NWS</b>_drbd_before_ASCS inf: \
     ms-drbd_<b>NWS</b>_ASCS:promote g-<b>NWS</b>_ASCS:start
   
   crm(live)configure# colocation col-<b>NWS</b>_ASCS_on_drbd inf: \
     ms-drbd_<b>NWS</b>_ASCS:Master g-<b>NWS</b>_ASCS
   
   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r

   # Node nws-cl-1: standby
   # <b>Online: [ nws-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      Stopped: [ nws-cl-1 ]
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   </code></pre>

1. **[1]**  Installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som rot på den första noden med hjälp av ett virtuellt värdnamn som till exempel mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för ASCS <b>nws ascs</b>, <b>10.0.0.10</b> och instansen tal som du använde för avsökning av belastningsutjämnaren till exempel <b>00</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. **[1]**  Skapa en virtuell IP-resurs och hälsoavsökningen för den interna belastningsutjämnaren

   <pre><code>
   sudo crm node standby <b>nws-cl-0</b>
   sudo crm node online <b>nws-cl-1</b>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_ERS \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_ers" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_ERS drbd_<b>NWS</b>_ERS \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true"

   crm(live)configure# primitive fs_<b>NWS</b>_ERS \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/usr/sap/<b>NWS</b>/ERS<b>02</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# primitive vip_<b>NWS</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.11</b> cidr_netmask=24 \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0

   crm(live)configure# group g-<b>NWS</b>_ERS nc_<b>NWS</b>_ERS vip_<b>NWS</b>_ERS fs_<b>NWS</b>_ERS

   crm(live)configure# order o-<b>NWS</b>_drbd_before_ERS inf: \
     ms-drbd_<b>NWS</b>_ERS:promote g-<b>NWS</b>_ERS:start
   
   crm(live)configure# colocation col-<b>NWS</b>_ERS_on_drbd inf: \
     ms-drbd_<b>NWS</b>_ERS:Master g-<b>NWS</b>_ERS
   
   crm(live)configure# commit
   # WARNING: Resources nc_NWS_ASCS,nc_NWS_ERS,nc_NWS_nfs violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y

   crm(live)configure# exit
   
   </code></pre>
 
   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r

   # Node <b>nws-cl-0: standby</b>
   # <b>Online: [ nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      Stopped: [ nws-cl-0 ]
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      Stopped: [ nws-cl-0 ]
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   </code></pre>

1. **[2]**  Installera SAP NetWeaver ÄNDARE  

   Installera SAP NetWeaver ÄNDARE som rot på den andra noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för ÄNDARE exempelvis <b>nws ändare</b>, <b>10.0.0.11</b> och instansen tal som du använde för avsökning av belastningsutjämnaren till exempel <b>02</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller högre. Lägre versioner anger inte behörigheter på rätt sätt och installationen misslyckas.
   > 

1. **[1]**  Adapt ASCS/SCS och ÄNDARE instansen profiler
 
   * ASCS/SCS-profil

   <pre><code> 
   sudo vi /sapmnt/<b>NWS</b>/profile/<b>NWS</b>_<b>ASCS00</b>_<b>nws-ascs</b>

   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)

   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector

   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ÄNDARE profil

   <pre><code> 
   sudo vi /sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b>

   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]**  Konfigurera Keep-Alive

   Kommunikationen mellan SAP NetWeaver programservern och ASCS/SCS dirigeras via en belastningsutjämnare för programvara. Belastningsutjämnaren kopplar från inaktiva anslutningar efter en konfigurerbar tidsgräns. För att förhindra detta måste du ange en parameter i profilen för SAP NetWeaver ASCS/SCS och ändra inställningar för Linux-system. Läs [SAP Obs 1410736] [ 1410736] för mer information.
   
   Den ASCS/SCS profil parametern placera/encni/set_so_keepalive har redan lagts till i det sista steget.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Konfigurera SAP-användare efter installationen
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nws</b>adm   
   </code></pre>

1. **[1]**  Lägg till ASCS och ÄNDARE SAP-tjänster i filen sapservice

   Lägg till ASCS tjänsten post till den andra noden och kopiera posten ÄNDARE tjänsten till den första noden.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nws-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nws-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Skapa klusterresurser SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"

   sudo crm configure

   crm(live)configure# primitive rsc_sap_<b>NWS</b>_ASCS<b>00</b> SAPInstance \
    operations $id=rsc_sap_<b>NWS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NWS</b>_ASCS<b>00</b>_<b>nws-ascs</b> START_PROFILE="/sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ASCS<b>00</b>_<b>nws-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10

   crm(live)configure# primitive rsc_sap_<b>NWS</b>_ERS<b>02</b> SAPInstance \
    operations $id=rsc_sap_<b>NWS</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b> START_PROFILE="/sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000

   crm(live)configure# modgroup g-<b>NWS</b>_ASCS add rsc_sap_<b>NWS</b>_ASCS<b>00</b>
   crm(live)configure# modgroup g-<b>NWS</b>_ERS add rsc_sap_<b>NWS</b>_ERS<b>02</b>

   crm(live)configure# colocation col_sap_<b>NWS</b>_no_both -5000: g-<b>NWS</b>_ERS g-<b>NWS</b>_ASCS
   crm(live)configure# location loc_sap_<b>NWS</b>_failover_to_ers rsc_sap_<b>NWS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NWS</b> eq 1
   crm(live)configure# order ord_sap_<b>NWS</b>_first_start_ascs Optional: rsc_sap_<b>NWS</b>_ASCS<b>00</b>:start rsc_sap_<b>NWS</b>_ERS<b>02</b>:stop symmetrical=false

   crm(live)configure# commit
   crm(live)configure# exit

   sudo crm configure property maintenance-mode="false"
   sudo crm node online <b>nws-cl-0</b>
   </code></pre>

   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r

   # Online: <b>[ nws-cl-0 nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   #      rsc_sap_NWS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-0</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      <b>Slaves: [ nws-cl-0 ]</b>
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #      rsc_sap_NWS_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-1</b>
   </code></pre>

### <a name="create-stonith-device"></a>Skapa STONITH enhet

STONITH enheten använder ett huvudnamn för tjänsten för att godkänna mot Microsoft Azure. Följ dessa steg om du vill skapa ett huvudnamn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaperna och Skriv ned Directory-Id. Det här är den **klient-id**.
1. Klicka på appen registreringar
1. Klicka på Lägg till
1. Ange ett namn, Välj typ av program ”Web app/API”, ange en inloggnings-URL (till exempel http://localhost) och klicka på Skapa
1. URL för inloggning används inte och kan vara en giltig URL
1. Välj den nya appen och klicka på nycklar på fliken Inställningar
1. Ange en beskrivning för en ny nyckel, Välj ”upphör aldrig att gälla” och klicka på Spara
1. Anteckna värdet. Den används som den **lösenord** för tjänstens huvudnamn
1. Skriv ned det program-Id. Den används som användarnamnet (**inloggnings-id** i stegen nedan) för tjänstens huvudnamn

Tjänstens huvudnamn har inte behörighet att komma åt Azure-resurser som standard. Du behöver ge behörighet att starta och stoppa tjänstens huvudnamn (frigöra) alla virtuella datorer i klustret.

1. Gå till https://portal.azure.com
1. Öppna bladet alla resurser
1. Välj den virtuella datorn
1. Klicka på åtkomstkontroll (IAM)
1. Klicka på Lägg till
1. Välj rollen ägare
1. Ange namnet på programmet som du skapade ovan
1. Klicka på OK

#### <a name="1-create-the-stonith-devices"></a>**[1]**  Skapa STONITH-enheter

När du har redigerat behörigheter för de virtuella datorerna kan du konfigurera STONITH-enheter i klustret.

<pre><code>
sudo crm configure

# replace the bold string with your subscription id, resource group, tenant id, service principal id and password

crm(live)configure# primitive rsc_st_azure_1 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# primitive rsc_st_azure_2 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started

crm(live)configure# commit
crm(live)configure# exit
</code></pre>

#### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Aktivera användning av en STONITH-enhet

Aktivera användning av en STONITH-enhet

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>

## <a name="install-database"></a>Installera databasen

I det här exemplet är en SAP HANA System replikering installeras och konfigureras. SAP HANA körs i samma kluster som SAP NetWeaver ASCS/SCS och ÄNDARE. Du kan också installera SAP HANA på ett kluster. Se [hög tillgänglighet för SAP HANA på Azure Virtual Machines (virtuella datorer)] [ sap-hana-ha] för mer information.

### <a name="prepare-for-sap-hana-installation"></a>Förbereda för SAP HANA-installation

Normalt bör du använda LVM för volymer som lagrar data och loggfiler. I testsyfte kan välja du också att lagra data och loggfilen direkt på en vanlig disk.

1. **[A]**  LVM  
   Exemplet nedan förutsätter att de virtuella datorerna har fyra datadiskar som är anslutna som ska användas för att skapa två volymer.
   
   Skapa fysiska volymer för alla diskar som du vill använda.
   
   <pre><code>
   sudo pvcreate /dev/sdd
   sudo pvcreate /dev/sde
   sudo pvcreate /dev/sdf
   sudo pvcreate /dev/sdg
   </code></pre>
   
   Skapa en volym grupp för datafiler, en volym grupp för loggfilerna och en för den delade katalogen för SAP HANA
   
   <pre><code>
   sudo vgcreate vg_hana_data /dev/sdd /dev/sde
   sudo vgcreate vg_hana_log /dev/sdf
   sudo vgcreate vg_hana_shared /dev/sdg
   </code></pre>
   
   Skapa logiska volymer
   
   <pre><code>
   sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
   sudo mkfs.xfs /dev/vg_hana_data/hana_data
   sudo mkfs.xfs /dev/vg_hana_log/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
   </code></pre>
   
   Skapa mount-kataloger och kopiera alla logiska volymer UUID
   
   <pre><code>
   sudo mkdir -p /hana/data
   sudo mkdir -p /hana/log
   sudo mkdir -p /hana/shared
   sudo chattr +i /hana/data
   sudo chattr +i /hana/log
   sudo chattr +i /hana/shared
   # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
   sudo blkid
   </code></pre>
   
   Skapa autofs poster för de tre logiska volymerna
   
   <pre><code>
   sudo vi /etc/auto.direct
   </code></pre>
   
   Infoga raden till sudo vi /etc/auto.direct
   
   <pre><code>
   /hana/data -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b>
   /hana/log -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b>
   /hana/shared -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b>
   </code></pre>
   
   Montera nya volymer
   
   <pre><code>
   sudo service autofs restart 
   </code></pre>

1. **[A]**  Vanlig diskar  

   För små eller demonstrera system, du kan placera HANA data och loggfilen filer på en disk. Följande kommandon skapar en partition på /dev/sdc och formatera den med xfs.
   ```bash
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdd'
   sudo mkfs.xfs /dev/sdd1
   
   # write down the id of /dev/sdd1
   sudo /sbin/blkid
   sudo vi /etc/auto.direct
   ```
   
   Infoga raden till /etc/auto.direct
   <pre><code>
   /hana -fstype=xfs :UUID=<b>&lt;UUID&gt;</b>
   </code></pre>
   
   Skapa målkatalogen och montera disken.
   
   <pre><code>
   sudo mkdir /hana
   sudo chattr +i /hana
   sudo service autofs restart
   </code></pre>

### <a name="installing-sap-hana"></a>Installera SAP HANA

Följande steg baseras på kapitel 4 i den [SAP HANA SR prestanda optimerade scenariot guiden] [ suse-hana-ha-guide] att installera SAP HANA System replikering. Läs den innan du fortsätter med installationen.

1. **[A]**  Kör hdblcm från DVD: n HANA
   
   <pre><code>
   sudo hdblcm --sid=<b>HDB</b> --number=<b>03</b> --action=install --batch --password=<b>&lt;password&gt;</b> --system_user_password=<b>&lt;password for system user&gt;</b>

   sudo /hana/shared/<b>HDB</b>/hdblcm/hdblcm --action=configure_internal_network --listen_interface=internal --internal_network=<b>10.0.0/24</b> --password=<b>&lt;password for system user&gt;</b> --batch
   </code></pre>

1. **[A]**  Uppgradera SAP Värdagenten

   Hämta senaste SAP Värdagenten arkivet från den [SAP Softwarecenter] [ sap-swcenter] och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar till den fil som du hämtat.
   <pre><code>
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <b>&lt;path to SAP Host Agent SAR&gt;</b> 
   </code></pre>

1. **[1]**  Skapa HANA replikering (som rot)  

   Kör följande kommando. Se till att ersätta fetstil strängar (HANA System-ID HDB och instans antalet 03) med värden för SAP HANA-installationen.
   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
   </code></pre>

1. **[A]**  Skapa keystore posten (rot)

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>&lt;passwd&gt;</b>
   </code></pre>

1. **[1]**  Backup database (som rot)

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
   </code></pre>

1. **[1]**  Växla till HANA sapsid användare och skapa den primära platsen.

   <pre><code>
   su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Växla till HANA sapsid användare och skapa en sekundär plats.

   <pre><code>
   su - <b>hdb</b>adm
   sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>nws-cl-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

1. **[1]**  Skapa SAP HANA klusterresurser

   Först skapa topologin.
   
   <pre><code>
   sudo crm configure

   # replace the bold string with your instance number and HANA system id
   
   crm(live)configure# primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b>   ocf:suse:SAPHanaTopology \
     operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"
    
   crm(live)configure# clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>
   
   Skapa sedan HANA-resurser
   
   <pre><code>
   sudo crm configure

   # replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
    
   crm(live)configure# primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
    
   crm(live)configure# ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
    
   crm(live)configure# primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
     meta target-role="Started" is-managed="true" \ 
     operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
     op monitor interval="10s" timeout="20s" \ 
     params ip="<b>10.0.0.12</b>" 

   crm(live)configure# primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
     op monitor timeout=20s interval=10 depth=0 

   crm(live)configure# group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
    
   crm(live)configure# colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
     msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  

   crm(live)configure# order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
     msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
    
   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r

   # <b>Online: [ nws-cl-0 nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      <b>Slaves: [ nws-cl-0 ]</b>
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #      rsc_sap_NWS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-1</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   #      rsc_sap_NWS_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-0</b>
   #  Clone Set: cln_SAPHanaTopology_HDB_HDB03 [rsc_SAPHanaTopology_HDB_HDB03]
   #      <b>Started: [ nws-cl-0 nws-cl-1 ]</b>
   #  Master/Slave Set: msl_SAPHana_HDB_HDB03 [rsc_SAPHana_HDB_HDB03]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g_ip_HDB_HDB03
   #      rsc_ip_HDB_HDB03   (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      rsc_nc_HDB_HDB03   (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   # rsc_st_azure_1  (stonith:fence_azure_arm):      <b>Started nws-cl-0</b>
   # rsc_st_azure_2  (stonith:fence_azure_arm):      <b>Started nws-cl-1</b>
   </code></pre>

1. **[1]**  Installera databasinstansen SAP NetWeaver

   Installera SAP NetWeaver-databasinstans som rot med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för databasen, till exempel <b>nws-db</b> och <b>10.0.0.12</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver application server-installation

Följ dessa steg om du vill installera en SAP-programserver. Stegen nedan förutsätter att du installerar application server på en annan server än ASCS/SCS och HANA-servrar. Annars behövs vissa av stegen nedan (t.ex. Konfigurera värdnamnsmatchning) inte.

1. Konfigurera värdnamnsmatchning    
   Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon
   ```bash
   sudo vi /etc/hosts
   ```
   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.10 nws-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.11 nws-ers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.12 nws-db</b>
   # IP address of the application server
   <b>10.0.0.8 nws-di-0</b>
   </code></pre>

1. Skapa katalogen sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NWS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NWS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurera autofs
 
   <pre><code>
   sudo vi /etc/auto.master

   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Skapa en ny fil med

   <pre><code>
   sudo vi /etc/auto.direct

   # Add the following lines to the file, save and exit
   /sapmnt/<b>NWS</b> -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/trans
   </code></pre>

   Starta om autofs montera nya resurser

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurera växlingsfilen
 
   <pre><code>
   sudo vi /etc/waagent.conf

   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>

   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starta om agenten för att aktivera ändringen

   <pre><code>
   sudo service waagent restart
   </code></pre>

1. Installera SAP NetWeaver programserver

   Installera en primär eller ytterligare SAP NetWeaver programserver.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Uppdatera SAP HANA säker lagringstjänst

   Uppdatera SAP HANA säker lagringstjänst att peka till det virtuella namnet på installationsprogrammet för SAP HANA System replikering.
   <pre><code>
   su - <b>nws</b>adm
   hdbuserstore SET DEFAULT <b>nws-db</b>:3<b>03</b>15 <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Nästa steg
* [Azure virtuella datorer planering och implementering för SAP][planning-guide]
* [Distribution av Azure virtuella datorer för SAP][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA på virtuella Azure-datorer med hög tillgänglighet finns [hög tillgänglighet för SAP HANA på Azure Virtual Machines (virtuella datorer)][sap-hana-ha]