---
title: Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för SAP-program | Microsoft Docs
description: Hög tillgänglighet guide för SAP NetWeaver på SUSE Linux Enterprise Server för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 12efeba68f30aa8723acc32449ae05ffac4c1ac4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658765"
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
[nfs-ha]:high-availability-guide-suse-nfs.md

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurera virtuella datorer, installera kluster framework och installera en högtillgänglig SAP NetWeaver 7,50.
I exempelkonfigurationer installationskommandon osv. ASCS instansnummer 00 ÄNDARE instansen nummer 02 och SAP System-ID NW1 används. Namnen på de resurser (till exempel virtuella datorer, virtuella nätverk) i exemplet förutsätter att du har använt den [konvergerat mallen] [ template-converged] med SAP system-ID NW1 att skapa resurser.

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

NFS-servern, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ÄNDARE och SAP HANA-databas ska du använda virtuella värdnamn och virtuella IP-adresser. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visar konfigurationen av (A) SCS och ÄNDARE belastningsutjämnare.

### <a name="ascs"></a>(A) SCS

* Konfiguration för klientdel
  * IP-adress 10.0.0.7
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i (A) SCS/ÄNDARE kluster
* Avsökningsport
  * Port 620**&lt;nr&gt;**
* Regler för belastningsutjämning
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;**  TCP
  * 81**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ÄNDARE

* Konfiguration för klientdel
  * IP-adress 10.0.0.8
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i (A) SCS/ÄNDARE kluster
* Avsökningsport
  * Port 621**&lt;nr&gt;**
* Regler för belastningsutjämning
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Hur du konfigurerar en NFS-server med hög tillgänglighet

SAP NetWeaver kräver en delad lagring för katalogen transport och profil. Läs [hög tillgänglighet för NFS på Azure Virtual Machines på SUSE Linux Enterprise Server] [ nfs-ha] på hur du konfigurerar en NFS-server för SAP NetWeaver.

## <a name="setting-up-ascs"></a>Skapa (A) SCS

Du kan antingen använda en Azure-mall från github för att distribuera alla nödvändiga Azure-resurser, inklusive de virtuella datorerna, tillgänglighet ange och belastningsutjämnare eller du kan distribuera resurserna manuellt.

### <a name="deploy-linux-via-azure-template"></a>Distribuera Linux via Azure-mall

Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP program 12 som du kan använda för att distribuera nya virtuella datorer. Marketplace-avbildning innehåller resurs-agent för SAP NetWeaver.

Du kan använda en av quickstart mallar på github för att distribuera alla nödvändiga resurser. Mallen distribuerar virtuella datorer, belastningsutjämnare, tillgänglighetsuppsättning osv. Följ dessa steg om du vill distribuera mallen:

1. Öppna den [ASCS/SCS Multi SID-mall] [ template-multisid-xscs] eller [konvergerat mallen] [ template-converged] på Azure-portalen i ASCS/SCS endast skapas på belastningsutjämning regler för SAP NetWeaver ASCS/SCS och ÄNDARE (endast Linux) instanser medan mallen konvergerade skapar även reglerna för belastningsutjämning för en databas (till exempel Microsoft SQL Server eller SAP HANA). Om du planerar att installera ett SAP NetWeaver baserat system och du även vill installera databasen på samma datorer använder den [konvergerat mallen][template-converged].
1. Ange följande parametrar
   1. Resursen Prefix (endast mall ASCS/SCS Multi SID)  
      Ange det prefix som du vill använda. Värdet används som ett prefix för de resurser som har distribuerats.
   3. SAP System-ID (endast konvergerade mall)  
      Ange ID för SAP-system för SAP-system som du vill installera. ID som används som ett prefix för de resurser som har distribuerats.
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
   10. Undernät-ID  
   ID för det undernät som de virtuella datorerna ska anslutas till.  Lämna tomt om du vill skapa ett nytt virtuellt nätverk eller Välj samma undernät som du användas eller skapas som en del av distributionen av NFS-servern. ID som ser oftast ut så /subscriptions/**&lt;prenumerations-ID&gt;**/resourceGroups/**&lt;resursgruppnamn&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;virtuella nätverksnamnet&gt;**/subnets/**&lt;undernätsnamn&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure-portalen

Du måste först skapa virtuella datorer för det här NFS-klustret. Därefter kan du skapa en belastningsutjämnare och använder de virtuella datorerna i backend-pooler.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en Tillgänglighetsuppsättning  
   Ange högsta uppdateringsdomän
1. Skapa virtuell dator 1   
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1-bild https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES för SAP program 12 SP1 används  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Skapa virtuell dator 2   
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1-bild https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES för SAP program 12 SP1 används  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Lägg till minst en datadisk till både virtuella datorer  
   Datadiskar som användsför/usr/sap/`<SAPSID`> directory
1. Skapa en belastningsutjämnare (internt)  
   1. Skapa IP-adresser för klientdel
      1. IP-adress 10.0.0.7 för ASCS
         1. Öppna belastningsutjämnaren, Välj klientdelens IP-pool och klicka på Lägg till
         1. Ange namnet på den nya klientdelens IP-poolen (till exempel **nw1-ascs-klientdel**)
         1. Ange tilldelningen som statisk och ange IP-adress (till exempel **10.0.0.7**)
         1. Klicka på OK
      1. IP-adress 10.0.0.8 för ASCS ERS
         * Upprepa stegen ovan för att skapa en IP-adress för ÄNDARE (till exempel **10.0.0.8** och **nw1-aers-backend**)
   1. Skapa backend-pooler
      1. Skapa en serverdelspool för ASCS
         1. Öppna belastningsutjämnaren, Välj serverdelspooler och klicka på Lägg till
         1. Ange namnet på den nya serverdelspoolen (till exempel **nw1-ascs-backend**)
         1. Klicka på Lägg till en virtuell dator.
         1. Välj Tillgänglighetsuppsättningen som du skapade tidigare
         1. Välj de virtuella datorerna i (A) SCS kluster
         1. Klicka på OK
      1. Skapa en serverdelspool för ASCS ERS
         * Upprepa stegen ovan för att skapa en serverdelspool för ÄNDARE (till exempel **nw1-aers-backend**)
   1. Skapa hälsoavsökningar
      1. Port 620**00** för ASCS
         1. Öppna belastningsutjämnaren, Välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **nw1-ascs-hp**)
         1. Välj TCP som protokoll, port 620**00**, hålla intervallet 5 och tröskelvärde för ohälsosamt värde 2
         1. Klicka på OK
      1. Port 621**02** för ASCS ÄNDARE
         * Upprepa stegen ovan för att skapa en hälsoavsökningen för ÄNDARE (till exempel 621**02** och **nw1-aers-hp**)
   1. Regler för belastningsutjämning
      1. 32**00** TCP för ASCS
         1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
         1. Ange namnet på den nya regeln för belastningsutjämnare (till exempel **nw1-lb-3200**)
         1. Välj klientdelens IP-adress, serverdelspool och hälsoavsökningen som du skapade tidigare (till exempel **nw1-ascs-klientdel**)
         1. Behåll protokollet **TCP**, ange port **3200**
         1. Öka tidsgränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK    
      1. Ytterligare portar för ASCS
         * Upprepa stegen ovan för portar 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16- och TCP för ASCS
      1. Ytterligare portar för ASCS ERS
         * Upprepa stegen ovan för portar 33**02**, 5**02**13, 5**02**14, 5**02**16- och TCP för ASCS ERS

### <a name="create-pacemaker-cluster"></a>Skapa Pacemaker kluster

Följ stegen i [konfigurerar Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande kluster Pacemaker för detta (A) SCS-server.

### <a name="installation"></a>Installation

Följande objekt är prefixet antingen **[A]** - gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Installera SUSE Connector
   
   <pre><code>
   sudo zypper install sap_suse_cluster_connector
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
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Förbereda för SAP NetWeaver installation

1. **[A]**  Skapa delade kataloger

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
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
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   /usr/sap/<b>NW1</b>/ASCS<b>00</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCS
   /usr/sap/<b>NW1</b>/ERS<b>02</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS
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

1. **[1]**  Skapa en virtuell IP-resurs och hälsoavsökningen för ASCS-instans

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]**  Installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som rot på den första noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för ASCS, till exempel <b>nw1 ascs</b>, <b>10.0.0.7</b> och instansen tal som du använde för avsökning av belastningsutjämnare, till exempel <b>00</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Om installationen misslyckas med att skapa en undermapp i/usr/sap/**NW1**/ASCS**00**, försök med ägaren och gruppen av ASCS**00** mappen och försök igen.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Skapa en virtuell IP-resurs och hälsoavsökningen för ÄNDARE-instans

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]**  Installera SAP NetWeaver ÄNDARE  

   Installera SAP NetWeaver ÄNDARE som rot på den andra noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för ÄNDARE, till exempel <b>nw1 aers</b>, <b>10.0.0.8</b> och instansen tal som du använde för avsökning av belastningsutjämnare, till exempel <b>02</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller högre. Lägre versioner anger inte behörigheter på rätt sätt och installationen misslyckas.

   Om installationen misslyckas med att skapa en undermapp i/usr/sap/**NW1**/ERS**02**, försök med ägaren och gruppen av ÄNDARE**02** mappen och försök igen.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]**  Adapt ASCS/SCS och ÄNDARE instansen profiler
 
   * ASCS/SCS-profil

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
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
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
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
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]**  Lägg till ASCS och ÄNDARE SAP-tjänster i filen sapservice

   Lägg till ASCS tjänsten post till den andra noden och kopiera posten ÄNDARE tjänsten till den första noden.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Skapa klusterresurser SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"   
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Förberedelse av SAP NetWeaver programmet server

Vissa databaser kräver att instansen databasinstallation körs på en programserver. Förbered application server-datorer för att kunna använda dem i dessa fall.

Stegen nedan förutsätter att du installerar application server på en annan server än ASCS/SCS och HANA-servrar. Annars behövs vissa av stegen nedan (t.ex. Konfigurera värdnamnsmatchning) inte.

1. Konfigurera värdnamnsmatchning

   Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon
   ```bash
   sudo vi /etc/hosts
   ```
   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Skapa katalogen sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
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
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
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

## <a name="install-database"></a>Installera databasen

I det här exemplet är SAP NetWeaver installerad på SAP HANA. Du kan använda varje databas som stöds för den här installationen. Mer information om hur du installerar SAP HANA i Azure finns [hög tillgänglighet för SAP HANA på Azure Virtual Machines (virtuella datorer)][sap-hana-ha]. En lista över databaser som stöds, se [SAP Obs 1928533][1928533].

1. Kör installationen instans SAP-databas

   Installera SAP NetWeaver-databasinstans som rot med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för databasen, till exempel <b>nw1 db</b> och <b>10.0.0.13</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver application server-installation

Följ dessa steg om du vill installera en SAP-programserver. 

1. Förbereda programserver

Följ stegen i kapitlet [SAP NetWeaver programmet server förberedelse](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ovan för att förbereda servern för programmet.

1. Installera SAP NetWeaver programserver

   Installera en primär eller ytterligare SAP NetWeaver programserver.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER för att tillåta en rot-användare att ansluta till sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Uppdatera SAP HANA säker lagringstjänst

   Uppdatera SAP HANA säker lagringstjänst att peka till det virtuella namnet på installationsprogrammet för SAP HANA System replikering.

   Kör följande kommando för att visa en lista med poster
   <pre><code>
   hdbuserstore List
   </code></pre>

   Detta bör innehålla alla poster och bör likna
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   Utdata visar att IP-adressen för standard-posten pekar till den virtuella datorn och inte till belastningsutjämnarens IP-adress. Den här posten måste ändras för att peka till virtuella värdnamn för belastningsutjämnaren. Se till att använda samma port (**30313** i resultatet ovan)!

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Nästa steg
* [Azure virtuella datorer planering och implementering för SAP][planning-guide]
* [Distribution av Azure virtuella datorer för SAP][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA på virtuella Azure-datorer med hög tillgänglighet finns [hög tillgänglighet för SAP HANA på Azure Virtual Machines (virtuella datorer)][sap-hana-ha]
