---
title: Virtuella Azure-datorer med hög tillgänglighet för SAP NW på RHEL med Azure NetApp-filer| Microsoft-dokument
description: Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux
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
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351250"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

I den hÃ¤r artikeln beskrivs hur du distribuerar virtuella datorer, konfigurerar virtuella datorer, installerar klusterramverket och installerar ett högnÃ¤ndentÃ¤ges SAP NetWeaver 7.50-system med [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
I exempelkonfigurationerna, installationskommandon etc. ASCS-instansen är nummer 00, ERS-instansen är nummer 01, PAS -instans (Primär application instance) är 02 och programinstansen (AAS) är 03. SAP System ID QAS används. 

Databaslagret beskrivs inte i detalj i den här artikeln.  

Läs följande SAP Notes och papers först:

* [Dokumentation om Azure NetApp Files][anf-azure-doc] 
* SAP Note [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-programvara
  * Viktig kapacitetsinformation för Azure VM-storlekar
  * Sap-programvara och kombinationer av operativsystem (operativsystem) och databas
  * Nödvändig SAP-kärnversion för Windows och Linux på Microsoft Azure

* SAP Note [2015553] listar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure.
* SAP Note [2002167] har rekommenderat OS-inställningar för Red Hat Enterprise Linux
* SAP Note [2009879] har SAP HANA Riktlinjer för Red Hat Enterprise Linux
* SAP Note [2178632] har detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [2191498] har den nödvändiga SAP Host Agent-versionen för Linux i Azure.
* SAP Note [2243692] har information om SAP-licensiering på Linux i Azure.
* SAP Note [1999351] har ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Planering och implementering av Virtuella Azure-datorer för SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP Netweaver i pacemakerkluster](https://access.redhat.com/articles/3150081)
* Allmän RHEL-dokumentation
  * [Tilläggsöversikt för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration av tillägg med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Tilläggsreferens för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurera ASCS/ERS för SAP Netweaver med fristående resurser i RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurera SAP S/4HANA ASCS/ERS med fristående enqueue Server 2 (ENSA2) i Pacemaker på RHEL](https://access.redhat.com/articles/3974941)
* Azure-specifik RHEL-dokumentation:
  * [Supportprinciper för RHEL-kluster med hög tillgänglighet – Virtuella Microsoft Azure-datorer som klustermedlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera ett Red Hat Enterprise Linux 7.4 -kluster med hög tillgänglighet på Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp-filer][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

Hög tillgänglighet (HA) för SAP Netweaver centrala tjänster kräver delad lagring.
För att uppnå det på Red Hat Linux hittills var det nödvändigt att bygga separata högtillgängliga GlusterFS kluster. 

Nu är det möjligt att uppnå SAP Netweaver HA med hjälp av delad lagring, distribueras på Azure NetApp-filer. Om du använder Azure NetApp-filer för delad lagring elimineras behovet av ytterligare [GlusterFS-kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Pacemaker behövs fortfarande för HA av SAP Netweaver centrala tjänster (ASCS / SCS).

![ÖVERSIKT ÖVER HÖG TILLGÄNGLIGHET I SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS och SAP HANA-databasen använder virtuella värdnamn och virtuella IP-adresser. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Vi rekommenderar att du använder [standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). Följande lista visar konfigurationen av belastningsutjämnaren med separata frontend-IPs för (A)SCS och ERS.

### <a name="ascs"></a>A. Scs

* Frontend-konfiguration
  * IP-adress 192.168.14.9
* Avsökningsport
  * Port 620<strong>&lt;nr&gt;</strong>
* Regler för belastningsutjämning
  * Om du använder Standard belastningsutjämnare väljer du **HA-portar**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 36<strong>&lt;&gt; nr</strong> TCP
  * 39<strong>&lt;&gt; nr</strong> TCP
  * 81<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Frontend-konfiguration
  * IP-adress 192.168.14.10
* Avsökningsport
  * Port 621<strong>&lt;nr&gt;</strong>
* Regler för belastningsutjämning
  * Om du använder Standard belastningsutjämnare väljer du **HA-portar**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 33<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

* Serverningskonfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i (A)SCS/ERS-klustret

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurera Azure NetApp Files-infrastrukturen 

SAP NetWeaver kräver delad lagring för transport- och profilkatalogen.  Innan du fortsätter med installationen för Azure NetApp-filer infrastruktur, bekanta dig med [Azure NetApp Files dokumentation][anf-azure-doc]. Kontrollera om din valda Azure-region erbjuder Azure NetApp-filer. Följande länk visar tillgängligheten för Azure NetApp-filer per Azure-region: [Azure NetApp-filers tillgänglighet per Azure-region][anf-avail-matrix].

Azure NetApp-filer är tillgängliga i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Innan du distribuerar Azure NetApp-filer, begär introduktion till Azure NetApp-filer, i och med [instruktionerna för Registrera för Azure NetApp-filer][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp-filresurser  

Stegen förutsätter att du redan har distribuerat [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Azure NetApp-filresurserna och de virtuella datorerna, där Azure NetApp-filresurserna ska monteras, måste distribueras i samma Virtuella Azure-nätverk eller i peer-azure-virtuella nätverk.  

1. Om du inte redan har gjort det begär du [introduktion till Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Skapa NetApp-kontot i den valda Azure-regionen, enligt [instruktionerna för att skapa NetApp-konto](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Konfigurera Azure NetApp Files kapacitetspool, i följande [anvisningar om hur du konfigurerar Azure NetApp Files kapacitetspool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
SAP Netweaver-arkitekturen som presenteras i den här artikeln använder en enda Azure NetApp-filkapacitetspool, Premium SKU. Vi rekommenderar Azure NetApp Files Premium SKU för SAP Netweaver-programarbetsbelastning på Azure.  
4. Delegera ett undernät till Azure NetApp-filer enligt [instruktionerna Delegera ett undernät till Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuera Azure NetApp-filer volymer, följ [instruktionerna för att skapa en volym för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Distribuera volymerna i det angivna [Undernätet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)För Azure NetApp-filer . Tänk på att Azure NetApp-filresurserna och virtuella Azure-datorer måste finnas i samma Virtuella Azure-nätverk eller i peer-azure-virtuella nätverk. I det här exemplet använder vi två Azure NetApp-filer volymer: sap<b>QAS</b> och transSAP. De filsökvägar som är monterade på motsvarande monteringspunkter är /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, etc.  

   1. volymsav<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volymsav<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS-ascs)</b>
   3. volymsav<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volymsav<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volymtransSAP (nfs://192.168.24.4/transSAP)
   6. volymsav<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volym sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
I det här exemplet använde vi Azure NetApp-filer för alla SAP Netweaver-filsystem för att visa hur Azure NetApp-filer kan användas. SAP-filsystem som inte behöver monteras via NFS kan också distribueras som [Azure-disklagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . I det här exemplet måste <b>a-e</b> vara på Azure NetApp-filer och <b>f-g</b> (det vill säga /usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03</b>) kan distribueras som Azure-disklagring. 

### <a name="important-considerations"></a>Att tänka på

När du överväger Azure NetApp-filer för SAP Netweaver på SUSE arkitektur med hög tillgänglighet bör du vara medveten om följande viktiga överväganden:

- Den minsta kapaciteten poolen är 4 TiB. Kapaciteten poolstorlek kan ökas i 1 TiB steg.
- Den minsta volymen är 100 GiB
- Azure NetApp-filer och alla virtuella datorer, där Azure NetApp-filer volymer kommer att monteras, måste vara i samma Azure Virtual Network eller i [peered virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) i samma region. Azure NetApp-filer åtkomst via VNET-peering i samma region stöds nu. Azure NetApp-åtkomst över global peering stöds ännu inte.
- Det valda virtuella nätverket måste ha ett undernät som delegerats till Azure NetApp-filer.
- Azure NetApp Files erbjuder [exportprincip:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)du kan styra tillåtna klienter, åtkomsttypen (Skriv&, Skrivskyddat osv.). 
- Azure NetApp Files-funktionen är inte zonmedveten ännu. Azure NetApp Files-funktionen distribueras inte i alla tillgänglighetszoner i en Azure-region. Var medveten om de potentiella latenskonsekvenserna i vissa Azure-regioner. 
- Azure NetApp Files-volymer kan distribueras som NFSv3- eller NFSv4.1-volymer. Båda protokollen stöds för SAP-programskiktet (ASCS/ERS, SAP-programservrar). 

## <a name="setting-up-ascs"></a>Ställa in (A)SCS

I det här exemplet distribuerades resurserna manuellt via [Azure-portalen](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuera Linux manuellt via Azure Portal

Först måste du skapa Azure NetApp Files volymer. Distribuera de virtuella datorerna. Därefter skapar du en belastningsutjämnare och använder de virtuella datorerna i serverdapoolen.

1. Skapa belastningsutjämnare (intern, standard):  
   1. Skapa IP-adresser för klientdel
      1. IP-adress 192.168.14.9 för ASCS
         1. Öppna belastningsutjämnaren, välj IP-pool med klientdel och klicka på Lägg till
         1. Ange namnet på den nya ip-poolen för frontend (till exempel **frontend. Qas. ASCS**)
         1. Ange tilldelningen till Statisk och ange IP-adressen (till exempel **192.168.14.9**)
         1. Klicka på OK
      1. IP-adress 192.168.14.10 för ASCS ERS
         * Upprepa stegen ovan under "a" för att skapa en IP-adress för ERS (till exempel **192.168.14.10** och **frontend. Qas. ERS**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **backend. QAS**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj Virtuell dator. 
      1. Välj de virtuella datorerna i (A)SCS-klustret och deras IP-adresser.
      1. Klicka på Lägg till
   1. Skapa hälsoavsökningar
      1. Port 620**00** för ASCS
         1. Öppna belastningsutjämnaren, välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **hälsotillstånd. Qas. ASCS**)
         1. Välj TCP som protokoll, port 620**00**, behåll intervall 5 och felfritt tröskelvärde 2
         1. Klicka på OK
      1. Port 621**01** för ASCS ERS
            * Upprepa stegen ovan under "c" för att skapa en hälsoavsökning för ERS (till exempel 621**01** och **hälsa. Qas. ERS**)
   1. Regler för belastningsutjämning
      1. Belastningsutjämningsregler för ASCS
         1. Öppna belastningsutjämnaren, välj Belastningsutjämningsregler och klicka på Lägg till
         1. Ange namnet på den nya belastningsutjämnarens regel (till exempel **lb. Qas. ASCS**)
         1. Välj ip-adressen för klientdel för ASCS, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **frontend. Qas. ASCS**, **backend. QAS** och **hälsa. Qas. ASCS**)
         1. Välj **HA-portar**
         1. Öka tidsgränsen för inaktiv tid till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
         * Upprepa stegen ovan för att skapa belastningsutjämningsregler för ERS (till exempel **lb. Qas. ERS**)
1. Om ditt scenario kräver grundläggande belastningsutjämnare (intern) gör du så här:  
   1. Skapa IP-adresser för klientdel
      1. IP-adress 192.168.14.9 för ASCS
         1. Öppna belastningsutjämnaren, välj IP-pool med klientdel och klicka på Lägg till
         1. Ange namnet på den nya ip-poolen för frontend (till exempel **frontend. Qas. ASCS**)
         1. Ange tilldelningen till Statisk och ange IP-adressen (till exempel **192.168.14.9**)
         1. Klicka på OK
      1. IP-adress 192.168.14.10 för ASCS ERS
         * Upprepa stegen ovan under "a" för att skapa en IP-adress för ERS (till exempel **192.168.14.10** och **frontend. Qas. ERS**)
   1. Skapa serverdelspoolen
      1. Öppna belastningsutjämnaren, välj backend-pooler och klicka på Lägg till
      1. Ange namnet på den nya backend-poolen (till exempel **backend. QAS**)
      1. Klicka på Lägg till en virtuell dator.
      1. Välj den tillgänglighetsuppsättning som du skapade tidigare för ASCS 
      1. Välj virtuella datorer i (A)SCS-klustret
      1. Klicka på OK
   1. Skapa hälsoavsökningar
      1. Port 620**00** för ASCS
         1. Öppna belastningsutjämnaren, välj hälsoavsökningar och klicka på Lägg till
         1. Ange namnet på den nya hälsoavsökningen (till exempel **hälsotillstånd. Qas. ASCS**)
         1. Välj TCP som protokoll, port 620**00**, behåll intervall 5 och felfritt tröskelvärde 2
         1. Klicka på OK
      1. Port 621**01** för ASCS ERS
            * Upprepa stegen ovan under "c" för att skapa en hälsoavsökning för ERS (till exempel 621**01** och **hälsa. Qas. ERS**)
   1. Regler för belastningsutjämning
      1. 32**00** TCP för ASCS
         1. Öppna belastningsutjämnaren, välj Belastningsutjämningsregler och klicka på Lägg till
         1. Ange namnet på den nya belastningsutjämnarens regel (till exempel **lb. Qas. ASCS.3200**)
         1. Välj ip-adressen för klientdel för ASCS, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **frontend. Qas. ASCS**)
         1. Behåll protokoll **TCP**, ange port **3200**
         1. Öka tidsgränsen för inaktiv tid till 30 minuter
         1. **Se till att aktivera flytande IP**
         1. Klicka på OK
      1. Ytterligare portar för ASCS
         * Upprepa stegen ovan under "d" för portarna 36**00,** 39**00,** 81**00,** 5**00**13, 5**00**14, 5**00**16 och TCP för ASCS
      1. Ytterligare portar för ASCS ERS
         * Upprepa stegen ovan under "d" för portarna 32**01,** 33**01,** 5**01**13, 5**01**14, 5**01**16 och TCP för ASCS ERS

      > [!Note]
      > När virtuella datorer utan offentliga IP-adresser placeras i serverdelspoolen för intern (ingen offentlig IP-adress) Standard Azure load balancer, kommer det inte att finnas någon utgående internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutning finns [i Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

      > [!IMPORTANT]
      > Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placeras bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange parameter **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [belastningsutjämnares hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Inaktivera ID-mappning (om NFSv4.1)

Instruktionerna i det här avsnittet är endast tillämpliga om du använder Azure NetApp Files-volymer med NFSv4.1-protokollet. Utför konfigurationen på alla virtuella datorer, där Azure NetApp Files NFSv4.1-volymer ska monteras.  

1. Verifiera NFS-domäninställningen. Kontrollera att domänen är konfigurerad som standarddomän för Azure **`defaultv4iddomain.com`** NetApp Files, dvs och mappningen är inställd på **ingen**.  

    > [!IMPORTANT]
    > Se till att ange NFS-domänen `/etc/idmapd.conf` på den virtuella datorn så **`defaultv4iddomain.com`** att den matchar standarddomänkonfigurationen på Azure NetApp-filer: . Om det finns en obalans mellan domänkonfigurationen på NFS-klienten (dvs. den virtuella datorn) och NFS-servern, d.v.s. Azure NetApp-konfigurationen, visas behörigheterna `nobody`för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Verifiera . Det bör ställas in på **Y**. Om du vill `nfs4_disable_idmapping` skapa katalogstrukturen där den finns kör du kommandot montera. Du kommer inte att kunna skapa katalogen manuellt under /sys/modules, eftersom åtkomst är reserverad för kärnan/drivrutinerna.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Mer information om hur `nfs4_disable_idmapping` du https://access.redhat.com/solutions/1749883ändrar parameter finns i .

### <a name="create-pacemaker-cluster"></a>Skapa pacemakerkluster

Följ stegen i [Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure](high-availability-guide-rhel-pacemaker.md) för att skapa ett grundläggande Pacemaker-kluster för den här (A)SCS-servern.

### <a name="prepare-for-sap-netweaver-installation"></a>Förbered för SAP NetWeaver-installation

Följande objekt föregås av antingen **[A]** - som gäller för alla noder, **[1]** - endast gäller för nod 1 eller **[2]** - endast gäller för nod 2.

1. **[A]** Namnmatchning för installationsprogrammet

   Du kan antingen använda en DNS-server eller ändra /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon

    ```
    sudo vi /etc/hosts
    ```

   Infoga följande rader till /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö

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

1. **[1]** Skapa SAP-kataloger i Azure NetApp Files-volymen.  
   Montera azure netapp-filvolymen tillfälligt på en av de virtuella datorerna och skapa SAP-kataloger (filsökvägar).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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
    ``` 

1. **[A]** Skapa delade kataloger

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Installera NFS-klient och andra krav

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Kontrollera version av resursagenter-sap

   Kontrollera att versionen av det installerade resursagenter-sap-paketet är minst 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Lägg till monteringsposter

   Om du använder NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Om du använder NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Se till att matcha NFS-protokollversionen av Azure NetApp Files-volymerna när volymerna monteras. Om Azure NetApp Files-volymerna skapas som NFSv3-volymer använder du motsvarande NFSv3-konfiguration. Om Azure NetApp Files-volymerna skapas som NFSv4.1-volymer följer du instruktionerna för att inaktivera ID-mappning och se till att använda motsvarande NFSv4.1-konfiguration. I det här exemplet skapades Azure NetApp Files-volymerna som NFSv3-volymer.  

   Montera de nya aktierna

   ```
   sudo mount -a  
   ```

1. **[A]** Konfigurera SWAP-fil

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Starta om agenten för att aktivera ändringen

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL-konfiguration

   Konfigurera RHEL enligt beskrivningen i SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Installera SAP NetWeaver ASCS/ERS

1. **[1]** Skapa en virtuell IP-resurs och hälsoavsökning för ASCS-instansen

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som rot på den första noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för frontadkonfigurationen för belastningsutjämnarhanteraren för ASCS, till exempel <b>anftstsapvh</b>, <b>192.168.14.9</b> och det instansnummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>00</b>.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Om installationen inte kan skapa en undermapp i /usr/sap/**QAS**/ASCS**00**kan du prova att ange ägare och grupp för ASCS**00-mappen** och försöka igen.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Skapa en virtuell IP-resurs och hälsoavsökning för ERS-instansen

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Installera SAP NetWeaver ERS  

   Installera SAP NetWeaver ERS som rot på den andra noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnadsfrontendkonfigurationen för ERS, till exempel <b>anftstsapers</b>, <b>192.168.14.10</b> och det instansnummer som du använde för avsökningen av lastbalanseraren, till exempel <b>01</b>.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Om installationen inte kan skapa en undermapp i /usr/sap/**QAS**/ERS**01**kan du prova att ange ägare och grupp för mappen**ERS 01** och försök igen.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Anpassa ASCS/SCS- och ERS-instansprofilerna

   * ASCS/SCS-profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS-profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Konfigurera Keep Alive

   Kommunikationen mellan SAP NetWeaver-programservern och ASCS/SCS dirigeras via en belastningsutjämnare för programvara. Belastningsutjämnaren kopplar från inaktiva anslutningar efter en konfigurerbar timeout. För att förhindra detta måste du ange en parameter i PROFILEN SAP NetWeaver ASCS/SCS och ändra Linux-systeminställningarna. Läs [SAP Note 1410736][1410736] för mer information.

   Parametern ASCS/SCS-profil enque/encni/set_so_keepalive har redan lagts till i det sista steget.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Uppdatera filen /usr/sap/sapservices

   För att förhindra att instanserna startar av sapinitstartskriptet måste alla instanser som hanteras av Pacemaker kommenteras ut från filen /usr/sap/sapservices. Kommentera inte UT SAP HANA-instansen om den ska användas med HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Skapa SAP-klusterresurserna

   Om du använder enqueue server 1-arkitektur (ENSA1) definierar du resurserna på följande sätt:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP introducerade stöd för enqueue server 2, inklusive replikering, från och med SAP NW 7.52. Från och med ABAP Platform 1809 installeras enqueue server 2 som standard. Se SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) för stöd för enqueue server 2.
   Om du använder enqueue server 2-arkitektur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)installerar du resursagentens resursagenter-sap-4.1.1-12.el7.x86_64 eller nyare och definierar resurserna enligt följande:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Om du uppgraderar från en äldre version och byter till enqueue server 2 läser du SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Tidsutgångarna i ovanstående konfiguration är bara exempel och kan behöva anpassas till den specifika SAP-installationen. 

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Lägg till brandväggsregler för ASCS och ERS på båda noderna Lägg till brandväggsregler för ASCS och ERS på båda noderna.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver programserver förberedelse

   Vissa databaser kräver att installationen av databasinstansen körs på en programserver. Förbered virtuella datorer för programservern för att kunna använda dem i dessa fall.  

   Stegen nedan förutsätter att du installerar programservern på en annan server än ASCS/SCS- och HANA-servrarna. Annars behövs inte några av stegen nedan (som att konfigurera värdnamnsmatchning).  

   Följande punkter föregås av antingen **[A]** - som gäller både PAS och AAS, **[P]** - endast tillämpliga på PAS eller **[S]** - endast gäller för AAS.  

1. **[A]** Installation värdnamnsmatchning Du kan antingen använda en DNS-server eller ändra /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon:  

   ```
   sudo vi /etc/hosts
   ```

   Infoga följande rader till /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Skapa sapmntkatalogen Skapa sapmntkatalogen.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Installera NFS-klient och andra krav  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Lägg till monteringsposter  
   Om du använder NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Om du använder NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Montera de nya aktierna

   ```
   sudo mount -a
   ```

1. **[P]** Skapa och montera PAS-katalogen  
   Om du använder NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Om du använder NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Skapa och montera AAS-katalogen  
   Om du använder NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Om du använder NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Konfigurera SWAP-fil
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Starta om agenten för att aktivera ändringen

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Installera databas

I det här exemplet installeras SAP NetWeaver på SAP HANA. Du kan använda alla databaser som stöds för den här installationen. Mer information om hur du installerar SAP HANA i Azure finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Köra installationen av SAP-databasinstansen

   Installera SAP NetWeaver-databasinstansen som root med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för klientkonfigurationen för belastningsutjämnarhanteraren för databasen.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>INSTALLATION av PROGRAMserver för SAP NetWeaver

Så här installerar du en SAP-programserver.

1. Förbereda programserver

   Följ stegen i kapitlet [SAP NetWeaver programserver förberedelse](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) ovan för att förbereda programservern.

1. Installera SAP NetWeaver-programserver

   Installera en primär eller ytterligare SAP NetWeaver-programserver.

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Uppdatera SECURE STORE FÖR SAP HANA

   Uppdatera det säkra SAP HANA-arkivet så att det pekar på det virtuella namnet på SAP HANA System Replication-konfigurationen.

   Kör följande kommando för att \<lista posterna som sapsid>adm

   ```
   hdbuserstore List
   ```

   Detta bör lista alla poster och bör se ut ungefär som
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Utdata visar att IP-adressen för standardposten pekar på den virtuella datorn och inte på belastningsutjämnarens IP-adress. Den här posten måste ändras så att den pekar på lastbalansens virtuella värdnamn. Se till att använda samma port **(30313** i utdata ovan) och databasnamn **(QAS** i utdata ovan)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testa klusterinställningarna

1. Migrera ASCS-instansen manuellt

   Resurstillstånd innan testet påbörjas:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Kör följande kommandon som rot för att migrera ASCS-instansen.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurstillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simulera nodkrasch

   Resurstillstånd innan testet påbörjas:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Kör följande kommando som rot på noden där ASCS-instansen körs

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Statusen när noden har startats igen ska se ut så här.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Använd följande kommando för att rensa de misslyckade resurserna.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurstillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kill meddelandeserver process

   Resurstillstånd innan testet påbörjas:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Kör följande kommandon som root för att identifiera meddelandeserverns process och döda den.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Om du bara dödar meddelandeservern en gång, kommer den att startas om av `sapstart`. Om du dödar den tillräckligt ofta flyttar Pacemaker så småningom ASCS-instansen till den andra noden. Kör följande kommandon som root för att rensa resurstillståndet för ASCS- och ERS-instansen efter testet.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurstillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Döda enqueue server process

   Resurstillstånd innan testet påbörjas:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Kör följande kommandon som rot på noden där ASCS-instansen körs för att döda enqueue-servern.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   ASCS-instansen bör omedelbart växla över till den andra noden. ERS-instansen bör också växla över när ASCS-instansen har startats. Kör följande kommandon som root för att rensa resurstillståndet för ASCS- och ERS-instansen efter testet.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurstillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kill enqueue replikeringsserver process

   Resurstillstånd innan testet påbörjas:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Kör följande kommando som rot på noden där ERS-instansen körs för att döda enqueue-replikeringsserverprocessen.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Om du bara kör `sapstart` kommandot en gång startar du om processen. Om du kör det `sapstart` tillräckligt ofta, kommer inte att starta om processen och resursen kommer att vara i ett stoppat tillstånd. Kör följande kommandon som rot för att rensa resurstillståndet för ERS-instansen efter testet.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resurstillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Döda enqueue sapstartsrv process

   Resurstillstånd innan testet påbörjas:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Kör följande kommandon som rot på noden där ASCS körs.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Sapstartsrv-processen ska alltid startas om av Pacemaker-resursagenten som en del av övervakningen. Resurstillstånd efter testet:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Nästa steg

* [HA för SAP NW på Virtuella Azure-datorer på RHEL för SAP-program med flera SID-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md).
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (virtuella datorer)][sap-hana-ha]
