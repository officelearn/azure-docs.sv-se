---
title: Konfigurera SAP HANA-Systemreplikering på virtuella Azure-datorer (VM) | Microsoft Docs
description: Skapa hög tillgänglighet för SAP HANA på Azure virtual machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 1be3c411a208a2a9da1a4f6a319fdf37cc8aa2dd
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669052"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hög tillgänglighet för SAP HANA på Azure virtuella datorer på Red Hat Enterprise Linux

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

Du kan använda antingen HANA System Replication eller använder delad lagring för att upprätta hög tillgänglighet för SAP HANA för lokal utveckling.
På Azure-datorer (VM) är HANA System Replication i Azure för närvarande endast stöd för funktionen för hög tillgänglighet.
SAP HANA Replication består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

Den här artikeln beskriver hur du distribuerar och konfigurerar de virtuella datorerna, installera kluster-ramverket och installera och konfigurera SAP HANA System Replication.
I exempelkonfigurationer, installationskommandon, antal instanser **03**, och HANA System-ID **HN1** används.

Läs följande SAP Notes och papers först:

* SAP-kommentar [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-program.
  * Viktiga kapacitetsinformation för Azure VM-storlekar.
  * SAP-program som stöds, och operativsystem (OS) och kombinationer av databasen.
  * Den obligatoriska SAP kernel-versionen för Windows och Linux på Microsoft Azure.
* SAP-kommentar [2015553] visar en lista över kraven för distribution av SAP-stöd för SAP-programvara i Azure.
* SAP-kommentar [2002167] rekommenderar OS-inställningar för Red Hat Enterprise Linux
* SAP-kommentar [2009879] har SAP HANA riktlinjer för Red Hat Enterprise Linux
* SAP-kommentar [2178632] mer information om all övervakning mått som rapporterats för SAP i Azure.
* SAP-kommentar [2191498] har Värdagenten för SAP-version som krävs för Linux i Azure.
* SAP-kommentar [2243692] har licensieringsinformation SAP på Linux i Azure.
* SAP-kommentar [1999351] innehåller ytterligare felsökningsinformation för Azure förbättrad övervakning av tillägget för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Azure virtuella datorer, planering och implementering av SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP HANA-systemreplikering på pacemaker kluster](https://access.redhat.com/articles/3004101)
* Allmänna RHEL-dokumentation
  * [Översikt över tillägg för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Hög tillgänglighet tillägg Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referens för tillägg för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure specifika RHEL-dokumentationen:
  * [Stöd för principer för RHEL-kluster för hög tillgänglighet – Microsoft Azure-datorer som medlemmar i ett kluster](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera en Red Hat Enterprise Linux 7.4 (och senare) hög tillgänglighet-kluster på Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Installera SAP HANA på Red Hat Enterprise Linux för användning i Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Översikt

SAP HANA är installerad på två virtuella datorer för att uppnå hög tillgänglighet. Data replikeras med hjälp av HANA System Replication.

![Översikt över hög tillgänglighet för SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA System Replication installationen använder en dedikerad virtuell värdnamn och virtuella IP-adresser. På Azure måste en belastningsutjämnare använda en virtuell IP-adress. I följande lista visas konfigurationen av belastningsutjämnaren:

* Konfiguration på klientsidan: IP-adress 10.0.0.13 för hn1-db
* Backend-konfiguration: Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i HANA System Replication
* Avsökningsporten: Port 62503
* Belastningsutjämningsregler: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Distribuera för Linux

Azure Marketplace innehåller en bild för Red Hat Enterprise Linux 7.4 för SAP HANA som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda en av de snabbstartsmallarna som finns på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighetsuppsättning och så vidare.
Om du vill distribuera mallen genom att följa dessa steg:

1. Öppna den [databasen mallen] [ template-multisid-db] på Azure portal.
1. Ange följande parametrar:
    * **SAP-System-ID**: Ange ID för SAP-system för SAP-system som du vill installera. ID: T används som ett prefix för de resurser som distribueras.
    * **OS-typ**: Välj en av Linux-distributioner. Det här exemplet väljer **RHEL 7**.
    * **DB-typ**: Välj **HANA**.
    * **SAP-systemstorlek**: Ange hur många SAP som kommer att ge det nya systemet. Om du inte är säker på hur många SAP kräver att systemet genom att be din SAP-teknikpartner eller systemintegratör.
    * **Systemets tillgänglighet**: Välj **HA**.
    * **Administratören Username, administratörslösenord eller SSH-nyckeln**: En ny användare skapas som kan användas för att logga in på datorn.
    * **Undernät-ID**: Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T vanligtvis ser ut som **/subscriptions/\<prenumerations-ID > /resourceGroups/\<resursgruppens namn > /providers/Microsoft.Network/virtualNetworks/\<virtuellt nätverksnamn > /subnets/ \<undernätets namn >**. Lämna tomt om du vill skapa ett nytt virtuellt nätverk

### <a name="manual-deployment"></a>Manuell distribution

1. Skapa en resursgrupp.
1. Skapa ett virtuellt nätverk.
1. Skapa en tillgänglighetsuppsättning.  
   Ange den maximala uppdateringsdomänen.
1. Skapa en belastningsutjämnare (internt).
   * Välj det virtuella nätverket som skapades i steg 2.
1. Skapa virtuell dator 1.  
   Använd minst Red Hat Enterprise Linux 7.4 för SAP HANA. Det här exemplet används Red Hat Enterprise Linux 7.4 för SAP HANA-avbildningen <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Välj tillgänglighetsuppsättning som skapades i steg 3.
1. Skapa virtuell dator 2.  
   Använd minst Red Hat Enterprise Linux 7.4 för SAP HANA. Det här exemplet används Red Hat Enterprise Linux 7.4 för SAP HANA-avbildningen <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Välj tillgänglighetsuppsättning som skapades i steg 3.
1. Lägga till datadiskar.
1. Konfigurera belastningsutjämnaren. Börja med att skapa en IP-adresspool på klientsidan:

   1. Öppna belastningsutjämnaren, Välj **IP-adresspool på klientdelen**, och välj **Lägg till**.
   1. Ange namnet på den nya IP-adresspoolen på klientsidan (till exempel **hana-klientdel**).
   1. Ange den **tilldelning** till **statiska** och ange IP-adressen (till exempel **10.0.0.13**).
   1. Välj **OK**.
   1. När den nya IP-adresspoolen på klientsidan har skapats kan du notera pool IP-adressen.

1. Skapa sedan en backend pool:

   1. Öppna belastningsutjämnaren, Välj **serverdelspooler**, och välj **Lägg till**.
   1. Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
   1. Välj **lägga till en virtuell dator**.
   1. Välj tillgänglighetsuppsättning som skapades i steg 3.
   1. Välj de virtuella datorerna för SAP HANA-klustret.
   1. Välj **OK**.

1. Skapa sedan en hälsoavsökning:

   1. Öppna belastningsutjämnaren, Välj **hälsoavsökningar**, och välj **Lägg till**.
   1. Ange namnet på den nya hälsoavsökningen (till exempel **hana hp**).
   1. Välj **TCP** som protokoll och port 625**03**. Behåll den **intervall** värdet 5, och **tröskelvärde för ej felfri** värdet 2.
   1. Välj **OK**.

1. För SAP HANA 1.0, skapar du regler för belastningsutjämning:

   1. Öppna belastningsutjämnaren, Välj **belastningsutjämningsregler**, och välj **Lägg till**.
   1. Ange namnet på den nya belastningsutjämningsregeln (till exempel hana-lb-3**03**15).
   1. Välj den frontend IP-adressen, backend poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-klientdel**).
   1. Behåll den **protokollet** inställd **TCP**, och ange port 3**03**15.
   1. Öka den **timeout för inaktivitet** till 30 minuter.
   1. Se till att **aktivera flytande IP**.
   1. Välj **OK**.
   1. Upprepa dessa steg för port 3**03**17.

1. Skapa belastningsutjämningsregler för systemdatabasen för SAP HANA 2.0:

   1. Öppna belastningsutjämnaren, Välj **belastningsutjämningsregler**, och välj **Lägg till**.
   1. Ange namnet på den nya belastningsutjämningsregeln (till exempel hana-lb-3**03**13).
   1. Välj den frontend IP-adressen, backend poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-klientdel**).
   1. Behåll den **protokollet** inställd **TCP**, och ange port 3**03**13.
   1. Öka den **timeout för inaktivitet** till 30 minuter.
   1. Se till att **aktivera flytande IP**.
   1. Välj **OK**.
   1. Upprepa dessa steg för port 3**03**14.

1. För SAP HANA 2.0 först skapa belastningsutjämningsregler för klientdatabasen:

   1. Öppna belastningsutjämnaren, Välj **belastningsutjämningsregler**, och välj **Lägg till**.
   1. Ange namnet på den nya belastningsutjämningsregeln (till exempel hana-lb-3**03**40).
   1. Välj klientdelens IP-adress, serverdelspool och hälsoavsökning som du skapade tidigare (till exempel **hana-klientdel**).
   1. Behåll den **protokollet** inställd **TCP**, och ange port 3**03**40.
   1. Öka den **timeout för inaktivitet** till 30 minuter.
   1. Se till att **aktivera flytande IP**.
   1. Välj **OK**.
   1. Upprepa dessa steg för portar 3**03**41 och 3**03**42.

Mer information om portarna som krävs för SAP HANA, finns i kapitlet [anslutningar till klientdatabaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) i den [klientdatabaser för SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) guide eller [SAP anteckning 2388694][2388694].

> [!IMPORTANT]
> Aktivera inte TCP tidsstämplarna för virtuella Azure-datorer är placerade bakom Azure Load Balancer. Aktivera TCP tidsstämplar genereras hälsoavsökningar misslyckas. Ange parametern **net.ipv4.tcp_timestamps** till **0**. Mer information finns i [hälsoavsökningar för belastningsutjämnaren](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).
> Se även SAP anteckning [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Installera SAP HANA

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller nod 1.
* **[2]**: Steget gäller nod 2 i Pacemaker-klustret.

1. **[A]**  Konfigurera disklayouten: **Logical Volume Manager (LVM)**.

   Vi rekommenderar att du använder LVM för volymer som lagrar data och loggfiler. I följande exempel förutsätter att de virtuella datorerna har fyra datadiskar som används för att skapa två volymer.

   Lista över alla tillgängliga diskar:

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

   Skapa en volym-grupp för datafiler. Använd en volym grupp för loggfilerna och en för den delade katalogen på SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Skapa logiska volymer. En linjär volym skapas när du använder `lvcreate` utan den `-i` växla. Vi rekommenderar att du skapar en stripe-volym för bättre prestanda för i/o, där den `-i` argumentet ska vara numret på den underliggande fysiska volymen. I det här dokumentet har två fysiska volymer används för datavolym, så den `-i` switch-argumentet **2**. En fysisk volym används för loggvolymen, så att du inte `-i` växel används. Använd den `-i` växla och ge den värdet numret på den underliggande fysiska volymen när du använder mer än en fysisk volym för varje data, logg eller klusterdelade volymer.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Skapa mount-kataloger och kopiera UUID för alla logiska volymer:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Skapa `fstab` poster för de tre logiska volymerna:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Infoga följande rad i den `/etc/fstab` fil:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Montera de nya volymerna:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Konfigurera disklayouten: **Vanlig diskar**.

   Du kan placera dina HANA-data och loggfiler lagras på en disk för demo-system. Skapa en partition på /dev/disk/azure/scsi1/lun0 och formatera den med xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Infoga den här raden i filen/etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Skapa målkatalogen och montera disken:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]**  Konfigurera matcha värdnamn för alla värdar.

   Du kan använda en DNS-server, eller så kan du ändra filen/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Lägg till följande rader i filen/etc/hosts. Ändra IP-adressen och värdnamnet till matchar din miljö:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  RHEL för HANA-konfiguration

   Konfigurera RHEL enligt beskrivningen i SAP-kommentar [2292690] och [2455582] och <https://access.redhat.com/solutions/2447641>.

1. **[A]**  Installera SAP HANA

   Så här installerar du SAP HANA-Systemreplikering <https://access.redhat.com/articles/3004101>.

   * Kör den **hdblcm** programmet från DVD-skivan för HANA. Ange följande värden i Kommandotolken:
   * Välj installation: Ange **1**.
   * Välj ytterligare komponenter för installation: Ange **1**.
   * Ange installationssökvägen [/ hana/delade]: Välj ange.
   * Ange namn på lokal värd [.]: Välj ange.
   * Vill du lägga till ytterligare värdar i systemet? (j/n) [n]: Välj ange.
   * Ange SAP HANA System-ID: Ange SID HANA, till exempel: **HN1**.
   * Ange instansnummer [00]: Ange numret HANA-instans. Ange **03** om du har använt mallar för Azure eller följt manuell distribution i den här artikeln.
   * Välj databasen läge / ange Index [1]: Välj ange.
   * Välj systemanvändning / ange Index [4]: Välj användarvärde system.
   * Ange platsen för datavolymer [/ hana/data/HN1]: Välj ange.
   * Ange platsen för Loggvolymerna [/ hana/log/HN1]: Välj ange.
   * Begränsa maximal minnesallokering? [n]: Välj ange.
   * Ange Certifikatvärdnamn för värd ”...” [...]: Välj ange.
   * Ange SAP värd Agent-användare (sapadm) lösenord: Ange värden agenten användarens lösenord.
   * Bekräfta SAP värd Agent-användare (sapadm) lösenord: Ange värden agenten användarens lösenord igen för att bekräfta.
   * Ange systemadministratören (hdbadm) lösenord: Ange lösenord.
   * Bekräfta systemadministratören (hdbadm) lösenord: Ange lösenord igen för att bekräfta.
   * Ange arbetskatalog för System-administratör [/ usr/sap/HN1/home]: Välj ange.
   * Ange System administratör inloggningsgränssnitt [/ bin/sh]: Välj ange.
   * Ange systemadministratören användar-ID [1001]: Välj ange.
   * Ange ID för användargrupp (sapsys) [79]: Välj ange.
   * Ange databaslösenord för användare (SYSTEM): Ange lösenordet för användaren.
   * Bekräfta databas-användarlösenord (SYSTEM): Ange databas användarens lösenord igen för att bekräfta.
   * Starta om systemet efter omstart av datorn? [n]: Välj ange.
   * Vill du fortsätta? (y/n): Verifiera sammanfattningen. Ange **y** att fortsätta.

1. **[A]**  Uppgradera Värdagenten SAP.

   Ladda ned det senaste Värdagenten för SAP-arkivet från den [SAP Software Center] [ sap-swcenter] och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar till den fil som du laddade ned:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]**  Konfigurera brandväggen

   Skapa brandväggsregel för Azure load balancer avsökningsporten.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2.0-Systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller nod 1.
* **[2]**: Steget gäller nod 2 i Pacemaker-klustret.

1. **[A]**  Konfigurera brandväggen

   Skapa brandväggsregler för att tillåta trafik för HANA System Replication och klienten. Portarna som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel som tillåter trafik för HANA 2.0 System Replication och klienten till databas SYSTEMDB, HN1 och NW1.

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

1. **[1]**  Skapa klientdatabasen.

   Om du använder SAP HANA 2.0 eller MDC, skapa en klientdatabas för SAP NetWeaver-system. Ersätt **NW1** med SID för SAP-system.

   Köra som < hanasid\>adm följande kommando:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurera Systemreplikering på den första noden:

   Säkerhetskopiera databaser som < hanasid\>adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopiera PKI systemfiler till den sekundära platsen:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Skapa den primära platsen:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]**  System Konfigurera replikering på den andra noden:
    
   Registrera den andra noden för att starta systemreplikering. Kör följande kommando som < hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]**  Kontrollera replikeringsstatus

   Kontrollera replikeringsstatus och vänta tills alla databaser är synkroniserade. Om status är okänd, kontrollera dina brandväggsinställningar.

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

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera SAP HANA 1.0-Systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller nod 1.
* **[2]**: Steget gäller nod 2 i Pacemaker-klustret.

1. **[A]**  Konfigurera brandväggen

   Skapa brandväggsregler för att tillåta trafik för HANA System Replication och klienten. Portarna som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel för att tillåta HANA 2.0 System Replication. Anpassa det till din SAP HANA 1.0-installation.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]**  Skapa nödvändiga användarna.

   Kör följande kommando som rot. Ersätt fetstil strängar (HANA System-ID **HN1** och antal instanser **03**) med värdena för SAP HANA-installation:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]**  Skapa nyckellagerposten.

   Kör följande kommando som rot för att skapa en ny keystore-post:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]**  Säkerhetskopiera databasen.

   Säkerhetskopiera databaserna som rot:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Om du använder en installation, bör du också säkerhetskopiera klientdatabasen:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Konfigurera Systemreplikering på den första noden.

   Skapa den primära platsen som < hanasid\>adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurera Systemreplikering på den sekundära noden.

   Registrera den sekundära platsen som < hanasid\>adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Skapa ett Pacemaker-kluster

Följ stegen i [konfigurerar Pacemaker på Red Hat Enterprise Linux i Azure](high-availability-guide-rhel-pacemaker.md) att skapa ett grundläggande Pacemaker kluster för den här HANA-servern.

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA-klusterresurser

Installera SAP HANA-resource-agenterna på **alla noder**. Se till att aktivera en databas som innehåller paketet.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Därefter skapa HANA-topologi. Kör följande kommandon på en av noderna i Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Skapa sedan HANA-resurser:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som resurserna som körs.

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

## <a name="test-the-cluster-setup"></a>Testa konfiguration

Det här avsnittet beskrivs hur du kan testa din konfiguration. Kontrollera att Pacemaker har inte några misslyckade åtgärder (via status för datorer), det finns inga oväntat Platsbegränsningar (till exempel rester av ett test för migrering) och att HANA är synkroniseringstillstånd, till exempel med systemReplicationStatus innan du startar ett test:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testa migreringen

Resurstillstånd innan du startar testet:

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

Om du ställer in `AUTOMATED_REGISTER="false"`, det här kommandot bör migrera SAP HANA-huvudnod och den grupp som innehåller den virtuella IP-adressen till hn1-db-1.

När migreringen är klar 'sudo datorer status' utdata ser ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

SAP HANA-resursen på hn1-db-0 har stoppats. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migreringen skapar Platsbegränsningar som måste tas bort igen:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Övervaka tillståndet för HANA-resursen med hjälp av status för datorer. När HANA har startats på hn1-db-0, utdata bör se ut så här

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testa att hägna in Azure-agenten

Resurstillstånd innan du startar testet:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Du kan testa installationen av agenten att hägna in Azure genom att inaktivera nätverksgränssnittet på noden där den kör SAP HANA som Master.
Se [Red Hat Knowledgebase-artikeln 79523](https://access.redhat.com/solutions/79523) för en beskrivning av hur du simulerar ett nätverksfel. I det här exemplet använder vi net_breaker skriptet för att blockera all åtkomst till nätverket.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Den virtuella datorn måste nu starta om eller stoppa beroende på din klusterkonfiguration.
Om du ställer in den `stonith-action` inställningen på av den virtuella datorn har stoppats och resurserna som migreras till den aktiva virtuella datorn.

> [!NOTE]
> Det kan ta upp till 15 minuter tills de virtuella datorerna är online igen.

När du startar den virtuella datorn igen går SAP HANA-resurs som inte går att starta som sekundär om du ställer in `AUTOMATED_REGISTER="false"`. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

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

Resurstillstånd innan du startar testet:

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

Efter redundansen, kan du starta klustret igen. Om du ställer in `AUTOMATED_REGISTER="false"`, SAP HANA-resursen på hn1-db-0-nod som inte går att starta som sekundär. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

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

* [Azure virtuella datorer, planering och implementering av SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure (stora instanser) i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md)
