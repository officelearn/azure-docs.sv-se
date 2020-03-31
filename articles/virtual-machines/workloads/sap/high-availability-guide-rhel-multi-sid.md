---
title: Azure virtuella datorer hög tillgänglighet för SAP NW på RHEL multi-SID guide | Microsoft-dokument
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
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247443"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP-program med flera SID-program

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

I den här artikeln beskrivs hur du distribuerar flera SAP NetWeaver-system med högtillgängliga system (det vill säga flera SID) i ett kluster med två noder på virtuella Azure-datorer med Red Hat Enterprise Linux för SAP-program.  

I exempelkonfigurationerna distribueras installationskommandon etc. tre SAP NetWeaver 7.50-system i ett enda, två nodkluster med hög tillgänglighet. SAP-systemens SID är:
* **NW1**: ASCS-instansnummer **00** och virtuellt värdnamn **msnw1ascs;** ERS-instansnummer **02** och virtuellt värdnamn **msnw1ers**.  
* **NW2**: ASCS-instansnummer **10** och virtuellt värdnamn **msnw2ascs;** ERS-instansnummer **12** och virtuellt värdnamn **msnw2ers**.  
* **NW3**: ASCS-instansnummer **20** och virtuellt värdnamn **msnw3ascs.** ERS-instansnummer **22** och virtuellt värdnamn **msnw3ers**.  

Artikeln täcker inte databaslagret och distributionen av SAP NFS-resurser. I exemplen i den här artikeln använder vi [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) volym **sapMSID** för NFS-resurser, förutsatt att volymen redan har distribuerats. Vi antar också att Azure NetApp Files-volymen distribueras med NFSv3-protokollet och att följande filsökvägar finns för klusterresurserna för ASCS- och ERS-instanserna av SAP-system nw1, NW2 och NW3:  

* volym sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW1-abc)</b>
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volym sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW2-abc)</b>
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volym sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW3-abc)</b>
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volym sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Innan du börjar läser du först följande SAP-anteckningar och -dokument:

* SAP Note [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-programvara
  * Viktig kapacitetsinformation för Azure VM-storlekar
  * Sap-programvara och kombinationer av operativsystem (operativsystem) och databas
  * Nödvändig SAP-kärnversion för Windows och Linux på Microsoft Azure
* [Dokumentation om Azure NetApp Files][anf-azure-doc]
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

De virtuella datorerna som deltar i klustret måste vara dimensionerade för att kunna köra alla resurser, om redundans skulle inträffa. Varje SAP SID kan växla över oberoende av varandra i multi-SID hög tillgänglighet kluster.  

För att uppnå hög tillgänglighet kräver SAP NetWeaver högtillgänglighet. I den här dokumentationen presenterar vi exemplen med SAP-resurser som distribueras på [Azure NetApp Files NFS-volymer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Det är också möjligt att vara värd för resurserna på högt tillgängliga [GlusterFS-kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), som kan användas av flera SAP-system.  

![ÖVERSIKT ÖVER HÖG TILLGÄNGLIGHET I SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Stödet för multi-SID-klustring av SAP ASCS/ERS med Red Hat Linux som gästoperativsystem i Virtuella Azure-datorer är begränsat till **fem** SAP-SID-SID på samma kluster. Varje nytt SID ökar komplexiteten. En blandning av SAP Enqueue Replication Server 1 och Enqueue Replication Server 2 i samma kluster **stöds inte**. Multi-SID-klustring beskriver installationen av flera SAP ASCS/ERS-instanser med olika SID i ett Pacemaker-kluster. För närvarande stöds multi-SID-kluster endast för ASCS/ERS.  

> [!TIP]
> Multi-SID-klustring av SAP ASCS/ERS är en lösning med högre komplexitet. Det är mer komplicerat att genomföra. Det innebär också högre administrativa insatser, när du utför underhållsaktiviteter (som OS-korrigering). Innan du påbörjar den faktiska implementeringen, ta dig tid att noggrant planera distributionen och alla inblandade komponenter som virtuella datorer, NFS-fästen, VIP-enheter, belastningsutjämnarekonfigurationer och så vidare.  

SAP NetWeaver ASCS, SAP NetWeaver SCS och SAP NetWeaver ERS använder virtuella värdnamn och virtuella IP-adresser. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Vi rekommenderar att du använder [standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal).  

Följande lista visar konfigurationen av (A)SCS och ERS belastningsutjämnare för det här multi-SID-klusterexemplet med tre SAP-system. Du behöver separata frontend IP, hälsoavsökningar och belastningsutjämningsregler för varje ASCS- och ERS-instans för var och en av SID-korten. Tilldela alla virtuella datorer, som ingår i ASCS/ASCS-klustret till en serverdelspool i en enda ILB.  

### <a name="ascs"></a>A. Scs

* Frontend-konfiguration
  * IP-adress för NW1: 10.3.1.50
  * IP-adress för NW2: 10.3.1.52
  * IP-adress för NW3: 10.3.1.54

* Avsökningsportar
  * Port 620<strong>&lt;&gt;nr</strong>, därför för NW1, NW2 och NW3 sondportar 620**00**, 620**10** och 620**20**
* Belastningsutjämningsregler - skapa en för varje instans, det vill ha NW1/ASCS, NW2/ASCS och NW3/ASCS.
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
  * IP-adress för NW1 10.3.1.51
  * IP-adress för NW2 10.3.1.53
  * IP-adress för NW3 10.3.1.55

* Avsökningsport
  * Port 621<strong>&lt;&gt;nr</strong>, därför för NW1, NW2 och N3 sondportar 621**02**, 621**12** och 621**22**
* Belastningsutjämningsregler - skapa en för varje instans, det vill än NW1/ERS, NW2/ERS och NW3/ERS.
  * Om du använder Standard belastningsutjämnare väljer du **HA-portar**
  * Om du använder Grundläggande belastningsutjämnare skapar du belastningsutjämningsregler för följande portar
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Serverningskonfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i (A)SCS/ERS-klustret

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i serverdelspoolen för intern (ingen offentlig IP-adress) Standard Azure load balancer, kommer det inte att finnas någon utgående internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutning finns [i Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placeras bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange parameter **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [belastningsutjämnares hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="sap-shares"></a>SAP-aktier

SAP NetWeaver kräver delad lagring för transport, profilkatalog och så vidare. För högtillgänge med SAP-system är det viktigt att ha högtillgängande aktier. Du måste bestämma arkitekturen för dina SAP-resurser. Ett alternativ är att distribuera resurser på [Azure NetApp Files NFS-volymer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Med Azure NetApp-filer får du inbyggd hög tillgänglighet för SAP NFS-resurser.

Ett annat alternativ är att bygga [GlusterFS på Azure virtuella datorer på Red Hat Enterprise Linux för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), som kan delas mellan flera SAP-system. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Distribuera det första SAP-systemet i klustret

Nu när du har bestämt dig för arkitekturen för SAP-resurser distribuerar du det första SAP-systemet i klustret, enligt motsvarande dokumentation.

* Om du använder Azure NetApp Files NFS-volymer följer du [Azure VMs hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* Om du använder GlusterFS-klustret följer du [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs).  

De dokument som anges ovan kommer att guida dig genom stegen för att förbereda den nödvändiga infrastrukturen, bygga klustret, förbereda operativsystemet för att köra SAP-programmet.  

> [!TIP]
> Testa alltid funktionen felväxling i klustret, efter att det första systemet har distribuerats, innan du lägger till ytterligare SAP-SID i klustret. På så sätt vet du att klusterfunktionen fungerar innan du lägger till komplexiteten i ytterligare SAP-system i klustret.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Distribuera ytterligare SAP-system i klustret

I det här exemplet antar vi att systemet **NW1** redan har distribuerats i klustret. Vi visar hur du distribuerar i klustret SAP-system **NW2** och **NW3**. 

Följande objekt föregås av antingen **[A]** - som gäller för alla noder, **[1]** - endast gäller för nod 1 eller **[2]** - endast gäller för nod 2.

### <a name="prerequisites"></a>Krav 

> [!IMPORTANT]
> Innan du följer instruktionerna för att distribuera ytterligare SAP-system i klustret följer du instruktionerna för att distribuera det första SAP-systemet i klustret, eftersom det finns steg som bara är nödvändiga under den första systemdistributionen.  

Denna dokumentation förutsätter att:
* Pacemaker-klustret är redan konfigurerat och körs.  
* Minst ett SAP-system (ASCS / ERS-instans) har redan distribuerats och körs i klustret.  
* Funktionen för klusterväxling har testats.  
* NFS-resurserna för alla SAP-system distribueras.  

### <a name="prepare-for-sap-netweaver-installation"></a>Förbered för SAP NetWeaver-installation

1. Lägg till konfiguration för det nyligen distribuerade systemet (det vill **ha NW2**, **NW3**) i den befintliga Azure Load Balancer, enligt instruktionerna [Distribuera Azure Load Balancer manuellt via Azure Portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal). Justera IP-adresser, hälsoavsökningsportar, belastningsutjämningsregler för din konfiguration.  

2. **[A]** Inställningsnamnmatchning för ytterligare SAP-system. Du kan antingen använda `/etc/hosts` DNS-servern eller ändra på alla noder. I det här exemplet `/etc/hosts` visas hur du använder filen.  Anpassa IP-adresserna och värdnamnen till din miljö. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** Skapa delade kataloger för ytterligare **NW2-** och **NW3** SAP-system som du distribuerar till klustret. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Lägg till monteringsposterna för filsystemen /sapmnt/SID och /usr/sap/SID/SYS för de ytterligare SAP-system som du distribuerar till klustret. I det här exemplet **NW2** och **NW3**.  

   Uppdatera `/etc/fstab` filen med filsystemen för de ytterligare SAP-system som du distribuerar till klustret.  

   * Om du använder Azure NetApp-filer följer du instruktionerna [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)  
   * Om du använder GlusterFS-kluster följer du instruktionerna [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Installera ASCS / ERS

1. Skapa de virtuella IP- och hälsoavsökningsklusterresurserna för ASCS-instanserna för de ytterligare SAP-system som du distribuerar till klustret. Exemplet som visas här är för **NW2** och **NW3** ASCS, med NFS på Azure NetApp Files volymer med NFSv3-protokoll.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.  

2. **[1]** Installera SAP NetWeaver ASCS  

   Installera SAP NetWeaver ASCS som root med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för frontend-konfigurationen för belastningsutjämnarhanteraren för ASCS. För system **NW2**är till exempel det virtuella värdnamnet <b>msnw2ascs</b>, <b>10.3.1.52</b> och det instansnummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>10</b>. För system **NW3**är det virtuella värdnamnet <b>msnw3ascs</b>, <b>10.3.1.54</b> och det instansnummer som du använde för avsökningen av belastningsutjämnaren, till exempel <b>20</b>. Anm.) ned på vilken klusternod du installerade ASCS för varje SAP SID.  

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst. Du kan använda parameter SAPINST_USE_HOSTNAME för att installera SAP med hjälp av virtuellt värdnamn.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Om installationen misslyckas med att skapa en undermapp i /usr/sap/**SID**/ASCS**Instance#** kan du försöka ställa in ägaren till **sid**adm och gruppera till sapsys för ASCS**Instance#** och försök igen.

3. **[1]** Skapa en virtuell IP- och hälsoavsökningsklusterkluster för ERS-instansen för det ytterligare SAP-system som du distribuerar till klustret. Exemplet som visas här är för **NW2** och **NW3** ERS, med NFS på Azure NetApp Files volymer med NFSv3-protokoll.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Kontrollera att klusterstatusen är ok och att alla resurser har startats.  

   Kontrollera sedan att resurserna i den nyligen skapade ERS-gruppen körs på klusternoden, mittemot klusternoden där ASCS-instansen för samma SAP-system installerades.  Om till exempel NW2 ASCS `rhelmsscl1`installerades på kontrollerar du att `rhelmsscl2`NW2 ERS-gruppen körs på .  Du kan migrera NW2 ERS-gruppen till genom att `rhelmsscl2` köra följande kommando för en av klusterresurserna i gruppen: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Installera SAP NetWeaver ERS

   Installera SAP NetWeaver ERS som root på den andra noden med hjälp av ett virtuellt värdnamn som mappar till IP-adressen för belastningsutjämnarfrontendkonfigurationen för ERS. Till exempel för system **NW2**, den virtuella värdnamnet kommer <b>att msnw2ers</b>, <b>10.3.1.53</b> och instansnummer som du använde för avsökningen av lastbalanseraren, till exempel <b>12</b>. För system **NW3**, den virtuella värdnamnet <b>msnw3ers</b>, <b>10.3.1.55</b> och instansnumret som du använde för avsökningen av lastbalanseraren, till exempel <b>22</b>. 

   Du kan använda den sapinst parametern SAPINST_REMOTE_ACCESS_USER för att tillåta en icke-root-användare att ansluta till sapinst. Du kan använda parameter SAPINST_USE_HOSTNAME för att installera SAP med hjälp av virtuellt värdnamn.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Använd SWPM SP 20 PL 05 eller senare. Lägre versioner anger inte behörigheterna korrekt och installationen misslyckas.

   Om installationen misslyckas med att skapa en undermapp i /usr/sap/**NW2**/ERS**Instance#** kan du försöka ställa in ägaren till **sid**adm och gruppen till sapsys i mappen ERS**Instance#** och försök igen.

   Om det var nödvändigt för dig att migrera ERS-gruppen i det nyligen distribuerade SAP-systemet till en annan klusternod, glöm inte att ta bort platsbegränsningen för ERS-gruppen. Du kan ta bort villkoret genom att köra följande kommando (exemplet ges för SAP-system **NW2** och **NW3**). Se till att ta bort de tillfälliga begränsningarna för samma resurs som du använde i kommandot för att flytta ERS-klustergruppen.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Anpassa ASCS/SCS- och ERS-instansprofilerna för de nyinstallerade SAP-systemen. Exemplet nedan är för NW2. Du måste anpassa ASCS/SCS- och ERS-profilerna för alla SAP-instanser som läggs till i klustret.  
 
   * ASCS/SCS-profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * ERS-profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Uppdatera filen /usr/sap/sapservices

   För att förhindra att instanserna startar av sapinitstartskriptet måste alla instanser `/usr/sap/sapservices` som hanteras av Pacemaker kommenteras ut från filen.  Exemplet nedan är för SAP-system **NW2** och **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Skapa SAP-klusterresurserna för det nyinstallerade SAP-systemet.  

   Om du använder enqueue server 1-arkitektur (ENSA1) definierar du resurserna för SAP-systemen **NW2** och **NW3** enligt följande:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP introducerade stöd för enqueue server 2, inklusive replikering, från och med SAP NW 7.52. Från och med ABAP Platform 1809 installeras enqueue server 2 som standard. Se SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) för stöd för enqueue server 2.
   Om du använder enqueue server 2-arkitektur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)definierar du resurserna för SAP-systemen **NW2** och **NW3** enligt följande:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Om du uppgraderar från en äldre version och byter till enqueue server 2 läser du SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Tidsutgångarna i ovanstående konfiguration är bara exempel och kan behöva anpassas till den specifika SAP-installationen. 

   Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.
   I följande exempel visas statusen klusterresurser, efter att SAP-systemen **NW2** och **NW3** har lagts till i klustret. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Lägg till brandväggsregler för ASCS och ERS på båda noderna.  Exemplet nedan visar brandväggsreglerna för både SAP-systemen **NW2** och **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Fortsätt med SAP-installationen 

Slutför DIN SAP-installation genom att:

* [Förbereda dina SAP NetWeaver-programservrar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Installera en DBMS-instans](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Installera en primär SAP-programserver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Installera ytterligare en ELLER flera SAP-programinstanser

## <a name="test-the-multi-sid-cluster-setup"></a>Testa klusterkonfigurationen för flera SID

Följande tester är en delmängd av testfallen i de bästa metoderna guider av Red Hat. De ingår för din bekvämlighet. En fullständig lista över klustertester finns i följande dokumentation:

* Om du använder Azure NetApp Files NFS-volymer följer du [Azure VMs hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* Om du `GlusterFS`använder högtillgängliga följer du [Azure VMs hög tillgänglighet för SAP NetWeaver på RHEL för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel).  

Läs alltid guiderna för bästa praxis i Red Hat och utför alla ytterligare tester som kan ha lagts till.  
Testerna som presenteras finns i ett tvånod, multi-SID-kluster med tre SAP-system installerade.  

1. Migrera ASCS-instansen manuellt. Exemplet visar migrering av ASCS-instansen för SAP-systemet NW3.

   Resurstillstånd innan testet påbörjas:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Kör följande kommandon som root för att migrera NW3 ASCS-instansen.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Resurstillstånd efter testet:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Simulera nodkrasch

   Resurstillstånd innan testet påbörjas:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Kör följande kommando som root på en nod, där minst en ASCS-instans körs. I det här exemplet körde `rhelmsscl1`vi kommandot på , där ASCS-instanserna för NW1, NW2 och NW3 körs.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Statusen efter testet, och efter att noden, som kraschade, har startat igen, ska se ut så här.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Om det finns meddelanden för misslyckade resurser rensar du statusen för de misslyckade resurserna. Ett exempel:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Nästa steg

* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (virtuella datorer)][sap-hana-ha]
