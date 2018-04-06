---
title: Konfigurera SAP HANA System replikering på virtuella Azure-datorer (VM) | Microsoft Docs
description: Skapa hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VM).
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: e3fb06309dabd7f66d5873e4c5faa48b468854f6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VM)

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

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Lokalt, du kan använda antingen HANA System replikering eller använder delad lagring för att upprätta hög tillgänglighet för SAP HANA.
På Azure VM HANA System-replikering i Azure är den enda stöd för funktionen för hög tillgänglighet hittills. SAP HANA replikering består av en primär nod och minst en sekundär nod. Ändringar av data på den primära noden replikeras till den sekundära noden synkront eller asynkront.

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurera virtuella datorer, installera kluster framework, installera och konfigurera SAP HANA System replikering.
Installationen kommandon etc. instansnummer 03 och HANA System-ID HN1 används i exempelkonfigurationer.

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
* [Scenario för SAP HANA SR prestanda optimerade] [ suse-hana-ha-guide] guiden innehåller all nödvändig information för att ställa in SAP HANA System replikering på lokalt. Använd den här guiden som utgångspunkt.

## <a name="overview"></a>Översikt

För att uppnå hög tillgänglighet, installeras SAP HANA på två virtuella datorer. Data replikeras med HANA System replikering.

![Hög tillgänglighet för SAP HANA-översikt](./media/sap-hana-high-availability/ha-suse-hana.png)

Installationsprogrammet för SAP HANA SR använder en dedikerad virtuell värdnamn och virtuella IP-adresser. På Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visar konfigurationen av belastningsutjämnaren.

* Konfiguration för klientdel
  * IP-adress 10.0.0.13 för hn1-db
* Backend-konfiguration
  * Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i HANA System replikering
* Avsökningsport
  * Port 62503
* Regler för belastningsutjämning
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Distribuera Linux

Resurs-agent för SAP HANA ingår i SUSE Linux Enterprise Server för SAP-program.
Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP program 12 som du kan använda för att distribuera nya virtuella datorer.

### <a name="deploy-with-template"></a>Distribuera med mall
Du kan använda en av quickstart mallar på github för att distribuera alla nödvändiga resurser. Mallen distribuerar virtuella datorer, belastningsutjämnare, tillgänglighetsuppsättning osv. Följ dessa steg om du vill distribuera mallen:

1. Öppna den [databasen mallen] [ template-multisid-db] eller [konvergerat mallen] [ template-converged] på Azure-portalen. 
   Mallen databasen skapas endast regler för belastningsutjämning för en databas medan mallen konvergerade skapar också regler för belastningsutjämning för en ASCS/SCS ÄNDARE (endast Linux)-instans. Om du planerar att installera ett SAP NetWeaver baserat system och du även vill installera ASCS/SCS-instans på samma datorer använder den [konvergerat mallen][template-converged].
1. Ange följande parametrar
    1. SAP System-ID  
       Ange ID för SAP-system för SAP-system som du vill installera. ID som ska användas som ett prefix för de resurser som har distribuerats.
    1. Stacken typ (gäller endast om du använder mallen konvergerade)   
       Välj typ för SAP NetWeaver stack
    1. OS-typen  
       Välj en av Linux-distributioner. Det här exemplet väljer du SLES 12
    1. DB-typ  
       Välj HANA
    1. Storlek för SAP-System  
       Storleken på det nya systemet ska ge SAP. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegreraren
    1. Systemets tillgänglighet  
       Välj hög tillgänglighet
    1. Användarnamn och lösenord för Admin administratör  
       En ny användare skapas som kan användas för att logga in på datorn.
    1. Ny eller befintlig undernät  
       Avgör om ett nytt virtuellt nätverk och undernät som ska skapas eller ett befintligt undernät som ska användas. Om du redan har ett virtuellt nätverk som är anslutna till ditt lokala nätverk väljer du befintliga.
    1. Undernät-ID  
    ID för det undernät som de virtuella datorerna ska anslutas till. Om du vill ansluta den virtuella datorn till ditt lokala nätverk markerar du undernätet i det virtuella nätverket för VPN eller Expressroute. ID som ser oftast ut så /subscriptions/`<subscription ID`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

### <a name="manual-deployment"></a>Manuell distribution

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa en Tillgänglighetsuppsättning  
   Ange högsta uppdateringsdomän
1. Skapa en belastningsutjämnare (internt)  
   Välj VNET som skapats i andra
1. Skapa virtuell dator 1  
   Använd minst SLES4SAP 12 SP1, i det här exemplet kommer vi att använda bilden SLES4SAP 12 SP2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES för SAP 12 SP2 (Premium)  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Skapa virtuell dator 2  
   Använd minst SLES4SAP 12 SP1, i det här exemplet kommer vi att använda bilden SLES4SAP 12 SP1 BYOS https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES för SAP 12 SP2 (Premium)  
   Välj Tillgänglighetsuppsättning skapade tidigare  
1. Lägg till Datadiskar
1. Konfigurera belastningsutjämnaren
    1. Skapa en klientdel IP-adresspool
        1. Öppna belastningsutjämnaren, Välj klientdelens IP-pool och klicka på Lägg till
        1. Ange namnet på den nya klientdelens IP-adresspoolen (till exempel hana-klientdel)
        1. Ange tilldelningen som statisk och ange IP-adress (till exempel **10.0.0.13**)
        1. Klicka på OK
        1. När du har skapat den nya klientdelens IP-poolen Skriv ned dess IP-adress
    1. Skapa en serverdelspool
        1. Öppna belastningsutjämnaren, Välj serverdelspooler och klicka på Lägg till
        1. Ange namnet på den nya serverdelspoolen (till exempel hana-serverdel)
        1. Klicka på Lägg till en virtuell dator
        1. Välj Tillgänglighetsuppsättningen som du skapade tidigare
        1. Välj de virtuella datorerna i SAP HANA-kluster
        1. Klicka på OK
    1. Skapa en hälsoavsökning
        1. Öppna belastningsutjämnaren, Välj hälsoavsökningar och klicka på Lägg till
        1. Ange namnet på den nya hälsoavsökningen (till exempel hana-hp)
        1. Välj TCP som protokoll, port 625**03**, hålla intervallet 5 och tröskelvärde för ohälsosamt värde 2
        1. Klicka på OK
    1. SAP HANA 1.0: Skapa regler för belastningsutjämning
        1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
        1. Ange namnet på den nya regeln för belastningsutjämnare (till exempel hana-lb-3**03**15)
        1. Välj klientdelens IP-adress, serverdelspool och hälsoavsökningen som du skapade tidigare (till exempel hana-klientdel)
        1. Håll protokollet TCP, ange port 3**03**15
        1. Öka tidsgränsen för inaktivitet till 30 minuter
        1. **Se till att aktivera flytande IP**
        1. Klicka på OK
        1. Upprepa stegen ovan för port 3**03**17
    1. SAP HANA 2.0: Skapa regler för systemdatabasen för belastningsutjämning
        1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
        1. Ange namnet på den nya regeln för belastningsutjämnare (till exempel hana-lb-3**03**13)
        1. Välj klientdelens IP-adress, serverdelspool och hälsoavsökningen som du skapade tidigare (till exempel hana-klientdel)
        1. Håll protokollet TCP, ange port 3**03**13
        1. Öka tidsgränsen för inaktivitet till 30 minuter
        1. **Se till att aktivera flytande IP**
        1. Klicka på OK
        1. Upprepa stegen ovan för port 3**03**14
    1. SAP HANA 2.0: Skapa belastningsutjämningsregler för första klient-databas
        1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
        1. Ange namnet på den nya regeln för belastningsutjämnare (till exempel hana-lb-3**03**40)
        1. Välj klientdelens IP-adress, serverdelspool och hälsoavsökningen som du skapade tidigare (till exempel hana-klientdel)
        1. Håll protokollet TCP, ange port 3**03**40
        1. Öka tidsgränsen för inaktivitet till 30 minuter
        1. **Se till att aktivera flytande IP**
        1. Klicka på OK
        1. Upprepa stegen ovan för port 3**03**41 och 3**03**42

Mer information om portarna som krävs för SAP HANA Läs kapitlet [anslutningar till klient databaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) av den [SAP HANA-klient databaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) guide eller [SAP Obs 2388694] [2388694].


## <a name="create-pacemaker-cluster"></a>Skapa Pacemaker kluster

Följ stegen i [konfigurerar Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) att skapa ett grundläggande Pacemaker kluster för den här HANA-servern. Du kan också använda samma Pacemaker kluster för SAP HANA och SAP NetWeaver (A) SCS.

## <a name="installing-sap-hana"></a>Installera SAP HANA

Följande objekt är prefixet antingen **[A]** - gäller för alla noder **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2 i Pacemaker klustret.

1. **[A]**  Disklayouten för installationen
    1. LVM  
       
       Normalt bör du använda LVM för volymer som lagrar data och loggfiler. Följande exempel förutsätter att de virtuella datorerna har fyra datadiskar som är anslutna som ska användas för att skapa två volymer.

       Visa en lista över alla tillgängliga diskar
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Exempel på utdata
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Skapa fysiska volymer för alla diskar som du vill använda.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Skapa en volym grupp för datafiler, en volym grupp för loggfilerna och en för den delade katalogen för SAP HANA

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       Skapa logiska volymer

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Skapa mount-kataloger och kopiera alla logiska volymer UUID
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Skapa fstab poster för de tre logiska volymerna
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Infoga raden till /etc/fstab
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Montera nya volymer
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Vanlig diskar  
       Du kan placera HANA data och loggfilen filer på en disk för demo-system. Följande kommandon skapar en partition på /dev/disk/azure/scsi1/lun0 och formatera den med xfs.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Infoga raden till /etc/fstab
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Skapa målkatalogen och montera disken.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]**  Konfigurera namnmatchning för alla värdar  
    Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon
    ```bash
    sudo vi /etc/hosts
    ```
    Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  Installera HANA HA paket  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

Installera SAP HANA System Replication enligt kapitel 4 i guiden för SAP HANA SR prestanda optimerade scenariot på https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/

1. **[A]**  Kör hdblcm från DVD: n HANA
    * Välj installation -> 1
    * Välj ytterligare komponenter för installation -> 1
    * Ange installationssökväg [/ hana/delade]: RETUR ->
    * Ange lokala värdnamn [.]: -> RETUR
    * Vill du lägga till ytterligare värdar systemet? (j/n) [n]: RETUR ->
    * Ange SAP HANA System-ID: <SID of HANA e.g. HN1>
    * Ange instansnummer [00]:   
  HANA instansnummer. Använd 03 om du har använt Azure-mall eller följt manuell distribution
    * Välj läge för databasen / ange Index [1]: -> RETUR
    * Välj systemanvändning / ange Index [4]:  
  Välj system användning
    * Ange platsen för datavolymer [/ hana/data/HN1]: RETUR ->
    * Ange platsen för Loggvolymer [/ hana/log/HN1]: RETUR ->
    * Begränsa maximal minnesallokering? [n]: RETUR ->
    * Ange värdnamnet för certifikat för värden ”...” [...]: RETUR ->
    * Ange SAP värden Agent användare (sapadm) lösenord:
    * Bekräfta SAP värden Agent användare (sapadm) lösenord:
    * Ange systemadministratören (hdbadm) lösenord:
    * Bekräfta systemadministratören (hdbadm) lösenord:
    * Ange System administratör arbetskatalog [/ usr/sap/HN1/hem]: RETUR ->
    * Ange System inloggningsgränssnitt för administratör [/ bin/del]: RETUR ->
    * Ange systemadministratören användar-ID [1001]: -> RETUR
    * Ange ID för användargrupp (sapsys) [79]: RETUR ->
    * Ange lösenord för användare (SYSTEM) av databasen:
    * Bekräfta databas användarlösenord (SYSTEM):
    * Starta om systemet efter omstart av datorn? [n]: RETUR ->
    * Vill du fortsätta? (j/n):   
  Kontrollera sammanfattningen och ange y om du vill fortsätta

1. **[A]**  Uppgradera SAP Värdagenten  
  Hämta senaste SAP Värdagenten arkivet från den [SAP Softwarecenter] [ sap-swcenter] och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar till den fil som du hämtat.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera replikering för SAP HANA 2.0 System

Följande objekt är prefixet antingen **[A]** - gäller för alla noder **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2 i Pacemaker klustret.

1. **[1]**  Skapar klient-databas

   Om du använder SAP HANA 2.0 eller MDC, skapa en klient-databas för SAP NetWeaver systemet. Ersätt NW1 med SID för SAP-system.

   Logga in som `<hanasid`> adm och kör följande kommando

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurera systemet replikering på den första noden
   
   Logga in som `<hanasid`> adm och säkerhetskopiera databaserna

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopiera PKI systemfiler till sekundär

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Skapa den primära platsen.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurera systemet replikering på den andra noden
    
    Registrera den andra noden för att starta replikering för systemet. Logga in som `<hanasid`> adm och kör följande kommando

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurera replikering för SAP HANA 1.0 System

1. **[1]**  Skapa nödvändiga användare

    Logga in som rot och kör följande kommando. Se till att ersätta fetstil strängar (HANA System-ID HN1 och instans antalet 03) med värden för SAP HANA-installationen.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]**  Skapa keystore-post
   
    Logga in som rot och kör följande kommando för att skapa en ny keystore-post.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]**  Backup database

   Logga in som rot och säkerhetskopiera databaserna

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Om du använder en installation med flera innehavare kan också säkerhetskopiera databasen för klient

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Konfigurera systemet replikering på den första noden
    
    Logga in som `<hanasid`> adm och skapa den primära platsen.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]**  Konfigurera systemet replikering på den sekundära noden.

    Logga in som `<hanasid`> adm och registrera den sekundära platsen.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA klusterresurser

   Först skapa HANA-topologi. Kör följande kommandon på en av klusternoderna Pacemaker.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Skapa sedan HANA-resurser.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
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
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 2000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Kontrollera att klustret status är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som använder resurserna.

   <pre><code>
   sudo crm_mon -r
   
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

### <a name="test-cluster-setup"></a>Inställningar för kluster
Det här avsnittet beskrivs hur du kan testa din konfiguration. Varje test förutsätter att du är rot och SAP HANA master körs på virtuella hn1-db-0.

#### <a name="fencing-test"></a>Avgränsningar Test

Du kan testa installationen av agenten avgränsningar genom att inaktivera nätverksgränssnittet på noden hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

Den virtuella datorn bör nu hämta startas om eller stoppas beroende på klusterkonfigurationen.
Om du ställer in stonith-åtgärd till av den virtuella datorn kommer att stoppas och resurserna som migreras till den virtuella datorn som körs.

När du startar den virtuella datorn igen SAP HANA-resurs som inte går att starta som sekundära om du ställer in AUTOMATED_REGISTER = ”false”. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Testa en manuell växling

Du kan testa en manuell växling genom att stoppa tjänsten pacemaker på noden hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

Efter växling vid fel, kan du starta tjänsten igen. Om du ställer in AUTOMATED_REGISTER = ”false”, SAP HANA-resursen på hn1-db-0 går inte att starta som sekundära. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Testa en migrering

Du kan migrera SAP HANA huvudnoden genom att köra följande kommando
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Om du ställer in AUTOMATED_REGISTER = ”false” den här kommandosekvens bör migrera SAP HANA-huvudnod och den grupp som innehåller den virtuella IP-adressen till hn1-db-1.
Det inte går att starta som sekundära SAP HANA-resursen på hn1-db-0. I detta fall konfigurera HANA-instans som sekundär genom att köra det här kommandot:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

Migreringen skapar Platsbegränsningar som måste tas bort igen.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

Du måste också rensa det sekundära noden tillståndet

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Nästa steg
* [Azure virtuella datorer planering och implementering för SAP][planning-guide]
* [Distribution av Azure virtuella datorer för SAP][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md). 
