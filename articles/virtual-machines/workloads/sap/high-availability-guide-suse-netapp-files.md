---
title: Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp Files | Microsoft Docs
description: Guide för hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/015/2019
ms.author: radeltch
ms.openlocfilehash: 18bbeef833e1c82999e87451d279c0d3464af509
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711138"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

Den här artikeln beskriver hur du distribuerar de virtuella datorerna, konfigurera virtuella datorer, installera kluster framework och installera en högtillgänglig SAP NetWeaver 7,50-system med hjälp av [Azure NetApp-filer (i allmänt tillgänglig förhandsversion)](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-introduction/).
I exempelkonfigurationer, installationskommandon etc., ASCS-instans är antalet 00, ÄNDARE instansnummer 01, instansen för primära programmet (PROVIDERADRESSER) är 02 och programinstansen (AAS) är 03. SAP System-ID: T QAS används. 

Den här artikeln förklarar hur att uppnå hög tillgänglighet för SAP NetWeaver-program med Azure NetApp-filer. Databas-lagret beskrivs inte i detalj i den här artikeln.

Läs följande SAP Notes och papers först:

* [Dokumentation om Azure NetApp-filer][anf-azure-doc] 
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
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SUSE SAP HA Regelverksguider] [ suse-ha-guide] guiderna innehåller all nödvändig information för att ställa in Netweaver hög tillgänglighet och SAP HANA-Systemreplikering på plats. Använd dessa guider som utgångspunkt Allmänt. De ger mycket mer detaljerad information.
* [SUSE hög tillgänglighet tillägget 12 SP3 viktig information][suse-ha-12sp3-relnotes]
* [NetApp SAP-program på Microsoft Azure med Azure NetApp filer][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

Hög availability(HA) för SAP Netweaver central services kräver delad lagring.
För att uppnå som på SUSE Linux var hittills det nödvändigt att skapa separat med hög tillgänglighet NFS-kluster. 

Nu är det möjligt att uppnå SAP Netweaver hög tillgänglighet med hjälp av delad lagring som distribuerats på Azure NetApp-filer. Med hjälp av Azure NetApp filer för den delade lagringen eliminerar behovet av ytterligare [NFS kluster](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Pacemaker krävs fortfarande för hög tillgänglighet för SAP Netweaver centrala services(ASCS/SCS).


![Översikt över SAP NetWeaver hög tillgänglighet](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, ÄNDARE för SAP NetWeaver och SAP HANA-databas använda virtuella värdnamn och virtuella IP-adresser. På Azure, en [belastningsutjämnare](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) krävs för att använda en virtuell IP-adress. I följande lista visas konfigurationen av (A) SCS och ÄNDARE belastningsutjämnare.

### <a name="ascs"></a>(A)SCS

* Konfiguration för klientdel
  * IP-adress 10.1.1.20
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska vara en del av (A) SCS/ÄNDARE kluster
* Avsökningsport
  * Port 620<strong>&lt;nr&gt;</strong>
* Belastningsutjämningsregler
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Konfiguration för klientdel
  * IP-adress 10.1.1.21
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska vara en del av (A) SCS/ÄNDARE kluster
* Avsökningsport
  * Port 621<strong>&lt;nr&gt;</strong>
* Belastningsutjämningsregler
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Hur du konfigurerar Azure NetApp Files-infrastruktur 

SAP NetWeaver kräver delad lagring för katalogen transport och profil.  Innan du fortsätter med installationen av Azure NetApp filer infrastruktur, kan du bekanta dig med den [Azure NetApp Files dokumentation][anf-azure-doc]. Kontrollera om den valda Azure-regionen erbjuder Azure NetApp-filer. Följande länk visar tillgängligheten för Azure NetApp filer av Azure-region: [Azure NetApp filer tillgänglighet av Azure-Region][anf-avail-matrix].

Funktionen Azure NetApp-filer är i offentlig förhandsversion i flera Azure-regioner. Innan du distribuerar Azure NetApp Files, registrera dig för förhandsversionen av Azure NetApp filer efter den [registrera Azure NetApp filer anvisningar][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files-resurser  

Anvisningarna förutsätter att du redan har distribuerat [Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview). Tänk på att Azure NetApp Files-resurser och de virtuella datorerna, där Azure NetApp Files-resurser ska monteras måste distribueras i samma Azure-nätverk.  

1. Om du inte har gjort det redan kan begära att [registrera i förhandsversionen av Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register).  

2. Skapa NetApp-kontot i den valda Azure-region, efter den [instruktioner för att skapa NetApp konto](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Konfigurera Azure NetApp Files kapacitet poolen följa den [anvisningar om hur du ställer in Azure NetApp Files kapacitet pool](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
SAP Netweaver-arkitekturen som visas i den här artikeln använder en enda Azure NetApp Files kapacitet pool, Premium-SKU. Vi rekommenderar Azure NetApp filer Premium-SKU för SAP Netweaver arbetsbelastning på Azure.  

4. Delegera ett undernät till Azure NetApp filer enligt beskrivningen i den [instruktioner Delegera ett undernät till Azure NetApp Files](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuera Azure NetApp Files volymer, efter den [instruktioner för att skapa en volym för Azure NetApp Files](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-volumes). Distribuera volymer i den avsedda Azure NetApp Files [undernät](https://docs.microsoft.com/en-us/rest/api/virtualnetwork/subnets). Tänk på att Azure NetApp Files-resurser och virtuella Azure-datorer måste vara i samma Azure-nätverk. Till exempel sapmnt<b>QAS</b>, usrsap<b>QAS</b>och så vidare är volymnamn och sapmnt<b>qas</b>, usrsap<b>qas</b>och så vidare är filepaths för Azure NetApp-filer volymer.  

   1. volymen sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. volymen usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. volymen usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. volymen usrsap<b>QAS</b>ändare (nfs://10.1.0.4/usrsap<b>qas</b>ändare)
   5. volymen trans (nfs://10.1.0.4/trans)
   6. volymen usrsap<b>QAS</b>provideradresser (nfs://10.1.0.5/usrsap<b>qas</b>provideradresser)
   7. volymen usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
I det här exemplet använde vi Azure NetApp-filer för alla SAP Netweaver-filsystem för att demonstrera hur Azure NetApp filer kan användas. Med SAP-system som inte behöver monteras via NFS kan också distribueras som [Azure disk storage](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#premium-ssd) . I det här exemplet <b>a e</b> måste finnas på Azure NetApp filer och <b>f-g</b> (det vill säga usr/sap/<b>QAS</b>/D<b>02</b>,usr/sap/<b>QAS </b>/D<b>03</b>) kan distribueras som Azure disk storage. 

### <a name="important-considerations"></a>Att tänka på

När du överväger Azure NetApp filer för SAP Netweaver på arkitektur med hög tillgänglighet i SUSE, Tänk på följande viktiga överväganden:

- Den minimikapacitet poolen är 4 TiB. Poolstorlek kapacitet måste vara i multiplar av 4 TiB.
- Minsta volymen är 100 GiB
- Azure NetApp-filer och alla virtuella datorer, där Azure NetApp Files volymer ska monteras måste vara i samma Azure-nätverk eller i [peer-kopplade virtuella nätverk](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview) i samma region. Azure NetApp filer åtkomst via VNET-peering i samma region stöds nu. NetApp åtkomst till Azure över global peering stöds inte ännu.
- Det valda virtuella nätverket måste ha ett undernät som delegerats till Azure NetApp-filer.
- Azure NetApp Files stöder för närvarande endast NFSv3 
- NetApp-filer som Azure erbjuder [exportera princip](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): du kan styra de tillåtna klienterna åtkomsttyp (läsa och skriva, skrivskyddad, osv.). 
- Azure NetApp filer-funktionen är inte medvetna zon ännu. NetApp-filer för Azure-funktionen inte är distribuerad i alla tillgänglighetszoner i en Azure-region. Vara medveten om potentiella konsekvenser för svarstid i vissa Azure-regioner. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Distribuera virtuella Linux-datorer manuellt via Azure-portalen

Du måste först skapa Azure NetApp Files-volymer. Distribuera de virtuella datorerna. Därefter kan du skapa en belastningsutjämnare och använder du virtuella datorer i backend-pooler.

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en Tillgänglighetsuppsättning för ASCS  
   Ställ in max uppdateringsdomän
1. Skapa virtuell dator 1  
   Använd minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3-avbildningen  
   Välj Tillgänglighetsuppsättning skapade tidigare för ASCS  
1. Skapa virtuell dator 2  
   Använd minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3-avbildningen  
   Välj Tillgänglighetsuppsättning skapade tidigare för ASCS  
1. Skapa en Tillgänglighetsuppsättning för SAP-programinstanser (PROVIDERADRESSER, AAS)    
   Ställ in max uppdateringsdomän
1. Skapa virtuell dator 3  
   Använd minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3-avbildningen  
   Välj Tillgänglighetsuppsättning skapade tidigare för PROVIDERADRESSER/AAS   
1. Skapa virtuell dator 4  
   Använd minst SLES4SAP 12 SP3, i det här exemplet SLES4SAP 12 SP3-avbildningen  
   Välj Tillgänglighetsuppsättning skapade tidigare för PROVIDERADRESSER/AAS  

## <a name="setting-up-ascs"></a>Konfigurationen av (A) SCS

I det här exemplet är resurserna som har distribuerats manuellt den [Azure-portalen](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Distribuera Azure Load Balancer manuellt via Azure-portalen

Du måste först skapa Azure NetApp Files-volymer. Distribuera de virtuella datorerna. Därefter kan du skapa en belastningsutjämnare och använder du virtuella datorer i backend-pooler.

1. Skapa en belastningsutjämnare (internt)  
   1. Skapa frontend-IP-adresser
      1. IP-adress 10.1.1.20 för ASCS
         1. Öppna belastningsutjämnaren, Välj IP-adresspool på klientdelen och klicka på Lägg till
         1. Ange namnet på den nya frontend IP-poolen (till exempel **klientdel. QAS. ASCS**)
         1. Ange tilldelningen till statiskt och ange IP-adressen (till exempel **10.1.1.20**)
         1. Klicka på OK
      1. IP-adress 10.1.1.21 för ASCS ERS
         * Upprepa stegen under ”a” så här skapar du en IP-adress för ÄNDARE (till exempel **10.1.1.21** och **klientdel. QAS. ÄNDARE**)
   1. Skapa backend-adresspooler
      1. Skapa en serverdelspool för ASCS
         1. Öppna belastningsutjämnaren, Välj serverdelspooler och klicka på Lägg till
         1. Ange namnet på den nya backend-poolen (till exempel **serverdel. QAS**)
         1. Klicka på Lägg till en virtuell dator.
         1. Välj Tillgänglighetsuppsättning som du skapade tidigare för ASCS 
         1. Välj de virtuella datorerna av (A) SCS-kluster
         1. Klicka på OK
   1. Skapa hälsoavsökningarna
      1. Port 620**00** för ASCS
         1. Öppna belastningsutjämnaren, Välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **hälsotillstånd. QAS. ASCS**)
         1. Välj TCP som protokoll, port 620**00**, se till att intervallet 5 och tröskelvärde för ej felfri 2
         1. Klicka på OK
      1. Port 621**01** för ASCS ÄNDARE
            * Upprepa stegen ovan i ”c” för att skapa en hälsoavsökning för ÄNDARE (till exempel 621**01** och **hälsotillstånd. QAS. ÄNDARE**)
   1. Belastningsutjämningsregler
      1. 32**00** TCP för ASCS
         1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
         1. Ange namnet på den nya belastningsutjämningsregeln (till exempel **lb. QAS. ASCS.3200**)
         1. Välj klientdelens IP-adress för ASCS, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **klientdel. QAS. ASCS**)
         1. Behåll protokollet **TCP**, ange port **3200**
         1. Öka tidsgränsen för inaktivitet till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
      1. Ytterligare portar för ASCS
         * Upprepa stegen ovan under ”d” för portar 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16- och TCP för ASCS
      1. Ytterligare portar för ASCS ERS
         * Upprepa stegen ovan under ”d” för portar 33**01**, 5**01**13, 5**01**14, 5**01**16- och TCP för ASCS ERS

> [!IMPORTANT]
> Aktivera inte TCP tidsstämplarna för virtuella Azure-datorer är placerade bakom Azure Load Balancer. Aktivera TCP tidsstämplar genereras hälsoavsökningar misslyckas. Ange parametern **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [hälsoavsökningar för belastningsutjämnaren](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Skapa Pacemaker kluster

Följ stegen i [konfigurerar Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande kluster Pacemaker för detta (A) SCS-server.

### <a name="installation"></a>Installation

Följande objekt har prefixet antingen **[A]** – gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Installera SUSE Connector

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Använd inte bindestreck i värdnamnen för klusternoderna. Annars fungerar inte ditt kluster. Detta är en känd begränsning och SUSE arbetar på att lösa. Korrigeringen släpps som en uppdatering av sap-suse-cloud-connector-paketet.

   Se till att du installerat den nya versionen av SAP SUSE-klusterkoppling. Gamla anropades sap_suse_cluster_connector och den nya servern kallas **sap-suse-kluster-connector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]**  Update SAP resource agenter  
   
   En korrigering för resurs-agenter paket krävs för att använda den nya konfigurationen som beskrivs i den här artikeln. Du kan kontrollera, om uppdateringen redan är installerad med följande kommando

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Utdata bör likna

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Om kommandot grep inte hittar parametern IS_ERS, måste du installera uppdateringen på [hämtningssidan för SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]**  Konfigurera matcha värdnamn

   Du kan använda en DNS-server, eller så kan du ändra i/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet till matchar din miljö   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Förbereda för installation av SAP NetWeaver

1. **[A]**  Skapa delade kataloger

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]**  Konfigurera autofs

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Skapa en fil med

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Azure NetApp Files stöder för närvarande endast NFSv3. Inte utelämna nfsvers = 3 switch.
   
   Starta om autofs för att montera de nya resurserna
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]**  Konfigurera växla fil

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


### <a name="installing-sap-netweaver-ascsers"></a>Installera SAP NetWeaver ASCS/ÄNDARE

1. **[1]**  Skapa en virtuell IP-resurs och en hälsoavsökning för ASCS-instans

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som resurserna som körs.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]**  Installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som rot på den första noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för ASCS, till exempel <b>anftstsapvh</b>, <b>10.1.1.20</b> och numret på instans som du använde för avsökning av belastningsutjämnare, till exempel <b>00</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER så att en icke-begränsande användare att ansluta till sapinst. Du kan använda parametern SAPINST_USE_HOSTNAME för att installera SAP, med hjälp av virtuella värdnamn.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Om installationen inte kan skapa en undermapp i/usr/sap/**QAS**/ASCS**00**, försök att ange ägare och grupp med ASCS**00** mappen och försök igen. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]**  Skapa en virtuell IP-resurs och en hälsoavsökning för ÄNDARE-instans

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som resurserna som körs.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]**  Installera SAP NetWeaver ÄNDARE

   Installera SAP NetWeaver ÄNDARE som rot på den andra noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för ÄNDARE, till exempel <b>anftstsapers</b>, <b>10.1.1.21</b> och numret på instans som du använde för avsökning av belastningsutjämnare, till exempel <b>01</b>.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER så att en icke-begränsande användare att ansluta till sapinst. Du kan använda parametern SAPINST_USE_HOSTNAME för att installera SAP, med hjälp av virtuella värdnamn.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller högre. Lägre versioner anger inte behörigheterna som korrekt och installationen misslyckas.

   Om installationen inte kan skapa en undermapp i/usr/sap/**QAS**/ERS**01**, försök att ange ägare och grupp med ÄNDARE**01** mappen och försök igen.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]**  Adapt ASCS/SCS och ÄNDARE instans profiler
 
   * ASCS/SCS-profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
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
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]**  Konfigurera Keep Alive

   Kommunikationen mellan SAP NetWeaver-programserver och ASCS/SCS dirigeras via en belastningsutjämnare för programvara. Belastningsutjämnaren kopplar från inaktiva anslutningar när du har en tidsgräns som kan konfigureras. Om du vill förhindra detta måste du anger en parameter i SAP NetWeaver ASCS/SCS-profil och ändra inställningarna för Linux-system. Läs [SAP anteckning 1410736] [ 1410736] för mer information.

   Den ASCS/SCS profil parametern placera/encni/set_so_keepalive har redan lagts till i det sista steget.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]**  Konfigurera SAP-användare efter installationen

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]**  Till ASCS och ÄNDARE SAP-tjänster i filen sapservice

   Lägg till ASCS tjänsten post till den andra noden och kopiera posten ÄNDARE tjänsten till den första noden.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]**  Skapa SAP-klusterresurser

Om du använder sätta 1 serverarkitektur (ENSA1) definiera resurserna på följande sätt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP-stöd för sätta servern 2, inklusive replikering, från och med SAP NV 7.52. Från och med ABAP plattform 1809, installeras placera servern 2 som standard. Se SAP anteckning [2630416](https://launchpad.support.sap.com/#/notes/2630416) placera servern 2 support.
Om du använder sätta serverarkitektur 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definierar resurserna på följande sätt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Om du uppgraderar från en äldre version och växla till sätta server 2, se sap-kommentar [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som resurserna som körs.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server förberedelse 

Vissa databaser kräver att instansen databasinstallation körs på en programserver. Förbereda programmet server-datorer för att kunna använda dem i dessa fall.

Stegen nedan förutsätter att du installerar application server på en annan server än ASCS/SCS- och HANA-servrar. Annars behövs vissa av stegen nedan (t.ex. Konfigurera värdnamnsmatchning) inte.

Följande objekt har prefixet antingen **[A]** – gäller för både PROVIDERADRESSER och AAS, **[P]** – gäller endast för PROVIDERADRESSER eller **[S]** – gäller endast för AAS.


1. **[A]**  Konfigurera operativsystem

   Minska storleken på den informationen i cachen. Mer information finns i [låg skrivprestanda på SLES 11/12 servrar med stora RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Konfigurera matcha värdnamn

   Du kan använda en DNS-server, eller så kan du ändra i/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   ```bash
   sudo vi /etc/hosts
   ```

   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet till matchar din miljö

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]**  Skapa sapmnt katalogen

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  Skapa PROVIDERADRESSER katalogen

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  Skapa AAS katalogen

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  Konfigurera autofs på PROVIDERADRESSER

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Skapa en ny fil med

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Starta om autofs för att montera de nya resurserna

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  Konfigurera autofs på AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Skapa en ny fil med

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Starta om autofs för att montera de nya resurserna

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

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Installera databas

I det här exemplet installeras SAP NetWeaver på SAP HANA. Du kan använda varje databas som stöds för den här installationen. Mer information om hur du installerar SAP HANA i Azure finns i [hög tillgänglighet för SAP HANA på Azure Virtual Machines (VM)][sap-hana-ha]. En lista över databaser som stöds finns i [SAP anteckning 1928533][1928533].

* Kör SAP-databasinstallation för instans

   Installera SAP NetWeaver-databasinstans som rot med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientdel belastningsutjämningskonfigurationen för databasen.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER så att en icke-begränsande användare att ansluta till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver application server-installation

Följ dessa steg om du vill installera en programserver för SAP.

1. **[A]**  Förbered programserver följer du stegen i kapitlet [SAP NetWeaver application server förberedelse](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ovan för att förbereda servern för programmet.

2. **[A]**  Installera SAP NetWeaver-programserver installera en primär eller ytterligare SAP NetWeaver-programserver.

   Du kan använda parametern sapinst SAPINST_REMOTE_ACCESS_USER så att en icke-begränsande användare att ansluta till sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  Update SAP HANA säker lagring

   Uppdatera arkivet SAP HANA säkert att den pekar på det virtuella namnet på SAP HANA System Replication-installationen.

   Kör följande kommando för att lista poster
   <pre><code>
   hdbuserstore List
   </code></pre>

   Detta bör lista alla poster och bör se ut ungefär som
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Utdata visar att IP-adressen för standardalternativet pekar till den virtuella datorn och inte till belastningsutjämnarens IP-adress. Den här posten måste ändras för att peka till det virtuella värdnamnet för belastningsutjämnaren. Se till att använda samma port (**30313** i utdata ovan) och databasnamnet (**QAS** i utdata ovan)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testa konfiguration

Följande tester är en kopia av testfall i den [bästa praxis riktlinjer för SUSE][suse-ha-guide]. Kopieras de för din bekvämlighet. Alltid också Läs riktlinjerna för bästa praxis och utföra alla ytterligare tester som kan ha lagts.

1. Testa HAGetFailoverConfig och HACheckConfig HACheckFailoverConfig

   Kör följande kommandon som \<sapsid > adm på noden där den ASCS-instansen körs för närvarande. Om kommandona misslyckas med fel: Otillräckligt med minne kan det orsakas av bindestreck i värdnamn. Detta är ett känt problem och korrigeras genom SUSE i sap-suse-kluster-connector-paketet.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Migrera manuellt ASCS-instans

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Kör följande kommandon som rot du migrerar ASCS-instans.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Testa HAFailoverToNode

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Kör följande kommandon som \<sapsid > adm att migrera ASCS-instans.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simulera nodkrasch 

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommando som rot på noden där den ASCS-instansen körs

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Om du använder uppstår får Pacemaker inte automatiskt börja på noden avslutats. Status när noden startas igen bör se ut så här.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Använd följande kommandon för att starta Pacemaker på noden avslutats, rensa uppstår meddelanden och rensa de misslyckade resurserna.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testa manuella omstart av ASCS-instans

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Skapa ett sätta Lås av, för exempel-redigera en användare i transaktionen su01. Kör följande kommandon som < sapsid\>adm på noden där den ASCS-instansen körs. Kommandona ska stoppa ASCS-instans och starta den igen. Om du använder sätta serverarkitektur 1 förväntas sätta låset gå förlorad i det här testet. Om du använder sätta serverarkitektur 2, ska sätta behållas. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS-instansen ska nu vara inaktiverade i Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Starta igen ASCS-instans på samma nod.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Placera låset för transaktionen su01 bör gå förlorade, om du använder sätta serverarkitektur replikering 1 och backend-bör har återställts. Resurstillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Avsluta serverprocessen för meddelande

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommandon som rot för att identifiera processen för meddelande-server och avsluta den.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Om du bara avsluta message server en gång, startas av sapstart. Om du avsluta det ofta räcker Pacemaker kommer så småningom flytta ASCS-instans till den andra noden. Kör följande kommandon som rot du rensar resurstillståndet i ASCS och ÄNDARE instans efter testet.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Avsluta sätta serverprocessen

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Kör följande kommandon som rot på noden där den ASCS-instansen körs för att avsluta placera servern.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   ASCS-instansen bör omedelbart växlas över till den andra noden. ÄNDARE instansen bör också redundansväxla efter ASCS-instansen har startats. Kör följande kommandon som rot du rensar resurstillståndet i ASCS och ÄNDARE instans efter testet.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Avsluta sätta replikeringsprocessen för server

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommando som rot på noden där den ÄNDARE-instansen körs för att avsluta serverprocessen sätta replikering.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Om du endast kör kommandot en gång, sapstart startar om processen. Om du kör den tillräckligt sapstart kommer inte starta om ofta processen och resursen är i ett stoppat tillstånd. Kör följande kommandon som rot du rensar resurstillståndet för ÄNDARE instansen efter testet.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Avsluta sätta sapstartsrv processen

   Resurstillstånd innan du startar testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Kör följande kommandon som rot på noden där ASCS körs.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Sapstartsrv processen bör alltid startas av Pacemaker resource agenten. Resurstillstånd efter testet:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Azure virtuella datorer, planering och implementering av SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Lär dig hur du upprättar hög tillgänglighet och planera för haveriberedskap för SAP 
* HANA på Azure (stora instanser), se [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure Virtual Machines i [hög tillgänglighet för SAP HANA på Azure Virtual Machines (VM)][sap-hana-ha]
