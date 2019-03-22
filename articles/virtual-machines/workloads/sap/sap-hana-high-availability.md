---
title: Hög tillgänglighet för SAP HANA på Azure virtuella datorer på SUSE Linux Enterprise Server | Microsoft Docs
description: Hög tillgänglighet för SAP HANA på Azure virtuella datorer på SUSE Linux Enterprise Server
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
ms.openlocfilehash: f0bac9d50e73ed703905545261e86796ede214e2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180848"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Hög tillgänglighet för SAP HANA på Azure virtuella datorer på SUSE Linux Enterprise Server

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
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Du kan använda antingen HANA System Replication eller använder delad lagring för att upprätta hög tillgänglighet för SAP HANA för lokal utveckling.
På Azure-datorer (VM) är HANA System Replication i Azure för närvarande endast stöd för funktionen för hög tillgänglighet. SAP HANA Replication består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

Den här artikeln beskriver hur du distribuerar och konfigurerar de virtuella datorerna, installera kluster-ramverket och installera och konfigurera SAP HANA System Replication.
I exempelkonfigurationer, installationskommandon, antal instanser **03**, och HANA System-ID **HN1** används.

Läs följande SAP Notes och papers först:

* SAP-kommentar [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-program.
  * Viktiga kapacitetsinformation för Azure VM-storlekar.
  * SAP-program som stöds, och operativsystem (OS) och kombinationer av databasen.
  * Den obligatoriska SAP kernel-versionen för Windows och Linux på Microsoft Azure.
* SAP-kommentar [2015553] beskrivs kraven för distribution av SAP-stöd för SAP-programvara i Azure.
* SAP-kommentar [2205917] rekommenderar OS-inställningar för SUSE Linux Enterprise Server för SAP-program.
* SAP-kommentar [1944799] har SAP HANA riktlinjer för SUSE Linux Enterprise Server för SAP-program.
* SAP-kommentar [2178632] mer information om alla övervakning mått som har rapporterats för SAP i Azure.
* SAP-kommentar [2191498] har Värdagenten för SAP-version som krävs för Linux i Azure.
* SAP-kommentar [2243692] har licensieringsinformation SAP på Linux i Azure.
* SAP-kommentar [1984787] har allmän information om SUSE Linux Enterprise Server 12.
* SAP-kommentar [1999351] innehåller ytterligare felsökningsinformation för Azure förbättrad övervakning av tillägget för SAP.
* SAP-kommentar [401162] innehåller information om hur du undviker ”adressen används redan” när du konfigurerar HANA System Replication.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure virtuella datorer, planering och implementering av SAP på Linux] [ planning-guide] guide.
* [Azure Virtual Machines-distribution för SAP på Linux] [ deployment-guide] (den här artikeln).
* [Azure Virtual Machines DBMS-distribution för SAP på Linux] [ dbms-guide] guide.
* [SUSE Linux Enterprise Server för SAP-program 12 SP3 bästa guider med metodtips][sles-for-sap-bp]
  * Hur du konfigurerar en SAP HANA SR prestanda optimerad infrastruktur (SLES för SAP-program 12 SP1). Guiden innehåller all informationen som krävs för att ställa in SAP HANA-Systemreplikering för lokal utveckling. Använd den här guiden som utgångspunkt.
  * Hur du konfigurerar en SAP HANA SR kostnaden optimerad infrastruktur (SLES för SAP-program 12 SP1)

## <a name="overview"></a>Översikt

SAP HANA är installerad på två virtuella datorer för att uppnå hög tillgänglighet. Data replikeras med hjälp av HANA System Replication.

![Översikt över hög tillgänglighet för SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA System Replication installationen använder en dedikerad virtuell värdnamn och virtuella IP-adresser. På Azure måste en belastningsutjämnare använda en virtuell IP-adress. I följande lista visas konfigurationen av belastningsutjämnaren:

* Konfiguration på klientsidan: IP-adress 10.0.0.13 för hn1-db
* Backend-konfiguration: Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i HANA System Replication
* Avsökningsporten: Port 62503
* Belastningsutjämningsregler: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Distribuera för Linux

Resurs-agenten för SAP HANA ingår i SUSE Linux Enterprise Server för SAP-program.
Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda en av de snabbstartsmallarna som finns på GitHub för att distribuera alla nödvändiga resurser. Mallen distribuerar de virtuella datorerna, belastningsutjämnaren, tillgänglighetsuppsättning och så vidare.
Om du vill distribuera mallen genom att följa dessa steg:

1. Öppna den [databasen mallen] [ template-multisid-db] eller [konvergerat mallen] [ template-converged] på Azure portal. 
    Databasen skapas belastningsutjämningsregler för endast en databas. Konvergerade mallen skapar även regler för belastningsutjämning för en ASCS/SCS och ÄNDARE (endast Linux)-instans. Om du planerar att installera ett SAP NetWeaver-baserat system och du vill installera ASCS/SCS-instans på samma datorer använder den [konvergerat mallen][template-converged].

1. Ange följande parametrar:
    - **SAP-System-ID**: Ange ID för SAP-system för SAP-system som du vill installera. ID: T används som ett prefix för de resurser som distribueras.
    - **Stack typ**: (Den här parametern är endast om du använder mallen konvergerade.) Välj typ av SAP NetWeaver-stack.
    - **OS-typ**: Välj en av Linux-distributioner. Det här exemplet väljer **SLES 12**.
    - **DB-typ**: Välj **HANA**.
    - **SAP-systemstorlek**: Ange hur många SAP som kommer att ge det nya systemet. Om du inte är säker på hur många SAP kräver att systemet genom att be din SAP-teknikpartner eller systemintegratör.
    - **Systemets tillgänglighet**: Välj **HA**.
    - **Administratörens användarnamn och lösenord för serveradministratören**: En ny användare skapas som kan användas för att logga in på datorn.
    - **Nytt eller befintligt undernät**: Anger om ett nytt virtuellt nätverk och undernät ska skapas eller ett befintligt undernät som används. Om du redan har ett virtuellt nätverk som är ansluten till ditt lokala nätverk, Välj **befintliga**.
    - **Undernät-ID**: Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T vanligtvis ser ut som **/subscriptions/\<prenumerations-ID > /resourceGroups/\<resursgruppens namn > /providers/Microsoft.Network/virtualNetworks/\<virtuellt nätverksnamn > /subnets/ \<undernätets namn >**.

### <a name="manual-deployment"></a>Manuell distribution

> [!IMPORTANT]
> Kontrollera att Operativsystemet som du väljer är SAP-certifierade för SAP HANA på de specifika VM-typer som du använder. Listan över SAP HANA-certifierade VM-typer och OS-versioner för de som kan sökas i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka på information om den typ av virtuell dator i listan för att få en fullständig lista över SAP HANA OS-versioner som stöds för den specifika VM-typ
>  

1. Skapa en resursgrupp.
1. Skapa ett virtuellt nätverk.
1. Skapa en tillgänglighetsuppsättning.
   - Ange den maximala uppdateringsdomänen.
1. Skapa en belastningsutjämnare (internt).
   - Välj det virtuella nätverket som skapades i steg 2.
1. Skapa virtuell dator 1.
   - Använd en SLES4SAP-avbildning i Azure-galleriet som stöds för SAP HANA på VM-typ du valt.
   - Välj tillgänglighetsuppsättning som skapades i steg 3.
1. Skapa virtuell dator 2.
   - Använd en SLES4SAP-avbildning i Azure-galleriet som stöds för SAP HANA på VM-typ du valt.
   - Välj tillgänglighetsuppsättning som skapades i steg 3. 
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
> SAP-kommentar [2382421](https://launchpad.support.sap.com/#/notes/2382421) innehåller för närvarande motstridig instruktionen som talar om att ställa in net.ipv4.tcp_timestamps på 1. Ange parametern för virtuella Azure-datorer är placerade bakom Azure Load balancer **net.ipv4.tcp_timestamps** till **0**. 

## <a name="create-a-pacemaker-cluster"></a>Skapa ett Pacemaker-kluster

Följ stegen i [konfigurerar Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) att skapa ett grundläggande Pacemaker kluster för den här HANA-servern. Du kan använda samma Pacemaker kluster för SAP HANA och SAP NetWeaver (A) SCS.

## <a name="install-sap-hana"></a>Installera SAP HANA

Stegen i det här avsnittet använder följande prefix:
- **[A]**: Steget gäller för alla noder.
- **[1]**: Steget gäller nod 1.
- **[2]**: Steget gäller nod 2 i Pacemaker-klustret.

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

1. **[A]**  Hög tillgänglighet för SAP HANA paketen:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Installera SAP HANA System Replication enligt kapitel 4 i den [SAP HANA SR prestanda optimerade scenariot guiden](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]**  Kör den **hdblcm** programmet från DVD-skivan för HANA. Ange följande värden i Kommandotolken:
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

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2.0-Systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller nod 1.
* **[2]**: Steget gäller nod 2 i Pacemaker-klustret.

1. **[1]**  Skapa klientdatabasen.

   Om du använder SAP HANA 2.0 eller MDC, skapa en klientdatabas för SAP NetWeaver-system. Ersätt **NW1** med SID för SAP-system.

   Logga in som \<hanasid > adm och kör följande kommando:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurera Systemreplikering på den första noden:

   Logga in som \<hanasid > adm och säkerhetskopiera databaserna:

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
    
   Registrera den andra noden för att starta systemreplikering. Logga in som \<hanasid > adm och kör följande kommando:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera SAP HANA 1.0-Systemreplikering

Stegen i det här avsnittet använder följande prefix:

* **[A]**: Steget gäller för alla noder.
* **[1]**: Steget gäller nod 1.
* **[2]**: Steget gäller nod 2 i Pacemaker-klustret.

1. **[1]**  Skapa nödvändiga användarna.

   Logga in som rot och kör följande kommando. Ersätt fetstil strängar (HANA System-ID **HN1** och antal instanser **03**) med värdena för SAP HANA-installation:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]**  Skapa nyckellagerposten.

   Logga in som rot och kör följande kommando för att skapa en ny keystore-post:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]**  Säkerhetskopiera databasen.

   Logga in som rot och säkerhetskopiera databaserna:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Om du använder en installation, bör du också säkerhetskopiera klientdatabasen:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Konfigurera Systemreplikering på den första noden.

   Logga in som \<hanasid > adm och skapa den primära platsen:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurera Systemreplikering på den sekundära noden.

   Logga in som \<hanasid > adm och registrera den sekundära platsen:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA-klusterresurser

Skapa först HANA-topologi. Kör följande kommandon på en av noderna i Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Skapa sedan HANA-resurser:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
  params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
  op monitor timeout=20s interval=10 depth=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som resurserna som körs.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testa konfiguration

Det här avsnittet beskrivs hur du kan testa din konfiguration. Varje test förutsätter att du är rot och SAP HANA-master körs på den **hn1-db-0** virtuell dator.

### <a name="test-the-migration"></a>Testa migreringen

Innan du startar testet ska du kontrollera att Pacemaker har inte några misslyckade åtgärder (via crm_mon - r), det finns inga oväntat Platsbegränsningar (till exempel rester av ett test för migrering) och att HANA är synkroniseringstillstånd, till exempel med SAPHanaSR showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Du kan migrera SAP HANA-huvudnoden genom att köra följande kommando:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Om du ställer in `AUTOMATED_REGISTER="false"`, denna serie kommandon bör migrera SAP HANA-huvudnod och den grupp som innehåller den virtuella IP-adressen till hn1-db-1.

När migreringen är klar crm_mon - r utdata ser ut så här

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

SAP HANA-resursen på hn1-db-0 inte går att starta som sekundär. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migreringen skapar Platsbegränsningar som måste tas bort igen:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Du måste också rensa upp tillståndet för den sekundära nod resursen:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Övervaka tillståndet för HANA-resursen som använder crm_mon - r. När HANA har startats på hn1-db-0, utdata bör se ut så här

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testa att hägna in Azure-agenten (inte uppstår)

Du kan testa installationen av agenten att hägna in Azure genom att inaktivera nätverksgränssnittet på hn1-db-0-noden:

<pre><code>sudo ifdown eth0
</code></pre>

Den virtuella datorn måste nu starta om eller stoppa beroende på din klusterkonfiguration.
Om du ställer in den `stonith-action` inställningen på av den virtuella datorn har stoppats och resurserna som migreras till den aktiva virtuella datorn.

När du startar den virtuella datorn igen går SAP HANA-resurs som inte går att starta som sekundär om du ställer in `AUTOMATED_REGISTER="false"`. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testa uppstår hägna in

Du kan testa installationen av uppstår genom att avsluta processen inquisitor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Klustret noden hn1-db-0 ska startas om. Pacemaker-tjänsten kan inte börja efteråt. Se till att starta den igen.

### <a name="test-a-manual-failover"></a>Testa en manuell redundans

Du kan testa en manuell redundans genom att stoppa den `pacemaker` på noden hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Efter redundansen kan starta du tjänsten igen. Om du ställer in `AUTOMATED_REGISTER="false"`, SAP HANA-resursen på hn1-db-0-nod som inte går att starta som sekundär. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-tester

> [!IMPORTANT]
> Kontrollera att Operativsystemet som du väljer är SAP-certifierade för SAP HANA på de specifika VM-typer som du använder. Listan över SAP HANA-certifierade VM-typer och OS-versioner för de som kan sökas i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka på information om den typ av virtuell dator i listan för att få en fullständig lista över SAP HANA OS-versioner som stöds för den specifika VM-typ

Kö alla testfall som listas i SAP HANA SR prestanda optimerade Scenario eller SAP HANA SR kostnaden optimerade scenariot handboken, beroende på ditt användningsområde. Du hittar guiderna på den [SLES for SAP bästa praxis sidan][sles-for-sap-bp].

Följande tester är en kopia av test-beskrivningar av de SAP HANA SR prestanda optimerade scenariot SUSE Linux Enterprise Server för SAP-program 12 SP1 guide. En uppdaterad version alltid Läs guiden för själva. Kontrollera alltid att HANA är synkroniserade innan du startar testet och Kontrollera också att Pacemaker konfigurationen är korrekt.

I följande test-beskrivningar förutsätter vi att PREFER_SITE_TAKEOVER = ”true” och AUTOMATED_REGISTER = ”false”.
OBS! Följande testerna är utformade för att köras i följd och beror på Avsluta tillståndet för dessa tester.

1. TEST 1: STOPPA PRIMÄRA DATABASEN PÅ NOD 1

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som \<hanasid > adm på noden hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker ska identifiera stoppad HANA-instans med redundans till den andra noden. När redundansväxlingen är klar, har HANA-instansen på noden hn1-db-0 stoppats eftersom Pacemaker inte registreras automatiskt nod som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-0 som sekundär och rensa åt resursen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 2: STOPPA PRIMÄRA DATABASEN PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som \<hanasid > adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker ska identifiera stoppad HANA-instans med redundans till den andra noden. När redundansväxlingen är klar, har HANA-instansen på noden hn1-db-1 stoppats eftersom Pacemaker inte registreras automatiskt nod som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-1 som sekundär och rensa åt resursen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 3: KRASCHA PRIMÄRA DATABASEN PÅ NODEN

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som \<hanasid > adm på noden hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker ska identifiera avslutats HANA-instans med redundans till den andra noden. När redundansväxlingen är klar, har HANA-instansen på noden hn1-db-0 stoppats eftersom Pacemaker inte registreras automatiskt nod som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-0 som sekundär och rensa åt resursen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 4: KRASCHA PRIMÄRA DATABASEN PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som \<hanasid > adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker ska identifiera avslutats HANA-instans med redundans till den andra noden. När redundansväxlingen är klar, har HANA-instansen på noden hn1-db-1 stoppats eftersom Pacemaker inte registreras automatiskt nod som HANA sekundär.

   Kör följande kommandon för att registrera noden hn1-db-1 som sekundär och rensa åt resursen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 5: KRASCHA PRIMÄR PLATS NODER (NOD 1)

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som rot på noden hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker ska identifiera avslutats klusternoden och fence noden. När noden är inhägnade utlöser Pacemaker ett övertagande av HANA-instans. Pacemaker startar inte automatiskt när noden inhägnade startas.

   Kör följande kommandon för att starta Pacemaker registrera Rensa uppstår meddelanden för noden hn1-db-0, noden hn1-db-0 som sekundär och rensa åt resursen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 6: KRASCHA SEKUNDÄR NOD (NOD 2)

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Kör följande kommandon som rot på noden hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker ska identifiera avslutats klusternoden och fence noden. När noden är inhägnade utlöser Pacemaker ett övertagande av HANA-instans. Pacemaker startar inte automatiskt när noden inhägnade startas.

   Kör följande kommandon för att starta Pacemaker registrera Rensa uppstår meddelanden för noden hn1-db-1, noden hn1-db-1 som sekundär och rensa åt resursen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 7: STOPPA SEKUNDÄRT DATABASEN PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som \<hanasid > adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker identifierar stoppad HANA-instans och markera resursen som misslyckad på noden hn1-db-1. Pacemaker bör automatiskt starta om den HANA-instansen. Kör följande kommando för att rensa felaktigt tillstånd.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 8: KRASCHER SOM SEKUNDÄRT DATABASEN PÅ NOD 2

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som \<hanasid > adm på noden hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker identifierar den avslutats HANA-instansen och markera resursen som misslyckad på noden hn1-db-1. Kör följande kommando för att rensa felaktigt tillstånd. Pacemaker bör sedan automatiskt HANA-instansen startas om.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 9: KRASCHA SEKUNDÄRA HANA-DATABAS FÖR SEKUNDÄR NOD (NOD 2) KÖRS

   Resurstillstånd innan du startar testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Kör följande kommandon som rot på noden hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker ska identifiera avslutats klusternoden och fence noden. Pacemaker startar inte automatiskt när noden inhägnade startas.

   Kör följande kommandon för att starta Pacemaker, rensa uppstår-meddelanden för noden hn1-db-1 och rensa åt resursen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resurstillstånd efter testet:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Azure virtuella datorer, planering och implementering av SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure (stora instanser) i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md)
