---
title: Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files | Microsoft Docs
description: Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503583"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Den här artikeln beskriver hur du distribuerar de virtuella datorerna, konfigurera virtuella datorer, installera kluster framework och installera en högtillgänglig SAP NetWeaver 7,50-system med hjälp av [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
I exempelkonfigurationer installationskommandon osv. ASCS-instansen är antalet 00, ÄNDARE instansen är antalet 01, primära programinstans (PROVIDERADRESSER) är 02 och programinstansen (AAS) är 03. SAP System-ID: T QAS används. 

Databas-lagret beskrivs inte i detalj i den här artikeln.  

Läs följande SAP Notes och papers först:

* [Dokumentation om Azure NetApp-filer][anf-azure-doc] 
* SAP-kommentar [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-program
  * Viktiga kapacitetsinformation för Azure VM-storlekar
  * SAP-program som stöds, och operativsystem (OS) och kombinationer av databas
  * Nödvändiga SAP kernel-version för Windows och Linux på Microsoft Azure

* SAP-kommentar [2015553] visar en lista över kraven för distribution av SAP-stöd för SAP-programvara i Azure.
* SAP-kommentar [2002167] rekommenderar OS-inställningar för Red Hat Enterprise Linux
* SAP-kommentar [2009879] har SAP HANA riktlinjer för Red Hat Enterprise Linux
* SAP-kommentar [2178632] mer information om all övervakning mått som rapporterats för SAP i Azure.
* SAP-kommentar [2191498] har Värdagenten för SAP-version som krävs för Linux i Azure.
* SAP-kommentar [2243692] har licensieringsinformation SAP på Linux i Azure.
* SAP-kommentar [1999351] innehåller ytterligare felsökningsinformation för Azure förbättrad övervakning av tillägget för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Azure virtuella datorer, planering och implementering av SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP Netweaver i pacemaker kluster](https://access.redhat.com/articles/3150081)
* Allmänna RHEL-dokumentation
  * [Översikt över tillägg för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Hög tillgänglighet tillägg Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referens för tillägg för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurera ASCS/ÄNDARE för SAP Netweaver med fristående resurser i RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurera SAP S/4HANA ASCS/ÄNDARE med fristående placera servern 2 (ENSA2) i Pacemaker på RHEL ](https://access.redhat.com/articles/3974941)
* Azure specifika RHEL-dokumentationen:
  * [Stöd för principer för RHEL-kluster för hög tillgänglighet – Microsoft Azure-datorer som medlemmar i ett kluster](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera en Red Hat Enterprise Linux 7.4 (och senare) hög tillgänglighet-kluster på Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp filer][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

Hög availability(HA) för SAP Netweaver central services kräver delad lagring.
För att uppnå som på Red Hat Linux var hittills det nödvändigt att skapa separata kluster för med hög tillgänglighet GlusterFS. 

Nu är det möjligt att uppnå SAP Netweaver hög tillgänglighet med hjälp av delad lagring som distribuerats på Azure NetApp-filer. Med hjälp av Azure NetApp filer för den delade lagringen eliminerar behovet av ytterligare [GlusterFS kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Pacemaker krävs fortfarande för hög tillgänglighet för SAP Netweaver centrala services(ASCS/SCS).

![Översikt över SAP NetWeaver hög tillgänglighet](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, ÄNDARE för SAP NetWeaver och SAP HANA-databas använda virtuella värdnamn och virtuella IP-adresser. På Azure måste en belastningsutjämnare använda en virtuell IP-adress. I följande lista visas konfigurationen av belastningsutjämnaren med separata front IP-adresser för (A) SCS och ÄNDARE.

> [!IMPORTANT]
> Multi-SID klustring av SAP ASCS/ÄNDARE med Red Hat Linux som gästoperativsystem i virtuella Azure-datorer är **stöds inte**. Multi-SID klustring beskriver hur du installerar flera SAP ASCS/ÄNDARE instanser med olika SID i ett Pacemaker kluster.

### <a name="ascs"></a>(A)SCS

* Konfiguration för klientdel
  * IP-adress 192.168.14.9
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
  * IP-adress 192.168.14.10
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska vara en del av (A) SCS/ÄNDARE kluster
* Avsökningsport
  * Port 621<strong>&lt;nr&gt;</strong>
* Belastningsutjämningsregler
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Hur du konfigurerar Azure NetApp Files-infrastruktur 

SAP NetWeaver kräver delad lagring för katalogen transport och profil.  Innan du fortsätter med installationen av Azure NetApp filer infrastruktur, kan du bekanta dig med den [Azure NetApp Files dokumentation][anf-azure-doc]. Kontrollera om den valda Azure-regionen erbjuder Azure NetApp-filer. Följande länk visar tillgängligheten för Azure NetApp filer av Azure-region: [Azure NetApp filer tillgänglighet av Azure-Region][anf-avail-matrix].

Azure NetApp-filer finns i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Innan du distribuerar Azure NetApp Files, begär du Kom igång med Azure NetApp filer, efter den [registrera Azure NetApp filer anvisningar][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files-resurser  

Anvisningarna förutsätter att du redan har distribuerat [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). NetApp-filer för Azure-resurser och de virtuella datorerna, där Azure NetApp Files-resurser ska monteras måste distribueras i samma Azure-nätverk eller i peerkopplade virtuella nätverk i Azure.  

1. Om du inte har gjort det redan begära [Kom igång med Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Skapa NetApp-kontot i den valda Azure-region, efter den [instruktioner för att skapa NetApp konto](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Konfigurera Azure NetApp Files kapacitet poolen följa den [anvisningar om hur du ställer in Azure NetApp Files kapacitet pool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
SAP Netweaver-arkitekturen som visas i den här artikeln använder en enda Azure NetApp Files kapacitet pool, Premium-SKU. Vi rekommenderar Azure NetApp filer Premium-SKU för SAP Netweaver arbetsbelastning på Azure.  
4. Delegera ett undernät till Azure NetApp filer enligt beskrivningen i den [instruktioner Delegera ett undernät till Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuera Azure NetApp Files volymer, efter den [instruktioner för att skapa en volym för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Distribuera volymer i den avsedda Azure NetApp Files [undernät](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Tänk på att Azure NetApp Files-resurser och virtuella Azure-datorer måste finnas i samma Azure-nätverk eller i peerkopplade virtuella nätverk i Azure. I det här exemplet använder vi två Azure NetApp Files volymer: sap<b>QAS</b> och transSAP. Sökvägarna som är monterade till motsvarande monteringspunkterna är /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, osv.  

   1. volymen sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volymen sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volymen sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volymen sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ändare)
   5. volymen transSAP (nfs://192.168.24.4/transSAP)
   6. volymen sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>provideradresser)
   7. volymen sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
I det här exemplet använde vi Azure NetApp-filer för alla SAP Netweaver-filsystem för att demonstrera hur Azure NetApp filer kan användas. Med SAP-system som inte behöver monteras via NFS kan också distribueras som [Azure disk storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . I det här exemplet <b>a e</b> måste finnas på Azure NetApp filer och <b>f-g</b> (det vill säga usr/sap/<b>QAS</b>/D<b>02</b>,usr/sap/<b>QAS </b>/D<b>03</b>) kan distribueras som Azure disk storage. 

### <a name="important-considerations"></a>Att tänka på

När du överväger Azure NetApp filer för SAP Netweaver på arkitektur med hög tillgänglighet i SUSE, Tänk på följande viktiga överväganden:

- Den minimikapacitet poolen är 4 TiB. Poolstorlek kapacitet måste vara i multiplar av 4 TiB.
- Minsta volymen är 100 GiB
- Azure NetApp-filer och alla virtuella datorer, där Azure NetApp Files volymer ska monteras måste vara i samma Azure-nätverk eller i [peer-kopplade virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) i samma region. Azure NetApp filer åtkomst via VNET-peering i samma region stöds nu. NetApp åtkomst till Azure över global peering stöds inte ännu.
- Det valda virtuella nätverket måste ha ett undernät som delegerats till Azure NetApp-filer.
- Azure NetApp Files stöder för närvarande endast NFSv3 
- NetApp-filer som Azure erbjuder [exportera princip](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): du kan styra de tillåtna klienterna åtkomsttyp (läsa och skriva, skrivskyddad, osv.). 
- Azure NetApp filer-funktionen är inte medvetna zon ännu. NetApp-filer för Azure-funktionen inte är distribuerad i alla tillgänglighetszoner i en Azure-region. Vara medveten om potentiella konsekvenser för svarstid i vissa Azure-regioner. 

## <a name="setting-up-ascs"></a>Konfigurationen av (A) SCS

I det här exemplet är resurserna som har distribuerats manuellt den [Azure-portalen](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure-portalen

Du måste först skapa Azure NetApp Files-volymer. Distribuera de virtuella datorerna. Därefter kan du skapa en belastningsutjämnare och använder du virtuella datorer i backend-pooler.

1. Skapa en belastningsutjämnare (internt)  
   1. Skapa frontend-IP-adresser
      1. IP-adress 192.168.14.9 för ASCS
         1. Öppna belastningsutjämnaren, Välj IP-adresspool på klientdelen och klicka på Lägg till
         1. Ange namnet på den nya frontend IP-poolen (till exempel **klientdel. QAS. ASCS**)
         1. Ange tilldelningen till statiskt och ange IP-adressen (till exempel **192.168.14.9**)
         1. Klicka på OK
      1. IP-adress 192.168.14.10 för ASCS ERS
         * Upprepa stegen under ”a” så här skapar du en IP-adress för ÄNDARE (till exempel **192.168.14.10** och **klientdel. QAS. ÄNDARE**)
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
         * Upprepa stegen ovan under ”d” för portar 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16- och TCP för ASCS ERS


> [!IMPORTANT]
> Aktivera inte TCP tidsstämplarna för virtuella Azure-datorer är placerade bakom Azure Load Balancer. Aktivera TCP tidsstämplar genereras hälsoavsökningar misslyckas. Ange parametern **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [hälsoavsökningar för belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Skapa Pacemaker kluster

Följ stegen i [konfigurerar Pacemaker på Red Hat Enterprise Linux i Azure](high-availability-guide-rhel-pacemaker.md) för att skapa ett grundläggande kluster Pacemaker för detta (A) SCS-server.

### <a name="prepare-for-sap-netweaver-installation"></a>Förbereda för installation av SAP NetWeaver

Följande objekt har prefixet antingen **[A]** – gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Konfigurera matcha värdnamn

   Du kan använda en DNS-server, eller så kan du ändra i/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

    ```
    sudo vi /etc/hosts
    ```

   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet till matchar din miljö

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  Skapa SAP kataloger i Azure NetApp Files-volym.  
   Montera tillfälligt Azure NetApp Files-volym på en av de virtuella datorerna och skapa SAP-kataloger (sökvägar).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr + jag/sapmnt/QAS sudo chattr + jag /usr/sap/trans sudo chattr + jag /usr/sap/QAS/SYS sudo chattr + jag /usr/sap/QAS/ASCS00 sudo chattr + jag /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y installera nfs-utils resurs-agenter resurs-agenter-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Läsa in plugin-program: langpacks, produkt-id, Sök-inaktiverad-lagringsplatser
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata är än 2 veckor. Installera yum-cron? Eller kör: yum makecache snabb
   # <a name="installed-packages"></a>Installerade paket
   # <a name="name---------resource-agents-sap"></a>Name        : resource-agents-sap
   # <a name="arch---------x8664"></a>Arch        : x86_64
   # <a name="version------395"></a>Version     : 3.9.5
   # <a name="release------124el7"></a>Version: 124.el7
   # <a name="size---------100-k"></a>Storlek: 100 k
   # <a name="repo---------installed"></a>Lagringsplats: installerad
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>Från lagringsplats: rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Sammanfattning: SAP-klusteragenterna resurs och anslutningen skript
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL: https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>Licens: GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Beskrivning: SAP resource agenter och anslutningen skriptet gränssnitt med
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Pacemaker att SAP-instanser som ska hanteras i ett kluster
   #          <a name="-environment"></a>: miljö.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi/etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Lägg till följande rader i fstab, spara och avsluta
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS nfs rw, hård, rsize = 65536 wsize = 65536, drivrutiner = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw, hård, rsize = 65536 wsize = 65536, drivrutiner = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw, hård, rsize = 65536, wsize = 65536, drivrutiner = 3
   ```

   Mount the new shares

   ```
   sudo mount - a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Ange egenskapen ResourceDisk.EnableSwap till y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Skapa och använda växlingsfil på resursdisk.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Ange storleken på växlingsfilen med egenskapen ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Ledigt utrymme på resursdisk varierar beroende på VM-storlek. Se till att du inte anger ett värde som är för stor. Du kan kontrollera växlingsutrymme med kommandot swapon
   # <a name="size-of-the-swapfile"></a>Storleken på växlingsfil.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo-tjänsten waagent omstart
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo datorer noden vänteläge anftstsapcl2
   
   sudo datorer resursen Skapad fs_QAS_ASCS filsystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     Directory = ”/ usr/sap/QAS/ASCS00” fstype = 'nfs ”\
     --grupp g QAS_ASCS
   
   sudo datorer resursen Skapad vip_QAS_ASCS IPaddr2 \
     IP = 192.168.14.9 cidr_netmask = 24 \
     --grupp g QAS_ASCS
   
   sudo datorer resource skapa nc_QAS_ASCS azure lb port = 62000 \
     --grupp g QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   status för sudo-datorer
   
   # <a name="node-anftstsapcl2-standby"></a>Noden anftstsapcl2: vänteläge
   # <a name="online--anftstsapcl1-"></a>Online: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>Fullständig lista över resurser:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>Resursgrupp: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Tillåt åtkomst till SWPM. Den här regeln är inte permanent. Du måste köra kommandot igen om du startar om datorn.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo ändra qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo datorer noden unstandby anftstsapcl2 sudo datorer noden vänteläge anftstsapcl1
   
   sudo datorer resursen Skapad fs_QAS_AERS filsystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     Directory = ”/ usr/sap/QAS/ERS01” fstype = 'nfs ”\
    --grupp g QAS_AERS

   sudo datorer resursen Skapad vip_QAS_AERS IPaddr2 \
     IP = 192.168.14.10 cidr_netmask = 24 \
    --grupp g QAS_AERS
   
   sudo datorer resource skapa nc_QAS_AERS azure lb port = 62101 \
    --grupp g QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   status för sudo-datorer
   
   # <a name="node-anftstsapcl1-standby"></a>Noden anftstsapcl1: vänteläge
   # <a name="online--anftstsapcl2-"></a>Online: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>Fullständig lista över resurser:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>Resursgrupp: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>Resursgrupp: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Tillåt åtkomst till SWPM. Den här regeln är inte permanent. Du måste köra kommandot igen om du startar om datorn.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo ändra qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Ändra omstartskommandot till ett start-kommando
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = lokal $(_EN) pf=$(_PF)
   Start_Program_01 = lokal $(_EN) pf=$(_PF)
   
   # <a name="add-the-keep-alive-parameter"></a>Lägg till parametern keep alive
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Ändra omstartskommandot till ett start-kommando
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>ta bort Autostart från ÄNDARE profil
   # <a name="autostart--1"></a>Autostart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Ändra konfigurationen för Linux-system
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>På noden där du installerade ASCS och kommentera ut följande rad
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ASCS00/exe: $LD_LIBRARY_PATH; Exportera LD_LIBRARY_PATH; /usr/SAP/QAS/ASCS00/exe/sapstartsrv pf = / usr/sap/QAS/SYS/profil/QAS_ASCS00_anftstsapvh - D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>På noden där du installerade ÄNDARE och kommentera ut följande rad
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo datorer egenskapsuppsättning underhållsläge = true
   
    sudo datorer resursen Skapad rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    Meta resurs-varaktighet = 5000 migrering tröskelvärde = 1 \
    --grupp g QAS_ASCS
   
    sudo datorer resursen Skapad rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --grupp g QAS_AERS
      
    sudo datorer begränsningen samordning Lägg till g-QAS_AERS med g-QAS_ASCS-5000 sudo datorer begränsningen plats rsc_sap_QAS_ASCS00 regeln poäng = 2000 runs_ers_QAS eq 1 sudo datorer begränsningen ordning g QAS_ASCS sedan g QAS_AERS typ = valfritt symmetrisk = false
    
    sudo datorer noden unstandby anftstsapcl1 sudo datorer egenskapsuppsättning underhållsläge = false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo datorer egenskapsuppsättning underhållsläge = true
    
    sudo datorer resursen Skapad rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    Meta resurs-varaktighet = 5000 \
    --grupp g QAS_ASCS
   
    sudo datorer resursen Skapad rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --grupp g QAS_AERS
      
    sudo datorer begränsningen samordning Lägg till g-QAS_AERS med g-QAS_ASCS-5000 sudo datorer begränsningen ordning g QAS_ASCS sedan g QAS_AERS typ = valfritt symmetrisk = false
   
    sudo datorer noden unstandby anftstsapcl1 sudo datorer egenskapsuppsättning underhållsläge = false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    status för sudo-datorer
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>Fullständig lista över resurser:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>Resursgrupp: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>Resursgrupp: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Avsökningsporten till ASCS
   sudo-brandväggen-cmd--zon = offentligt - Lägg till port = 62000/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 62000/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3200/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3200/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3600/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3600/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3900/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3900/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 8100/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 8100/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50013/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50013/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50014/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50014/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50016/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50016/tcp
   # <a name="probe-port-of-ers"></a>Avsökningsporten av ÄNDARE
   sudo-brandväggen-cmd--zon = offentligt - Lägg till port = 62101/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 62101/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3301/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 3301/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50113/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50113/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50114/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50114/tcp sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50116/tcp--permanent sudo brandväggen-cmd--zon = offentligt - Lägg till port = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi/etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>IP-adress för klientdel belastningsutjämningskonfigurationen för SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>IP-adress för klientdel belastningsutjämningskonfigurationen för SAP NetWeaver ASCS ÄNDARE
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr + jag/sapmnt/QAS sudo chattr + jag /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y installera nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi/etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Lägg till följande rader i fstab, spara och avsluta
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3 192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount - a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr + jag /usr/sap/QAS/D02
   
   sudo vi/etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Lägga till följande rad i fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>Montera
   sudo mount - a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr + jag /usr/sap/QAS/D03
   
   sudo vi/etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Lägga till följande rad i fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>Montera
   sudo mount - a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Ange egenskapen ResourceDisk.EnableSwap till y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Skapa och använda växlingsfil på resursdisk.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Ange storleken på växlingsfilen med egenskapen ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Ledigt utrymme på resursdisk varierar beroende på VM-storlek. Se till att du inte anger ett värde som är för stor. Du kan kontrollera växlingsutrymme med kommandot swapon
   # <a name="size-of-the-swapfile"></a>Storleken på växlingsfil.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo-tjänsten waagent omstart
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore lista
   ```

   This should list all entries and should look similar to
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   VIKTIGA STANDARD ENV: 192.168.14.4:30313 ANVÄNDARE: SAPABAP1   DATABASE: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su - qasadm hdbuserstore standard qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~] # datorer resursflytt rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~] # datorer resource Rensa rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Ta bort misslyckade åtgärder för ÄNDARE som inträffat som en del av migreringen
   [root@anftstsapcl1 ~] # datorer resource Rensa rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~] # echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [anftstsapcl1 anftstsapcl2]
   
   Fullständig lista över resurser:
   
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   
   Misslyckade åtgärder:
   * rsc_sap_QAS_ERS01_monitor_11000 på anftstsapcl1 'körs inte ”(7): anropa = 45, status = klar exitreason ='',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] # datorer resource Rensa rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~] # pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] # datorer resource Rensa rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~] # datorer resource Rensa rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~] # pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # datorer resource Rensa rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~] # datorer resource Rensa rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~] # pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # datorer resource Rensa rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Igång anftstsapcl1 resursgrupp: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Igång anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Igång anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl1 resursgrupp: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Igång anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Igång anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Igång anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Igång anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
