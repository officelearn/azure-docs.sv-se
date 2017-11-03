---
title: "Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VM) | Microsoft Docs"
description: "Skapa hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VM)."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.openlocfilehash: 951150e621d21037b0adde7287b9f985290d8d11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Lokalt, du kan använda antingen HANA System replikering eller använder delad lagring för att upprätta hög tillgänglighet för SAP HANA.
Vi stöds för närvarande bara ställa in HANA System replikering på Azure. SAP HANA replikering består av en nod och minst en slav nod. Ändringar av data på huvudnoden replikeras till de underordnade noderna synkront eller asynkront.

Den här artikeln beskriver hur du distribuerar virtuella datorer, konfigurera virtuella datorer, installera kluster framework, installera och konfigurera SAP HANA System replikering.
Installationen kommandon etc. instansnummer 03 och HANA System-ID HDB används i exempelkonfigurationer.

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

## <a name="deploying-linux"></a>Distribuera Linux

Resurs-agent för SAP HANA ingår i SUSE Linux Enterprise Server för SAP-program.
Azure Marketplace innehåller en bild för SUSE Linux Enterprise Server för SAP program 12 med BYOS (ta med din egen prenumeration) som du kan använda för att distribuera nya virtuella datorer.

### <a name="manual-deployment"></a>Manuell distribution

1. Skapa en resursgrupp
1. Skapa ett virtuellt nätverk
1. Skapa två Storage-konton
1. Skapa en Tillgänglighetsuppsättning  
   Ange högsta uppdateringsdomän
1. Skapa en belastningsutjämnare (internt)  
   Välj VNET i steget ovan
1. Skapa virtuell dator 1  
   https://Portal.Azure.com/#Create/SUSE-byos.SLES-for-SAP-byos12-SP1  
   SLES för SAP program 12 SP1 (BYOS)  
   Välj Lagringskonto 1  
   Välj Tillgänglighetsuppsättning  
1. Skapa virtuell dator 2  
   https://Portal.Azure.com/#Create/SUSE-byos.SLES-for-SAP-byos12-SP1  
   SLES för SAP program 12 SP1 (BYOS)  
   Välj Lagringskonto 2   
   Välj Tillgänglighetsuppsättning  
1. Lägg till Datadiskar
1. Konfigurera belastningsutjämnaren
    1. Skapa en klientdel IP-adresspool
        1. Öppna belastningsutjämnaren, Välj klientdelens IP-pool och klicka på Lägg till
        1. Ange namnet på den nya klientdelens IP-adresspoolen (till exempel hana-klientdel)
       1. Klicka på OK
        1. När du har skapat den nya klientdelens IP-poolen Skriv ned dess IP-adress
    1. Skapa en serverdelspool
        1. Öppna belastningsutjämnaren, Välj serverdelspooler och klicka på Lägg till
        1. Ange namnet på den nya serverdelspoolen (till exempel hana-serverdel)
        1. Klicka på Lägg till en virtuell dator
        1. Välj Tillgänglighetsuppsättningen som du skapade tidigare
        1. Välj de virtuella datorerna i SAP HANA-kluster
        1. Klicka på OK
    1. Skapa en hälsoavsökningen
       1. Öppna belastningsutjämnaren, Välj hälsoavsökningar och klicka på Lägg till
        1. Ange namnet på den nya hälsoavsökningen (till exempel hana-hp)
        1. Välj TCP som protokoll, port 625**03**, hålla intervallet 5 och tröskelvärde för ohälsosamt värde 2
        1. Klicka på OK
    1. Skapa regler för belastningsutjämning
        1. Öppna belastningsutjämnaren, Välj regler för belastningsutjämning och klicka på Lägg till
        1. Ange namnet på den nya regeln för belastningsutjämnare (till exempel hana-lb-3**03**15)
        1. Välj IP-adress för klientdel, serverdelspool och hälsa avsökning du skapade tidigare (till exempel hana-klientdel)
        1. Håll protokollet TCP, ange port 3**03**15
        1. Öka tidsgränsen för inaktivitet till 30 minuter
       1. **Se till att aktivera flytande IP**
        1. Klicka på OK
        1. Upprepa stegen ovan för port 3**03**17

### <a name="deploy-with-template"></a>Distribuera med mall
Du kan använda en av Snabbstart mallar på github för att distribuera alla nödvändiga resurser. Mallen distribuerar virtuella datorer, belastningsutjämnare, tillgänglighetsuppsättning osv. Följ dessa steg om du vill distribuera mallen:

1. Öppna den [databasen mallen] [ template-multisid-db] eller [konvergerat mallen] [ template-converged] på Azure Portal mallen databasen skapas endast regler för belastningsutjämning för en databas medan mallen konvergerade skapar också regler för belastningsutjämning för en ASCS/SCS ÄNDARE (endast Linux)-instans. Om du planerar att installera ett SAP NetWeaver baserat system och du även vill installera ASCS/SCS-instans på samma datorer använder den [konvergerat mallen][template-converged].
1. Ange följande parametrar
    1. SAP System-Id  
       Ange SAP-systemet Id för SAP-system som du vill installera. Id som ska användas som ett prefix för de resurser som har distribuerats.
    1. Stacken typ (gäller endast om du använder mallen konvergerade)  
       Välj typ för SAP NetWeaver stack
    1. OS-typen  
       Välj en av Linux-distributioner. Det här exemplet väljer du SLES 12 BYOS
    1. DB-typ  
       Välj HANA
    1. Storlek för SAP-System  
       Mängden SAP ger det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegreraren
    1. Systemets tillgänglighet  
       Välj hög tillgänglighet
    1. Användarnamn och lösenord för Admin administratör  
       En ny användare skapas som kan användas för att logga in på datorn.
    1. Ny eller befintlig undernät  
       Avgör om ett nytt virtuellt nätverk och undernät som ska skapas eller ett befintligt undernät som ska användas. Om du redan har ett virtuellt nätverk som är anslutna till ditt lokala nätverk väljer du befintliga.
    1. Undernät-Id  
    ID för det undernät som de virtuella datorerna ska anslutas till. Välj undernätet i ditt VPN eller Expressroute virtuella nätverk att ansluta den virtuella datorn till ditt lokala nätverk. ID som ser oftast ut så /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>Konfigurera Linux hög tillgänglighet

Följande objekt med antingen [A] - prefixet gäller för alla noder är [1] – gäller endast för nod 1 eller [2] - gäller endast för nod 2.

1. [A] SLES SAP BYOS endast - registrera SLES för att kunna använda databaser
1. [A] SLES för SAP BYOS endast - Lägg till offentliga moln modul
1. [A] uppdatera SLES
    ```bash
    sudo zypper update

    ```

1. [1] aktivera ssh-åtkomst
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] aktivera ssh-åtkomst
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] aktivera ssh-åtkomst
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] installera tillägg för hög tillgänglighet
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] disklayouten för installationsprogrammet för
    1. LVM  
    Generellt rekommenderar vi för att använda LVM för volymer som lagrar data och loggfiler. Exemplet nedan förutsätter att de virtuella datorerna har fyra datadiskar som är anslutna som ska användas för att skapa två volymer.
        * Skapa fysiska volymer för alla diskar som du vill använda.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Skapa en volym grupp för datafiler, en volym grupp för loggfilerna och en för den delade katalogen för SAP HANA
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Skapa logiska volymer
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Skapa mount-kataloger och kopiera alla logiska volymer UUID
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Skapa fstab poster för de tre logiska volymerna
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Infoga raden till /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Montera nya volymer
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Vanlig diskar  
       För små eller demonstrera system, du kan placera HANA data och loggfilen filer på en disk. Följande kommandon skapar en partition på /dev/sdc och formatera den med xfs.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>Anteckna id för /dev/sdc1
    sudo/sbin/blkid sudo vi/etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] konfigurera namnmatchning för alla värdar  
    Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon
    ```bash
    sudo vi /etc/hosts
    ```
    Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] installera kluster
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] Lägg till nod i klustret
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] ändra hacluster lösenord till samma lösenord
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] konfigurera corosync om du vill använda andra transport och lägga till nodelist. Klustret fungerar inte på annat sätt.
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Lägg till följande fetstil innehåll i filen.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Starta om tjänsten corosync

    ```bash
    sudo service corosync restart
    
    ```

1. [A] Installationspaketen HANA hög tillgänglighet  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Installera SAP HANA

Följ kapitel 4 i den [SAP HANA SR prestanda optimerade scenariot guiden] [ suse-hana-ha-guide] att installera SAP HANA System replikering.

1. [A] kör hdblcm från DVD: n HANA
    * Välj installation -> 1
    * Välj ytterligare komponenter för installation -> 1
    * Ange installationssökväg [/ hana/delade]: RETUR ->
    * Ange lokala värdnamn [.]: -> RETUR
    * Vill du lägga till ytterligare värdar systemet? (j/n) [n]: RETUR ->
    * Ange SAP HANA System-ID:<SID of HANA e.g. HDB>
    * Ange instansnummer [00]:   
  HANA instansnummer. Använd 03 om du har använt Azure-mall eller följt exemplet ovan
    * Välj läge för databasen / ange Index [1]: -> RETUR
    * Välj systemanvändning / ange Index [4]:  
  Välj system användning
    * Ange platsen för datavolymer [/ hana/data/HDB]: RETUR ->
    * Ange platsen för Loggvolymer [/ hana/log/HDB]: RETUR ->
    * Begränsa maximal minnesallokering? [n]: RETUR ->
    * Ange värdnamnet för certifikat för värden ”...” [...]: RETUR ->
    * Ange SAP värden Agent användare (sapadm) lösenord:
    * Bekräfta SAP värden Agent användare (sapadm) lösenord:
    * Ange systemadministratören (hdbadm) lösenord:
    * Bekräfta systemadministratören (hdbadm) lösenord:
    * Ange System administratör arbetskatalog [/ usr/sap/HDB/hem]: RETUR ->
    * Ange System inloggningsgränssnitt för administratör [/ bin/del]: RETUR ->
    * Ange systemadministratören användar-ID [1001]: -> RETUR
    * Ange ID för användargrupp (sapsys) [79]: RETUR ->
    * Ange lösenord för användare (SYSTEM) av databasen:
    * Bekräfta databas användarlösenord (SYSTEM):
    * Starta om systemet efter omstart av datorn? [n]: RETUR ->
    * Vill du fortsätta? (j/n):  
  Kontrollera sammanfattningen och ange y om du vill fortsätta
1. [A] uppgradera SAP Värdagenten  
  Hämta senaste SAP Värdagenten arkivet från den [SAP Softwarecenter] [ sap-swcenter] och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar till den fil som du hämtat.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] Skapa HANA replikering (som rot)  
    Kör följande kommando. Se till att ersätta fetstil strängar (HANA System-ID HDB och instans antalet 03) med värden för SAP HANA-installationen.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] Skapa keystore-post (som rot)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] backup database (som rot)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] växla till sapsid användare (till exempel hdbadm) och skapa den primära platsen.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] växla till sapsid användare (till exempel hdbadm) och skapa en sekundär plats.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Konfigurera klustret Framework

Ändra standardinställningarna

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Vi kan nu läsa in filen i klustret
sudo crm konfigurera belastningen update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Skapa STONITH enhet

STONITH enheten använder ett huvudnamn för tjänsten för att godkänna mot Microsoft Azure. Följ dessa steg för att skapa ett huvudnamn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaperna och Skriv ned Directory-Id. Det här är den **klient-id**.
1. Klicka på appen registreringar
1. Klicka på Lägg till
1. Ange ett namn, Välj typ av program ”Web app/API”, ange en inloggnings-URL (till exempel http://localhost) och klicka på Skapa
1. URL för inloggning används inte och kan vara en giltig URL
1. Välj den nya appen och klicka på nycklar på fliken Inställningar
1. Ange en beskrivning för en ny nyckel, Välj ”upphör aldrig att gälla” och klicka på Spara
1. Anteckna värdet. Den används som den **lösenord** för tjänstens huvudnamn
1. Skriv ned det program-Id. Den används som användarnamnet (**inloggnings-id** i stegen nedan) för tjänstens huvudnamn

Tjänstens huvudnamn har inte behörighet att komma åt Azure-resurser som standard. Du behöver ge behörighet att starta och stoppa tjänstens huvudnamn (frigöra) alla virtuella datorer i klustret.

1. Gå till https://portal.azure.com
1. Öppna bladet alla resurser
1. Välj den virtuella datorn
1. Klicka på åtkomstkontroll (IAM)
1. Klicka på Lägg till
1. Välj rollen ägare
1. Ange namnet på programmet som du skapade ovan
1. Klicka på OK

När du har redigerat behörigheter för de virtuella datorerna kan du konfigurera STONITH-enheter i klustret.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Vi kan nu läsa in filen i klustret
sudo crm konfigurera belastningen update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Skapa SAP HANA-resurser

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Vi kan nu läsa in filen i klustret
sudo crm konfigurera belastningen update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Vi kan nu läsa in filen i klustret
sudo crm konfigurera belastningen update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Inställningar för kluster
Följande avsnittet beskriver hur du kan testa din konfiguration. Varje test förutsätter att du är rot och SAP HANA master körs på den virtuella datorn saphanavm1.

#### <a name="fencing-test"></a>Avgränsningar Test

Du kan testa installationen av agenten avgränsningar genom att inaktivera nätverksgränssnittet på noden saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

Den virtuella datorn bör nu hämta startas om eller stoppas beroende på klusterkonfigurationen.
Om du ställer in stonith-åtgärd till av den virtuella datorn kommer att stoppas och resurserna som migreras till den virtuella datorn som körs.

När du startar den virtuella datorn igen SAP HANA-resurs inte längre att fungera som sekundär om du ställer in AUTOMATED_REGISTER = ”false”. I det här fallet måste du konfigurera HANA-instans som sekundär genom att köra följande kommando:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Testa en manuell växling

Du kan testa en manuell växling genom att stoppa tjänsten pacemaker på noden saphanavm1.
<pre><code>
service pacemaker stop
</code></pre>

Efter växling vid fel, kan du starta tjänsten igen. SAP HANA-resursen på saphanavm1 inte längre att fungera som sekundär om du ställer in AUTOMATED_REGISTER = ”false”. I det här fallet måste du konfigurera HANA-instans som sekundär genom att köra följande kommando:

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Testa en migrering

Du kan migrera SAP HANA huvudnoden genom att köra följande kommando
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Detta bör migrera SAP HANA-huvudnod och den grupp som innehåller saphanavm2 virtuella IP-adressen.
SAP HANA-resursen på saphanavm1 inte längre att fungera som sekundär om du ställer in AUTOMATED_REGISTER = ”false”. I det här fallet måste du konfigurera HANA-instans som sekundär genom att köra följande kommando:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

Migreringen skapar plats begränsningar som måste tas bort igen.

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

Du måste också rensa det sekundära noden tillståndet

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Nästa steg
* [Azure virtuella datorer planering och implementering för SAP][planning-guide]
* [Distribution av Azure virtuella datorer för SAP][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md). 
