---
title: Virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på SLES | Microsoft-dokument
description: Guide för hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 05effb7d2e64c5f27acabad4b086ba27d6849cc8
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348822"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

I den här artikeln beskrivs hur du distribuerar de virtuella datorerna, konfigurerar virtuella datorer, installerar klusterramverket och installerar ett system med högtillgängliga SAP NetWeaver 7.50.
I exempelkonfigurationerna, installationskommandon etc. ASCS-instansnummer 00, ERS-instansnummer 02 och SAP System ID NW1 används. Namnen på resurserna (till exempel virtuella datorer, virtuella nätverk) i exemplet förutsätter att du har använt den [konvergerade mallen][template-converged] med SAP-system-ID NW1 för att skapa resurserna.

Läs följande SAP-anteckningar och -dokument först

* SAP Note [1928533][1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-programvara
  * Viktig kapacitetsinformation för Azure VM-storlekar
  * Sap-programvara och kombinationer av operativsystem (operativsystem) och databas
  * Nödvändig SAP-kärnversion för Windows och Linux på Microsoft Azure

* SAP Note [2015553][2015553] listar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure.
* SAP Note [2205917][2205917] har rekommenderat OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP Note [1944799][1944799] har SAP HANA Riktlinjer för SUSE Linux Enterprise Server för SAP-program
* SAP Note [2178632][2178632] har detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [2191498][2191498] har den nödvändiga SAP Host Agent-versionen för Linux i Azure.
* SAP Note [2243692][2243692] har information om SAP-licensiering på Linux i Azure.
* SAP Note [1984787][1984787] har allmän information om SUSE Linux Enterprise Server 12.
* SAP Note [1999351][1999351] har ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Planering och implementering av Virtuella Azure-datorer för SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SUSE SAP HA Bästa praxis Guider][suse-ha-guide] Guiderna innehåller all nödvändig information för att ställa in Netweaver HA och SAP HANA System Replication lokalt. Använd dessa stödlinjer som en allmän baslinje. De ger mycket mer detaljerad information.
* [SUSE hög tillgänglighet Tillägg 12 SP3 viktig information][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet kräver SAP NetWeaver en NFS-server. NFS-servern är konfigurerad i ett separat kluster och kan användas av flera SAP-system.

![ÖVERSIKT ÖVER HÖG TILLGÄNGLIGHET I SAP NetWeaver](./media/high-availability-guide-suse/ha-suse.png)

NFS-servern, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS och SAP HANA-databasen använder virtuella värdnamn och virtuella IP-adresser. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Vi rekommenderar att du använder [standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). Följande lista visar konfigurationen av (A)SCS och ERS belastningsutjämnare.

### <a name="ascs"></a>A. Scs

* Frontend-konfiguration
  * IP-adress 10.0.0.7
* Avsökningsport
  * Port 620<strong>&lt;nr&gt;</strong>
* Regler för belastningsutjämning
  * Om du använder Standard belastningsutjämnare väljer du **HA-portar**
  * Om du använder Grundläggande belastningsutjämnare skapar du belastningsutjämningsregler för följande portar
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 36<strong>&lt;&gt; nr</strong> TCP
    * 39<strong>&lt;&gt; nr</strong> TCP
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Frontend-konfiguration
  * IP-adress 10.0.0.8
* Avsökningsport
  * Port 621<strong>&lt;nr&gt;</strong>
* Regler för belastningsutjämning
  * Om du använder Standard belastningsutjämnare väljer du **HA-portar**
  * Om du använder Grundläggande belastningsutjämnare skapar du belastningsutjämningsregler för följande portar
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Serverningskonfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i (A)SCS/ERS-klustret


## <a name="setting-up-a-highly-available-nfs-server"></a>Konfigurera en NFS-server med högtillgänge

SAP NetWeaver kräver delad lagring för transport- och profilkatalogen. Läs [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][nfs-ha] om hur du konfigurerar en NFS-server för SAP NetWeaver.

## <a name="setting-up-ascs"></a>Ställa in (A)SCS

Du kan antingen använda en Azure-mall från GitHub för att distribuera alla nödvändiga Azure-resurser, inklusive virtuella datorer, tillgänglighetsuppsättning och belastningsutjämnare eller så kan du distribuera resurserna manuellt.

### <a name="deploy-linux-via-azure-template"></a>Distribuera Linux via Azure-mall

Azure Marketplace innehåller en avbildning för SUSE Linux Enterprise Server för SAP Applications 12 som du kan använda för att distribuera nya virtuella datorer. Marketplace-avbildningen innehåller resursagenten för SAP NetWeaver.

Du kan använda en av snabbstartsmallarna på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighetsuppsättningen etc. Så här distribuerar du mallen:

1. Öppna [ASCS/SCS Multi SID-mallen][template-multisid-xscs] eller den [konvergerade mallen][template-converged] på Azure-portalen. 
   ASCS/SCS-mallen skapar bara belastningsutjämningsreglerna för instanserna SAP NetWeaver ASCS/SCS och ERS (endast Linux), medan den konvergerade mallen också skapar belastningsutjämningsreglerna för en databas (till exempel Microsoft SQL Server eller SAP HANA). Om du planerar att installera ett SAP NetWeaver-baserat system och du också vill installera databasen på samma datorer använder du den [konvergerade mallen][template-converged].
1. Ange följande parametrar
   1. Resursprefix (endast ASCS/SCS Multi SID-mall)  
      Ange det prefix som du vill använda. Värdet används som prefix för de resurser som distribueras.
   3. Sap-system-ID (endast konvergerad mall)  
      Ange SAP-system-ID för det SAP-system som du vill installera. ID används som prefix för de resurser som distribueras.
   4. Stack typ  
      Välj STACKTYPEN SAP NetWeaver
   5. Typ av os  
      Välj en av Linux-distributionerna. I det här exemplet väljer du SLES 12 BYOS
   6. Db-typ  
      Välj HANA
   7. Sap-systemets storlek.  
      Mängden SAPS det nya systemet tillhandahåller. Om du är osäker på hur många SAPS systemet kräver, fråga din SAP Technology Partner eller System Integrator
   8. Systemets tillgänglighet  
      Välj HA
   9. Administratörsanvändarnamn och administratörslösenord  
      En ny användare skapas som kan användas för att logga in på datorn.
   10. Nät-ID  
   Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät som definierats ska den virtuella datorn tilldelas, namnge ID:t för det specifika undernätet. ID:t ser vanligtvis ut som /subscriptions/subscription**&lt;ID&gt;**/resourceGroups/**&lt;resource group name&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;virtual network name&gt;**/subnets/**&lt;undernätsnamn&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure Portal

Du måste först skapa de virtuella datorerna för det här NFS-klustret. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i serverdapoolen.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en tillgänglighetsuppsättning  
   Ange maxuppdateringsdomän
1. Skapa virtuell dator 1  
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1-avbildningenhttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES För SAP Applications 12 SP1 används  
   Välj Tillgänglighetsuppsättning som skapats tidigare  
1. Skapa virtuell dator 2  
   Använd minst SLES4SAP 12 SP1, i det här exemplet SLES4SAP 12 SP1-avbildningenhttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES För SAP Applications 12 SP1 används  
   Välj Tillgänglighetsuppsättning som skapats tidigare  
1. Lägga till minst en datadisk till båda virtuella datorer  
   Datadiskarna används för katalogen /usr/sap/`<SAPSID`>
1. Skapa belastningsutjämnare (intern, standard):  
   1. Skapa IP-adresser för klientdel
      1. IP-adress 10.0.0.7 för ASCS
         1. Öppna belastningsutjämnaren, välj IP-pool med klientdel och klicka på Lägg till
         1. Ange namnet på den nya ip-poolen för frontend (till exempel **nw1-ascs-frontend**)
         1. Ange tilldelningen till Statisk och ange IP-adressen (till exempel **10.0.0.7**)
         1. Klicka på OK
      1. IP-adress 10.0.0.8 för ASCS ERS
         * Upprepa stegen ovan för att skapa en IP-adress för ERS (till exempel **10.0.0.8** och **nw1-aers-backend**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **nw1-backend**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj virtuell dator
      1. Välj de virtuella datorerna i (A)SCS-klustret och deras IP-adresser.
      1. Klicka på Lägg till
   1. Skapa hälsoavsökningar
      1. Port 620**00** för ASCS
         1. Öppna belastningsutjämnaren, välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **nw1-ascs-hp**)
         1. Välj TCP som protokoll, port 620**00**, behåll intervall 5 och felfritt tröskelvärde 2
         1. Klicka på OK
      1. Port 621**02** för ASCS ERS
         * Upprepa stegen ovan för att skapa en hälsosond för ERS (till exempel 621**02** och **nw1-aers-hp**)
   1. Regler för belastningsutjämning
      1. Belastningsutjämningsregler för ASCS
         1. Öppna belastningsutjämnaren, välj belastningsutjämningsregler och klicka på Lägg till
         1. Ange namnet på den nya belastningsutjämnarens regel (till exempel **nw1-lb-ascs)**
         1. Välj den klient-IP-adress, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **nw1-ascs-frontend,** **nw1-backend** och **nw1-ascs-hp**)
         1. Välj **HA-portar**
         1. Öka tidsgränsen för inaktiv tid till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
         * Upprepa stegen ovan för att skapa belastningsutjämningsregler för ERS (till exempel **nw1-lb-ers)**
1. Om ditt scenario kräver grundläggande belastningsutjämnare (intern) gör du så här:  
   1. Skapa IP-adresser för klientdel
      1. IP-adress 10.0.0.7 för ASCS
         1. Öppna belastningsutjämnaren, välj IP-pool med klientdel och klicka på Lägg till
         1. Ange namnet på den nya ip-poolen för frontend (till exempel **nw1-ascs-frontend**)
         1. Ange tilldelningen till Statisk och ange IP-adressen (till exempel **10.0.0.7**)
         1. Klicka på OK
      1. IP-adress 10.0.0.8 för ASCS ERS
         * Upprepa stegen ovan för att skapa en IP-adress för ERS (till exempel **10.0.0.8** och **nw1-aers-frontend**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **nw1-backend**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj den tillgänglighetsuppsättning som du skapade tidigare
      1. Välj virtuella datorer i (A)SCS-klustret
      1. Klicka på OK
   1. Skapa hälsoavsökningar
      1. Port 620**00** för ASCS
         1. Öppna belastningsutjämnaren, välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **nw1-ascs-hp**)
         1. Välj TCP som protokoll, port 620**00**, behåll intervall 5 och felfritt tröskelvärde 2
         1. Klicka på OK
      1. Port 621**02** för ASCS ERS
         * Upprepa stegen ovan för att skapa en hälsosond för ERS (till exempel 621**02** och **nw1-aers-hp**)
   1. Regler för belastningsutjämning
      1. 32**00** TCP för ASCS
         1. Öppna belastningsutjämnaren, välj belastningsutjämningsregler och klicka på Lägg till
         1. Ange namnet på den nya belastningsutjämnarens regel (till exempel **nw1-lb-3200)**
         1. Välj den klient-IP-adress, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **nw1-ascs-frontend**)
         1. Behåll protokoll **TCP**, ange port **3200**
         1. Öka tidsgränsen för inaktiv tid till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
      1. Ytterligare portar för ASCS
         * Upprepa stegen ovan för portarna 36**00,** 39**00,** 81**00,** 5**00**13, 5**00**14, 5**00**16 och TCP för ASCS
      1. Ytterligare portar för ASCS ERS
         * Upprepa stegen ovan för portarna 33**02**, 5**02**13, 5**02**14, 5**02**16 och TCP för ASCS ERS

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i serverdelspoolen för intern (ingen offentlig IP-adress) Standard Azure load balancer, kommer det inte att finnas någon utgående internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutning finns [i Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placeras bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange parameter **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [belastningsutjämnares hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Skapa pacemakerkluster

Följ stegen i [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande Pacemaker-kluster för den här (A)SCS-servern.

### <a name="installation"></a>Installation

Följande objekt föregås av antingen **[A]** - som gäller för alla noder, **[1]** - endast gäller för nod 1 eller **[2]** - endast gäller för nod 2.

1. **[A]** Installera SUSE-anslutning

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Det kända problemet med att använda ett streck i värdnamn är åtgärdat med version **3.1.1** av paketet **sap-suse-cluster-connector**. Kontrollera att du använder minst version 3.1.1 av paketet sap-suse-cluster-connector, om du använder klusternoder med instrumentbräda i värdnamnet. Annars fungerar inte klustret. 

   Kontrollera att du har installerat den nya versionen av SAP SUSE-klusteranslutningen. Den gamla kallades sap_suse_cluster_connector och den nya kallas **sap-suse-cluster-connector**.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** Uppdatera SAP-resursagenter  
   
   En korrigeringsfil för resursagentpaketet krävs för att använda den nya konfigurationen, som beskrivs i den här artikeln. Du kan kontrollera om korrigeringsfilen redan är installerad med följande kommando

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Produktionen bör liknas vid

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Om kommandot grep inte hittar parametern IS_ERS måste du installera korrigeringsfilen som visas på [nedladdningssidan för SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Namnmatchning för installationsprogrammet

   Du kan antingen använda en DNS-server eller ändra /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader till /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Förbered för SAP NetWeaver-installation

1. **[A]** Skapa delade kataloger

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
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

1. **[A]** Konfigurera autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Skapa en fil med

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Starta om autofs för att montera de nya resurserna

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Konfigurera SWAP-fil

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starta om agenten för att aktivera ändringen

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Installera SAP NetWeaver ASCS/ERS

1. **[1]** Skapa en virtuell IP-resurs och hälsoavsökning för ASCS-instansen

   > [!IMPORTANT]
   > Nyligen genomförda tester visade situationer, där netcat slutar svara på förfrågningar på grund av eftersläpning och dess begränsning av hantering endast en anslutning. Netcat-resursen slutar lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
   > För befintliga Pacemaker-kluster rekommenderade vi tidigare att ersätta netcat med socat. För närvarande rekommenderar vi att du använder azure-lb-resursagent, som är en del av paketets resursagenter, med följande paketversionskrav:
   > - För SLES 12 SP4/SP5 måste versionen vara minst resursagenter-4.3.018.a7fb5035-3.30.1.  
   > - För SLES 15/15 SP1 måste versionen vara minst resursagenter-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Observera att ändringen kräver korta driftstopp.  
   > För befintliga Pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att växla omedelbart till azure-lb-resursagenten.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som rot på den första noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för frontadkonfigurationen för belastningsutjämnarhanteraren för ASCS, till exempel <b>nw1-ascs</b>, <b>10.0.0.7</b> och det instansnummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>00</b>.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Om installationen inte kan skapa en undermapp i /usr/sap/**NW1**/ASCS**00**kan du prova att ange ägare och grupp för ASCS**00-mappen** och försöka igen.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Skapa en virtuell IP-resurs och hälsoavsökning för ERS-instansen

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Installera SAP NetWeaver ERS

   Installera SAP NetWeaver ERS som rot på den andra noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnadsfrontendkonfigurationen för ERS, till exempel <b>nw1-aers</b>, <b>10.0.0.8</b> och det instansnummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>02</b>.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller senare. Lägre versioner anger inte behörigheterna korrekt och installationen misslyckas.

   Om installationen inte kan skapa en undermapp i /usr/sap/**NW1**/ERS**02**kan du prova att ange ägare och grupp för mappen**ERS 02** och försök igen.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** Anpassa ASCS/SCS- och ERS-instansprofilerna
 
   * ASCS/SCS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** Konfigurera Keep Alive

   Kommunikationen mellan SAP NetWeaver-programservern och ASCS/SCS dirigeras via en belastningsutjämnare för programvara. Belastningsutjämnaren kopplar från inaktiva anslutningar efter en konfigurerbar timeout. För att förhindra detta måste du ställa in en parameter i SAP NetWeaver ASCS/SCS-profilen och ändra Linux-systeminställningarna. Läs [SAP Note 1410736][1410736] för mer information.

   Parametern ASCS/SCS-profil enque/encni/set_so_keepalive har redan lagts till i det sista steget.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Konfigurera SAP-användare efter installationen

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Lägg till ASCS- och ERS SAP-tjänsterna i sapservice-filen

   Lägg till ASCS-tjänstposten i den andra noden och kopiera ERS-tjänstposten till den första noden.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Skapa SAP-klusterresurserna

Om du använder enqueue server 1-arkitektur (ENSA1) definierar du resurserna på följande sätt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
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

  SAP introducerade stöd för enqueue server 2, inklusive replikering, från och med SAP NW 7.52. Från och med ABAP Platform 1809 installeras enqueue server 2 som standard. Se SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) för stöd för enqueue server 2.
Om du använder enqueue server 2-arkitektur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)definierar du resurserna på följande sätt:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Om du uppgraderar från en äldre version och byter till enqueue server 2 läser du SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver programserver förberedelse

Vissa databaser kräver att installationen av databasinstansen körs på en programserver. Förbered virtuella datorer för programservern för att kunna använda dem i dessa fall.

Stegen nedan förutsätter att du installerar programservern på en annan server än ASCS/SCS- och HANA-servrarna. Annars behövs inte några av stegen nedan (som att konfigurera värdnamnsmatchning).

1. Konfigurera operativsystem

   Minska storleken på den smutsiga cachen. Mer information finns i [Låg skrivprestanda på SLES 11/12-servrar med stort RAM-minne](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Upplösning av värdnamn för installationsprogrammet

   Du kan antingen använda en DNS-server eller ändra /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   ```bash
   sudo vi /etc/hosts
   ```

   Infoga följande rader till /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö

   <pre><code># IP address of the load balancer frontend configuration for NFS
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

1. Skapa sapmntkatalogen

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurera autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Skapa en ny fil med

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Starta om autofs för att montera de nya resurserna

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurera SWAP-fil

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starta om agenten för att aktivera ändringen

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Installera databas

I det här exemplet installeras SAP NetWeaver på SAP HANA. Du kan använda alla databaser som stöds för den här installationen. Mer information om hur du installerar SAP HANA i Azure finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VMs).][sap-hana-ha] En lista över databaser som stöds finns i [SAP Note 1928533][1928533].

1. Köra installationen av SAP-databasinstansen

   Installera SAP NetWeaver-databasinstansen som root med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientkonfigurationen för belastningsutjämnarhanteraren för databasen, till exempel <b>nw1-db</b> och <b>10.0.0.13</b>.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>INSTALLATION av PROGRAMserver för SAP NetWeaver

Så här installerar du en SAP-programserver.

1. Förbereda programserver

   Följ stegen i kapitlet [SAP NetWeaver programserver förberedelse](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ovan för att förbereda programservern.

1. Installera SAP NetWeaver-programserver

   Installera en primär eller ytterligare SAP NetWeaver-programserver.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Uppdatera SECURE STORE FÖR SAP HANA

   Uppdatera det säkra SAP HANA-arkivet så att det pekar på det virtuella namnet på SAP HANA System Replication-konfigurationen.

   Kör följande kommando för att lista posterna
   <pre><code>hdbuserstore List
   </code></pre>

   Detta bör lista alla poster och bör se ut ungefär som
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Utdata visar att IP-adressen för standardposten pekar på den virtuella datorn och inte på belastningsutjämnarens IP-adress. Den här posten måste ändras så att den pekar på lastbalansens virtuella värdnamn. Se till att använda samma port **(30313** i utdata ovan) och databasnamn **(HN1** i utdata ovan)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testa klusterinställningarna

Följande tester är en kopia av testfallen i de bästa metoderna guider av SUSE. De kopieras för din bekvämlighet. Läs alltid de bästa metodtipsguiderna och utför alla ytterligare tester som kan ha lagts till.

1. Testa HAGetFailoverConfig, HACheckConfig och HACheckFailoverConfig

   Kör följande kommandon \<som sapsid>adm på noden där ASCS-instansen körs för närvarande. Om kommandona misslyckas med FAIL: Otillräckligt minne kan det bero på streck i värdnamnet. Detta är ett känt problem och kommer att åtgärdas av SUSE i sap-suse-cluster-connector-paketet.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Migrera ASCS-instansen manuellt

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Kör följande kommandon som rot för att migrera ASCS-instansen.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Testa HAFailoverToNode

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommandon \<som sapsid>adm för att migrera ASCS-instansen.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Simulera nodkrasch

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Kör följande kommando som rot på noden där ASCS-instansen körs

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Om du använder SBD ska Pacemaker inte starta automatiskt på den dödade noden. Statusen när noden har startats igen ska se ut så här.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Använd följande kommandon för att starta Pacemaker på den dödade noden, rensa SBD-meddelandena och rensa de misslyckade resurserna.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Testa manuell omstart av ASCS-instans

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Skapa ett enqueue-lås genom att till exempel redigera en användare i transaktion su01. Kör följande kommandon \<som sapsid>adm på noden där ASCS-instansen körs. Kommandona stoppar ASCS-instansen och startar den igen. Om du använder enqueue server 1-arkitektur förväntas enqueue-låset gå förlorat i det här testet. Om du använder enqueue server 2-arkitekturen behålls köföljet. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS-instansen bör nu inaktiveras i Pacemaker

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Starta ASCS-instansen igen på samma nod.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Enqueue-låset för transaktionen su01 ska gå förlorad och backend-sidan borde ha återställts. Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kill meddelandeserver process

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommandon som root för att identifiera meddelandeserverns process och döda den.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Om du bara dödar meddelandeservern en gång, kommer den att startas om av sapstart. Om du dödar den tillräckligt ofta flyttar Pacemaker så småningom ASCS-instansen till den andra noden. Kör följande kommandon som root för att rensa resurstillståndet för ASCS- och ERS-instansen efter testet.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Döda enqueue server process

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Kör följande kommandon som rot på noden där ASCS-instansen körs för att döda enqueue-servern.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   ASCS-instansen bör omedelbart växla över till den andra noden. ERS-instansen bör också växla över när ASCS-instansen har startats. Kör följande kommandon som root för att rensa resurstillståndet för ASCS- och ERS-instansen efter testet.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kill enqueue replikeringsserver process

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommando som rot på noden där ERS-instansen körs för att döda enqueue-replikeringsserverprocessen.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Om du bara kör kommandot en gång startar sapstart om processen. Om du kör det tillräckligt ofta startar sapstart inte om processen och resursen stoppas. Kör följande kommandon som rot för att rensa resurstillståndet för ERS-instansen efter testet.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Döda enqueue sapstartsrv process

   Resurstillstånd innan testet påbörjas:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Kör följande kommandon som rot på noden där ASCS körs.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Sapstartsrv-processen ska alltid startas om av pacemakerresursagenten. Resurstillstånd efter testet:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [HA för SAP NW på virtuella Azure-datorer på SLES för SAP-program med flera SID-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (virtuella datorer)][sap-hana-ha]
