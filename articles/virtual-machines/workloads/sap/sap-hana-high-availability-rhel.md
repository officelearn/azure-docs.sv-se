---
title: Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på RHEL | Microsoft-dokument
description: Upprätta hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VIRTUELLA datorer).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/31/2020
ms.author: radeltch
ms.openlocfilehash: f1ae2c3c949e8bdbf30c8bef496177d56cd2dcbd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521397"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

För lokal utveckling kan du använda antingen HANA System Replication eller använda delad lagring för att skapa hög tillgänglighet för SAP HANA.
På virtuella Azure-datorer är HANA System Replication på Azure för närvarande den enda funktion som stöds med hög tillgänglighet.
SAP HANA Replication består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

I den här artikeln beskrivs hur du distribuerar och konfigurerar virtuella datorer, installerar klusterramverket och installerar och konfigurerar SAP HANA System Replication.
I exempelkonfigurationerna används installationskommandon, instansnummer **03**och HANA System-ID **HN1.**

Läs följande SAP Notes och papers först:

* SAP Note [1928533], som har:
  * Listan över Azure VM-storlekar som stöds för distribution av SAP-programvara.
  * Viktig kapacitetsinformation för Azure VM-storlekar.
  * Sap-programvara och operativsystem (OPERATIVSYSTEM) och databaskombinationer som stöds.
  * Den nödvändiga SAP-kärnversionen för Windows och Linux på Microsoft Azure.
* SAP Note [2015553] listar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure.
* SAP Note [2002167] har rekommenderat OS-inställningar för Red Hat Enterprise Linux
* SAP Note [2009879] har SAP HANA Riktlinjer för Red Hat Enterprise Linux
* SAP Note [2178632] har detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [2191498] har den nödvändiga SAP Host Agent-versionen för Linux i Azure.
* SAP Note [2243692] har information om SAP-licensiering på Linux i Azure.
* SAP Note [1999351] har ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Planering och implementering av Virtuella Azure-datorer för SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP HANA-systemreplikering i pacemakerkluster](https://access.redhat.com/articles/3004101)
* Allmän RHEL-dokumentation
  * [Tilläggsöversikt för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration av tillägg med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Tilläggsreferens för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-specifik RHEL-dokumentation:
  * [Supportprinciper för RHEL-kluster med hög tillgänglighet – Virtuella Microsoft Azure-datorer som klustermedlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera ett Red Hat Enterprise Linux 7.4 -kluster med hög tillgänglighet på Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Installera SAP HANA på Red Hat Enterprise Linux för användning i Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet installeras SAP HANA på två virtuella datorer. Data replikeras med hjälp av HANA System Replication.

![ÖVERSIKT ÖVER HÖG TILLGÄNGLIGHET I SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA System Replication-installationen använder en dedikerad virtuellt värdnamn och virtuella IP-adresser. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. Följande lista visar konfigurationen av belastningsutjämnaren:

* Front-end konfiguration: IP-adress 10.0.0.13 för hn1-db
* Backend-konfiguration: Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i HANA System Replication
* Sondport: Port 62503
* Belastningsutjämningsregler: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Distribuera för Linux

Azure Marketplace innehåller en avbildning för Red Hat Enterprise Linux 7.4 för SAP HANA som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda en av snabbstartsmallarna som finns på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighetsuppsättningen och så vidare.
Så här distribuerar du mallen:

1. Öppna [databasmallen][template-multisid-db] på Azure-portalen.
1. Ange följande parametrar:
    * **Sap System-ID:** Ange SAP-system-ID för SAP-systemet som du vill installera. ID används som prefix för de resurser som distribueras.
    * **Os Typ:** Välj en av Linux-distributioner. I det här exemplet väljer du **RHEL 7**.
    * **Db Typ**: Välj **HANA**.
    * **Sap System Storlek:** Ange antalet SAPS som det nya systemet kommer att ge. Om du är osäker på hur många SAPS systemet behöver, fråga din SAP Technology Partner eller System Integrator.
    * **Systemtillgänglighet:** Välj **HA**.
    * **Administratörsanvändarnamn, adminlösenord eller SSH-nyckel:** En ny användare skapas som kan användas för att logga in på datorn.
    * **Undernäts-ID:** Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat att den virtuella datorn ska tilldelas, namnger du ID:t för det specifika undernätet. ID:t ser vanligtvis ut som **\</subscriptions/subscription ID\<>/resourceGroups/resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/undernätsnamn\<>**. Lämna tomt om du vill skapa ett nytt virtuellt nätverk

### <a name="manual-deployment"></a>Manuell distribution

1. Skapa en resursgrupp.
1. Skapa ett virtuellt nätverk.
1. Skapa en tillgänglighetsuppsättning.  
   Ange den maximala uppdateringsdomänen.
1. Skapa en belastningsutjämnare (intern). Vi rekommenderar [standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).
   * Välj det virtuella nätverk som skapades i steg 2.
1. Skapa virtuell dator 1.  
   Använd minst Red Hat Enterprise Linux 7.4 för SAP HANA. I det här exemplet används Red Hat Enterprise Linux <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 7.4 för SAP HANA-avbildningen Välj den tillgänglighetsuppsättning som skapats i steg 3.
1. Skapa virtuell dator 2.  
   Använd minst Red Hat Enterprise Linux 7.4 för SAP HANA. I det här exemplet används Red Hat Enterprise Linux <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 7.4 för SAP HANA-avbildningen Välj den tillgänglighetsuppsättning som skapats i steg 3.
1. Lägg till datadiskar.
1. Om du använder standardbelastningsutjämnare följer du dessa konfigurationssteg:
   1. Skapa först en IP-pool för fronten:

      1. Öppna belastningsutjämnaren, välj **frontend IP-pool**och välj **Lägg till**.
      1. Ange namnet på den nya IP-poolen för frontend (till exempel **hana-frontend**).
      1. Ange **tilldelningen** till **Statisk** och ange IP-adressen (till exempel **10.0.0.13**).
      1. Välj **OK**.
      1. När den nya ip-poolen i fronten har skapats noterar du poolens IP-adress.

   1. Skapa sedan en backend-pool:

      1. Öppna belastningsutjämnaren, välj **backend-pooler**och välj **Lägg till**.
      1. Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
      1. Välj **Lägg till en virtuell dator**.
      1. Välj ** Virtuell dator**.
      1. Välj virtuella datorer i SAP HANA-klustret och deras IP-adresser.
      1. Välj **Lägg till**.

   1. Skapa sedan en hälsoavsökning:

      1. Öppna belastningsutjämnaren, välj **hälsoavsökningar**och välj **Lägg till**.
      1. Ange namnet på den nya hälsoavsökningen (till exempel **hana-hp**).
      1. Välj **TCP** som protokoll och port 625**03**. Behåll **intervallvärdet** inställt på 5 och det **felaktiga tröskelvärdet** inställt på 2.
      1. Välj **OK**.

   1. Skapa sedan belastningsutjämningsreglerna:
   
      1. Öppna belastningsutjämnaren, välj **belastningsutjämningsregler**och välj **Lägg till**.
      1. Ange namnet på den nya belastningsutjämnarens regel (till exempel **hana-lb**).
      1. Välj frontend IP-adress, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend,** **hana-backend** och **hana-hp**).
      1. Välj **HA-portar**.
      1. Öka **tidsgränsen** för inaktiv tid till 30 minuter.
      1. Se till att **aktivera Flytande IP**.
      1. Välj **OK**.

   > [!Note]
   > När virtuella datorer utan offentliga IP-adresser placeras i serverdelspoolen för intern (ingen offentlig IP-adress) Standard Azure load balancer, kommer det inte att finnas någon utgående internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutning finns [i Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

1. Om ditt scenario dikterar med hjälp av grundläggande belastningsutjämnare gör du så här:
   1. Konfigurera belastningsutjämnaren. Skapa först en IP-pool för fronten:

      1. Öppna belastningsutjämnaren, välj **frontend IP-pool**och välj **Lägg till**.
      1. Ange namnet på den nya IP-poolen för frontend (till exempel **hana-frontend**).
      1. Ange **tilldelningen** till **Statisk** och ange IP-adressen (till exempel **10.0.0.13**).
      1. Välj **OK**.
      1. När den nya ip-poolen i fronten har skapats noterar du poolens IP-adress.

   1. Skapa sedan en backend-pool:

      1. Öppna belastningsutjämnaren, välj **backend-pooler**och välj **Lägg till**.
      1. Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
      1. Välj **Lägg till en virtuell dator**.
      1. Välj den tillgänglighetsuppsättning som skapades i steg 3.
      1. Välj virtuella datorer i SAP HANA-klustret.
      1. Välj **OK**.

   1. Skapa sedan en hälsoavsökning:

      1. Öppna belastningsutjämnaren, välj **hälsoavsökningar**och välj **Lägg till**.
      1. Ange namnet på den nya hälsoavsökningen (till exempel **hana-hp**).
      1. Välj **TCP** som protokoll och port 625**03**. Behåll **intervallvärdet** inställt på 5 och det **felaktiga tröskelvärdet** inställt på 2.
      1. Välj **OK**.

   1. För SAP HANA 1.0 skapar du belastningsutjämningsregler:

      1. Öppna belastningsutjämnaren, välj **belastningsutjämningsregler**och välj **Lägg till**.
      1. Ange namnet på den nya belastningsutjämnarens regel (till exempel hana-lb-3**03**15).
      1. Välj ip-adressen för klientdelen, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll **protokollet** inställt på **TCP**och ange port 3**03**15.
      1. Öka **tidsgränsen** för inaktiv tid till 30 minuter.
      1. Se till att **aktivera Flytande IP**.
      1. Välj **OK**.
      1. Upprepa dessa steg för port 3**03**17.

   1. För SAP HANA 2.0 skapar du belastningsutjämningsreglerna för systemdatabasen:

      1. Öppna belastningsutjämnaren, välj **belastningsutjämningsregler**och välj **Lägg till**.
      1. Ange namnet på den nya belastningsutjämnarens regel (till exempel hana-lb-3**03**13).
      1. Välj ip-adressen för klientdelen, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll **protokollet** inställt på **TCP**och ange port 3**03**13.
      1. Öka **tidsgränsen** för inaktiv tid till 30 minuter.
      1. Se till att **aktivera Flytande IP**.
      1. Välj **OK**.
      1. Upprepa dessa steg för port 3**03**14.

   1. För SAP HANA 2.0 skapar du först belastningsutjämningsreglerna för klientdatabasen:

      1. Öppna belastningsutjämnaren, välj **belastningsutjämningsregler**och välj **Lägg till**.
      1. Ange namnet på den nya belastningsutjämnarens regel (till exempel hana-lb-3**03**40).
      1. Välj den klient-IP-adress, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **hana-frontend**).
      1. Behåll **protokollet** inställt på **TCP**och ange port 3**03**40.
      1. Öka **tidsgränsen** för inaktiv tid till 30 minuter.
      1. Se till att **aktivera Flytande IP**.
      1. Välj **OK**.
      1. Upprepa dessa steg för portarna 3**03**41 och 3**03**42.

Mer information om de portar som krävs för SAP HANA finns i kapitlet [Anslutningar till klientdatabaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) i [SAP HANA-klientdatabaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) eller [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placeras bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange parameter **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [belastningsutjämnares hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Se även SAP note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Installera SAP HANA

I stegen i det här avsnittet används följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast för nod 1.
* **[2]**: Steget gäller endast för nod 2 i Pacemaker-klustret.

1. **[A]** Ställ in disklayouten: **Logisk volymhanterare (LVM)**.

   Vi rekommenderar att du använder LVM för volymer som lagrar data och loggfiler. I följande exempel förutsätts att de virtuella datorerna har fyra datadiskar anslutna som används för att skapa två volymer.

   Lista alla tillgängliga diskar:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Exempel på utdata:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa fysiska volymer för alla diskar som du vill använda:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa en volymgrupp för datafilerna. Använd en volymgrupp för loggfilerna och en för den delade katalogen för SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa de logiska volymerna. En linjär volym skapas `lvcreate` när `-i` du använder utan växeln. Vi föreslår att du skapar en randig volym för bättre I/O-prestanda och anpassa randstorlekarna till de värden som dokumenteras i [SAP HANA VM-lagringskonfigurationer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Argumentet `-i` ska vara antalet underliggande fysiska volymer `-I` och argumentet är randstorleken. I det här dokumentet används två fysiska volymer `-i` för datavolymen, så växelargumentet är inställt på **2**. Stripe-storleken för datavolymen är **256KiB**. En fysisk volym används för loggvolymen, så inga `-i` eller `-I` växlar används uttryckligen för loggvolymkommandona.  

   > [!IMPORTANT]
   > Använd `-i` växeln och ange den till numret på den underliggande fysiska volymen när du använder mer än en fysisk volym för varje data, logg eller delade volymer. Använd `-I` växeln för att ange stripe-storlek när du skapar en stripe-volym.  
   > Se [SAP HANA VM-lagringskonfigurationer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) för rekommenderade lagringskonfigurationer, inklusive stripe-storlekar och antal diskar.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Skapa monteringskatalogerna och kopiera UUID för alla logiska volymer:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Skapa `fstab` poster för de tre logiska volymerna:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Infoga följande rad `/etc/fstab` i filen:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Montera de nya volymerna:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Konfigurera disklayouten: **Plain Disks**.

   För demosystem kan du placera dina HANA-data och loggfiler på en disk. Skapa en partition på /dev/disk/azure/scsi1/lun0 och formatera den med xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Infoga denna rad i filen /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Skapa målkatalogen och montera disken:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Ställ in värdnamnsmatchning för alla värdar.

   Du kan antingen använda en DNS-server eller ändra filen /etc/hosts på alla noder. I det här exemplet visas hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader i filen /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL för HANA-konfiguration

   Konfigurera RHEL enligt beskrivningen i SAP Note [2292690] och <https://access.redhat.com/solutions/2447641> [2455582] och .

1. **[A]** Installera SAP HANA

   Om du vill installera SAP <https://access.redhat.com/articles/3004101>HANA System Replication följer du .

   * Kör **hdblcm-programmet** från HANA DVD. Ange följande värden vid prompten:
   * Välj installation: Ange **1**.
   * Välj ytterligare komponenter för installation: Ange **1**.
   * Ange installationssökväg [/hana/delad]: Välj Retur.
   * Ange lokalt värdnamn [..]: Välj Retur.
   * Vill du lägga till ytterligare värdar i systemet? (y/n) [n]: Välj Retur.
   * Ange SAP HANA-system-ID: Ange SID för HANA, till exempel: **HN1**.
   * Ange förekomstnummer [00]: Ange HANA-förekomstnumret. Ange **03** om du har använt Azure-mallen eller följt avsnittet med manuell distribution i den här artikeln.
   * Välj databasläge / Ange index [1]: Välj Retur.
   * Välj Systemanvändning / Ange index [4]: Välj systemanvändningsvärdet.
   * Ange plats för datavolymer [/hana/data/HN1]: Välj Ange.
   * Ange plats för loggvolymer [/hana/log/HN1]: Välj Retur.
   * Vill du begränsa maximal minnesallokering? [n]: Välj Retur.
   * Ange certifikatvärdnamn för värden '...' [...]: Välj Enter.
   * Ange SAPADM-användarlösenord (SAP Host Agent User): Ange användarlösenordet för värdagenten.
   * Bekräfta SAPADM-användarlösenord (SAP Host Agent User) : Ange värdagentens användarlösenord igen för att bekräfta.
   * Ange lösenord för systemadministratör (hdbadm): Ange lösenordet för systemadministratören.
   * Bekräfta systemadministratör (hdbadm) Lösenord: Ange lösenordet för systemadministratören igen för att bekräfta.
   * Ange systemadministratörens arbetskatalog [/usr/sap/HN1/home]: Välj Retur.
   * Ange systemadministratörsinloggningsgränssnitt [/bin/sh]: Välj Retur.
   * Ange användar-ID för systemadministratör [1001]: Välj Retur.
   * Ange ID för användargrupp (sapsys) [79]: Välj Retur.
   * Ange lösenord för databasanvändare (SYSTEM): Ange databasanvändarlösenordet.
   * Bekräfta lösenord för databasanvändare (SYSTEM) : Ange lösenordet för databasanvändaren igen för att bekräfta.
   * Starta om systemet efter omstart av maskinen? [n]: Välj Retur.
   * Vill du fortsätta? (y/n): Validera sammanfattningen. Ange **y** för att fortsätta.

1. **[A]** Uppgradera SAP-värdagenten.

   Hämta det senaste SAP Host Agent-arkivet från [SAP Software Center][sap-swcenter] och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar på filen som du hämtade:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** Konfigurera brandvägg

   Skapa brandväggsregeln för Azure load balancer-avsökningsporten.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2.0-systemreplikering

I stegen i det här avsnittet används följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast för nod 1.
* **[2]**: Steget gäller endast för nod 2 i Pacemaker-klustret.

1. **[A]** Konfigurera brandvägg

   Skapa brandväggsregler som tillåter HANA-systemreplikering och klienttrafik. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel för att tillåta HANA 2.0 Systemreplikering och klienttrafik till databasen SYSTEMDB, HN1 och NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Skapa klientdatabasen.

   Om du använder SAP HANA 2.0 eller MDC skapar du en klientdatabas för DITT SAP NetWeaver-system. Ersätt **NW1** med SID i DITT SAP-system.

   Kör som <hanasid\>adm följande kommando:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Konfigurera systemreplikering på den första noden:

   Säkerhetskopiera databaserna som <hanasid\>adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopiera systemets PKI-filer till den sekundära platsen:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Skapa den primära platsen:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurera systemreplikering på den andra noden:
    
   Registrera den andra noden för att starta systemreplikeringen. Kör följande kommando som <hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** Kontrollera replikeringsstatus

   Kontrollera replikeringsstatusen och vänta tills alla databaser är synkroniserade. Om statusen förblir OKÄND kontrollerar du brandväggsinställningarna.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera SAP HANA 1.0-systemreplikering

I stegen i det här avsnittet används följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller endast för nod 1.
* **[2]**: Steget gäller endast för nod 2 i Pacemaker-klustret.

1. **[A]** Konfigurera brandvägg

   Skapa brandväggsregler som tillåter HANA-systemreplikering och klienttrafik. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel för att tillåta HANA 2.0 Systemreplikering. Anpassa den till din SAP HANA 1.0-installation.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Skapa de användare som krävs.

   Kör följande kommando som rot. Se till att ersätta fetstil (HANA System ID **HN1** och instansnummer **03)** med värdena för din SAP HANA-installation:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Skapa keystore-posten.

   Kör följande kommando som root för att skapa en ny keystore-post:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Säkerhetskopiera databasen.

   Säkerhetskopiera databaserna som root:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Om du använder en installation med flera innehavare säkerhetskopierar du även klientdatabasen:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Konfigurera systemreplikering på den första noden.

   Skapa den primära platsen som <\>hanasid adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurera systemreplikering på den sekundära noden.

   Registrera den sekundära platsen som <\>hanasid adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Skapa ett Pacemaker-kluster

Följ stegen i [Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure](high-availability-guide-rhel-pacemaker.md) för att skapa ett grundläggande Pacemaker-kluster för den här HANA-servern.

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA-klusterresurser

Installera SAP HANA-resursagenter på **alla noder**. Se till att aktivera en databas som innehåller paketet.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Skapa sedan HANA-topologin. Kör följande kommandon på en av Pacemaker-klusternoderna:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
--clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Skapa sedan HANA-resurserna:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt på vilken nod resurserna körs.

> [!NOTE]
> Tidsutgångarna i ovanstående konfiguration är bara exempel och kan behöva anpassas till den specifika HANA-installationen. Du kan till exempel behöva öka starttidsgränsen om det tar längre tid att starta SAP HANA-databasen.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testa klusterinställningarna

I det här avsnittet beskrivs hur du kan testa konfigurationen. Innan du startar ett test kontrollerar du att Pacemaker inte har någon misslyckad åtgärd (via pcs-status), att det inte finns några oväntade platsbegränsningar (till exempel rester av ett migreringstest) och att HANA är synkroniseringstillstånd, till exempel med systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testa migreringen

Resurstillstånd innan testet påbörjas:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan migrera SAP HANA-huvudnoden genom att köra följande kommando:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Om du `AUTOMATED_REGISTER="false"`anger ska det här kommandot migrera SAP HANA-huvudnoden och gruppen som innehåller den virtuella IP-adressen till hn1-db-1.

När migreringen är klar ser utdata för sudo-datorer ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

SAP HANA-resursen på hn1-db-0 stoppas. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migreringen skapar platsbegränsningar som måste tas bort igen:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Övervaka tillståndet för HANA-resursen med hjälp av "pcs-status". När HANA startas på hn1-db-0, bör utgången se ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testa Azure-fäktningsagenten

Resurstillstånd innan testet påbörjas:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Du kan testa installationen av Azure-stängselagenten genom att inaktivera nätverksgränssnittet på noden där SAP HANA körs som master.
Se [Red Hat Knowledgebase artikel 79523](https://access.redhat.com/solutions/79523) för en beskrivning av hur du simulerar ett nätverksfel. I det här exemplet använder vi skriptet net_breaker för att blockera all åtkomst till nätverket.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Den virtuella datorn ska nu starta om eller stoppa beroende på klusterkonfigurationen.
Om du `stonith-action` ställer in inställningen till av stoppas den virtuella datorn och resurserna migreras till den virtuella datorn som körs.

> [!NOTE]
> Det kan ta upp till 15 minuter tills de virtuella datorerna är online igen.

När du har startat den virtuella datorn igen kan SAP `AUTOMATED_REGISTER="false"`HANA-resursen inte starta som sekundär om du anger . I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Resurstillstånd efter testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testa en manuell redundans

Resurstillstånd innan testet påbörjas:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Du kan testa en manuell redundans genom att stoppa klustret på noden hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Efter redundansen kan du starta klustret igen. Om du `AUTOMATED_REGISTER="false"`anger kan SAP HANA-resursen på noden hn1-db-0 starta som sekundär. I det här fallet konfigurerar du HANA-instansen som sekundär genom att köra det här kommandot:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Resurstillstånd efter testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Nästa steg

* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md)
