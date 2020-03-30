---
title: SAP HANA-utskalning av HSR-Pacemaker med SLES på Azure-virtuella datorer felsökning| Microsoft-dokument
description: Guide för att kontrollera och felsöka en komplex SAP HANA-utskalningskonfiguration med hög tillgänglighet baserat på SAP HANA System Replication (HSR) och Pacemaker på SLES 12 SP3 som körs på virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: e93b3412785817050ac53030be9ff2172a678c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617127"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verifiera och felsöka SAP HANA-utskalning med hög tillgänglighet på SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Den här artikeln hjälper dig att kontrollera Pacemaker-klusterkonfigurationen för SAP HANA-utskalning som körs på virtuella Azure-datorer (VIRTUELLA datorer). Klusterkonfigurationen uppnåddes i kombination med SAP HANA System Replication (HSR) och SUSE RPM-paketet SAPHanaSR-ScaleOut. Alla tester gjordes endast på SUSE SLES 12 SP3. Artikelns avsnitt täcker olika områden och innehåller exempelkommandon och utdrag från config-filer. Vi rekommenderar dessa exempel som en metod för att verifiera och kontrollera hela klusterinställningarna.



## <a name="important-notes"></a>Viktiga meddelanden

Alla tester för SAP HANA-utskalning i kombination med SAP HANA System Replication och Pacemaker gjordes endast med SAP HANA 2.0. Operativsystemets version var SUSE Linux Enterprise Server 12 SP3 för SAP-program. Det senaste RPM-paketet, SAPHanaSR-ScaleOut från SUSE, användes för att konfigurera Pacemaker-klustret.
SUSE publicerade en [detaljerad beskrivning av den här prestandaoptimerade installationen][sles-hana-scale-out-ha-paper].

För typer av virtuella datorer som stöds för SAP HANA-utskalning läser du [DEN SAP HANA-certifierade IaaS-katalogen][sap-hana-iaas-list].

Det uppstod ett tekniskt problem med SAP HANA-utskalning i kombination med flera undernät och virtuella och virtuella nätverk och inrättandet av HSR. Det är obligatoriskt att använda de senaste SAP HANA 2.0-korrigeringsfilerna där problemet har åtgärdats. Följande SAP HANA-versioner stöds: 

* rev2.00.024.04 eller högre 
* rev2.00.032 eller högre

Om du behöver stöd från SUSE följer du den här [guiden][suse-pacemaker-support-log-files]. Samla in all information om HA-klustret (HIGH-Availability) enligt beskrivningen i artikeln. SUSE-stöd behöver denna information för vidare analys.

Under interna tester blev klusterkonfigurationen förvirrad av en normal graciös VM-avstängning via Azure-portalen. Därför rekommenderar vi att du testar en kluster redundans med andra metoder. Använd metoder som att tvinga en kernel panik, **msl** eller stänga av nätverk eller migrera msl-resursen. Se information i följande avsnitt. Antagandet är att en standard avstängning sker med avsikt. Det bästa exemplet på en avsiktlig avstängning är för underhåll. Se information i [Planerat underhåll](#planned-maintenance).

Under interna tester blev klusterkonfigurationen också förvirrad efter ett manuellt SAP HANA-övertagande medan klustret var i underhållsläge. Vi rekommenderar att du växlar tillbaka den manuellt innan du avslutar underhållsläget för klustret. Ett annat alternativ är att utlösa en redundans innan du sätter klustret i underhållsläge. Mer information finns i [Planerat underhåll](#planned-maintenance). Dokumentationen från SUSE beskriver hur du kan återställa klustret på det här sättet med hjälp av **crm-kommandot.** Men det tillvägagångssätt som tidigare nämnts var robust under interna tester och visade aldrig några oväntade biverkningar.

När du använder kommandot **crm migrate,** se till att rensa klusterkonfigurationen. Den lägger till platsbegränsningar som du kanske inte känner till. Dessa begränsningar påverkar klusterbeteendet. Se mer information i [Planerat underhåll](#planned-maintenance).



## <a name="test-system-description"></a>Beskrivning av testsystemet

 För SAP HANA-utskalning HA-verifiering och certifiering användes en installation. Det bestod av två system med tre SAP HANA-noder vardera: en master och två arbetare. I följande tabell visas VM-namn och interna IP-adresser. Alla verifieringsprover som följer gjordes på dessa virtuella datorer. Genom att använda dessa VM-namn och IP-adresser i kommandoexemplen kan du bättre förstå kommandona och deras utdata:


| Typ av nod | VM-namn | IP-adress |
| --- | --- | --- |
| Huvudnod på plats 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Arbetsnod 1 på plats 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Arbetsnod 2 på plats 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Huvudnod på plats 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Arbetsnod 1 på plats 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Arbetsnod 2 på plats 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Majoritetstillverkare nod | hso-hana-dm | 10.0.0.13 |
| SBD-enhetsserver | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS-server 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS-server 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Flera undernät och virtuella nätverkskort

Efter SAP HANA-nätverksrekommendationer skapades tre undernät inom ett virtuellt Azure-nätverk. SAP HANA-utskalning på Azure måste installeras i icke-delat läge. Det innebär att varje nod använder lokala diskvolymer för **/hana/data** och **/hana/log**. Eftersom noderna endast använder lokala diskvolymer är det inte nödvändigt att definiera ett separat undernät för lagring:

- 10.0.2.0/24 för SAP HANA internode kommunikation
- 10.0.1.0/24 för SAP HANA System Replication (HSR)
- 10.0.0.0/24 för allt annat

Information om SAP HANA-konfiguration som är relaterad till att använda flera nätverk finns [i SAP HANA global.ini](#sap-hana-globalini).

Varje virtuell dator i klustret har tre virtuella NCC som motsvarar antalet undernät. [Hur man skapar en virtuell Linux-dator i Azure med flera nätverkskort][azure-linux-multiple-nics] beskriver ett potentiellt routningsproblem på Azure när du distribuerar en Virtuell Linux-dator. Den här specifika routningsartikeln gäller endast för användning av flera virtuella NCC.This specific routing article applies only for use of multiple vNICs. Problemet löses av SUSE per standard i SLES 12 SP3. Mer information finns i [Multi-NIC med cloud-netconfig i EC2 och Azure][suse-cloud-netconfig].


Om du vill kontrollera att SAP HANA är korrekt konfigurerat för att använda flera nätverk kör du följande kommandon. Kontrollera först på OS-nivån att alla tre interna IP-adresser för alla tre undernäten är aktiva. Om du har definierat undernäten med olika IP-adressintervall måste du anpassa kommandona:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Följande exempelutdata kommer från den andra arbetarnoden på plats 2. Du kan se tre olika interna IP-adresser från eth0, eth1 och eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Kontrollera sedan SAP HANA-portarna för namnservern och HSR. SAP HANA bör lyssna på motsvarande undernät. Beroende på SAP HANA-instansnumret måste du anpassa kommandona. För testsystemet var instansnumret **00**. Det finns olika sätt att ta reda på vilka portar som används. 

Följande SQL-uttryck returnerar instans-ID, instansnummer och annan information:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Om du vill hitta rätt portnummer kan du till exempel titta i HANA Studio under **Konfiguration** eller via ett SQL-uttryck:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Om du vill hitta alla portar som används i SAP-programvarustacken, inklusive SAP HANA, söker du i [TCP/IP-portar för alla SAP-produkter.][sap-list-port-numbers]

Med tanke på instansnummer **00** i SAP HANA 2.0-testsystemet är portnumret för namnservern **30001**. Portnumret för HSR-metadatakommunikation är **40002**. Ett alternativ är att logga in på en arbetsnod och sedan kontrollera huvudnodtjänsterna. För den här artikeln kontrollerade vi arbetarnod 2 på plats 2 och försökte ansluta till huvudnoden på plats 2.

Kontrollera namnserverporten:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

För att bevisa att internodekommunikationen använder undernät **10.0.2.0/24**bör resultatet se ut så här med följande exempelutdata.
Endast anslutningen via undernät **10.0.2.0/24** bör lyckas:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Kontrollera nu om HSR-port **40002:**

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

För att bevisa att HSR-kommunikationen använder undernät **10.0.1.0/24**bör resultatet se ut som följande exempelutdata.
Endast anslutningen via undernät **10.0.1.0/24** bör lyckas:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


**Corosync-config-filen** måste vara korrekt på alla noder i klustret, inklusive majoritetsskaparnoden. Om klusterkopplingen för en nod inte fungerar som förväntat skapar eller **kopierar /etc/corosync/corosync.conf** manuellt på alla noder och startar om tjänsten. 

Innehållet i **corosync.conf** från testsystemet är ett exempel.

Det första avsnittet är **totem**, enligt beskrivningen i [Klusterinstallation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), steg 11. Du kan ignorera värdet för **mcastaddr**. Behåll bara den befintliga posten. Posterna för **token** och **konsensus** måste anges enligt Microsoft Azure [SAP HANA-dokumentation][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

Det andra avsnittet, **loggning,** ändrades inte från de angivna standardinställningarna:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

I det tredje avsnittet visas **nodlisten**. Alla noder i klustret måste visa upp med sin **nodid:**

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

I det sista avsnittet, **kvorum**, är det viktigt att ange värdet för **expected_votes** korrekt. Det måste vara antalet noder inklusive majoritetsskaparnoden. Och värdet för **two_node** måste vara **0**. Ta inte bort posten helt. Ställ bara in värdet på **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Starta om tjänsten via **systemctl:**

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD-enhet

Konfigurera en SBD-enhet på en Virtuell Azure-dator beskrivs i [SBD-stängsel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Kontrollera först på den virtuella datorn för SBD-servern om det finns ACL-poster för varje nod i klustret. Kör följande kommando på vm-servern för SBD:Run the following command on the SBD server VM:


<pre><code>
targetcli ls
</code></pre>


På testsystemet ser utdata från kommandot ut som följande exempel. ACL-namn som **iqn.2006-04.hso-db-0.local:hso-db-0** måste anges som motsvarande initierarnamn på de virtuella datorerna. Varje virtuell dator behöver en annan.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Kontrollera sedan att initierarnamnen på alla virtuella datorer är olika och motsvarar de tidigare visade posterna. Det här exemplet är från arbetarnod 1 på plats 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Utdata ser ut som följande exempel:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Kontrollera sedan att **identifieringen** fungerar korrekt. Kör följande kommando på varje klusternod med hjälp av IP-adressen för den virtuella datorn för SBD-servern:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Utdata ska se ut som följande exempel:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Nästa bevispunkt är att kontrollera att noden ser SDB-enheten. Kontrollera det på varje nod inklusive majoritetsmaskinnoden:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Utdata ska se ut som följande exempel. Namnen kan dock skilja sig åt. Enhetsnamnet kan också ändras när den virtuella datorn har startats om:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Beroende på systemets status hjälper det ibland till att starta om iSCSI-tjänsterna för att lösa problem. Kör sedan följande kommandon:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Från valfri nod kan du kontrollera om alla noder är **klara**. Kontrollera att du använder rätt enhetsnamn på en viss nod:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Utdata ska vara **tydlig** för varje nod i klustret:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


En annan SBD kontroll är **dump** alternativet för **sbd** kommandot. I det här exempelkommandot och utdata från majoritetsnoden var **enhetsnamnet sdd**, inte **sdm:**

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Utdata, bortsett från enhetens namn, ska se likadan ut på alla noder:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

En kontroll för SBD är möjligheten att skicka ett meddelande till en annan nod. Om du vill skicka ett meddelande till arbetsnod 2 på plats 2 kör du följande kommando på arbetsnod 1 på plats 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

På mål-VM-sidan, **hso-hana-vm-s2-2** i det här exemplet, hittar du följande post i **/var/log/messages:**

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Kontrollera att posterna i **/etc/sysconfig/sbd** motsvarar beskrivningen i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Kontrollera att startinställningen i **/etc/iscsi/iscsid.conf** är inställd på automatisk.

Följande poster är viktiga i **/etc/sysconfig/sbd**. Anpassa **id-värdet** om det behövs:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Kontrollera startinställningen i **/etc/iscsi/iscsid.conf**. Den erforderliga inställningen borde ha skett med följande **iscsiadm-kommando,** som beskrivs i dokumentationen. Verifiera och anpassa den manuellt med **vi** om den är annorlunda.

Det här kommandot anger startbeteende:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Gör denna post i **/etc/iscsi/iscsid.conf:**

<pre><code>
node.startup = automatic
</code></pre>

Under testning och verifiering, efter omstarten av en virtuell dator, var SBD-enheten inte längre synlig längre i vissa fall. Det fanns en diskrepans mellan startinställningen och vad YaST2 visade. Så här kontrollerar du inställningarna:

1. Starta YaST2.
2. Välj **Nätverkstjänster** till vänster.
3. Bläddra nedåt till höger till **iSCSI-initieraren** och välj den.
4. På nästa skärm under fliken **Tjänst** visas nodens unika initierarnamn.
5. Ovanför initierarnamnet kontrollerar du att **värdet För tjänstens start** är inställt på Vid **start**.
6. Om det inte är, sedan ställa in den till **vid uppstart** i stället för **manuellt**.
7. Växla sedan den övre fliken till **Anslutna mål**.
8. På skärmen **Anslutna mål** bör du se en post för SBD-enheten som det här exemplet: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Kontrollera om **startvärdet** är inställt **på vid uppstart**.
10. Om inte väljer du **Redigera** och ändrar det.
11. Spara ändringarna och avsluta YaST2.



## <a name="pacemaker"></a>Pacemaker

När allt har konfigurerats korrekt kan du köra följande kommando på varje nod för att kontrollera status för pacemakertjänsten:

<pre><code>
systemctl status pacemaker
</code></pre>

Den övre av utdata ska se ut som följande exempel. Det är viktigt att statusen efter **Aktiv** visas som **inläst** och **aktiv (körs)**. Statusen efter **Inläst** måste visas som **aktiverad**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Om inställningen fortfarande är **inaktiverad**kör du följande kommando:

<pre><code>
systemctl enable pacemaker
</code></pre>

Om du vill visa alla konfigurerade resurser i Pacemaker kör du följande kommando:

<pre><code>
crm status
</code></pre>

Utdata ska se ut som följande exempel. Det är bra att **cln** och **MSL** resurser visas som stoppas på majoriteten maker VM, **hso-hana-dm**. Det finns ingen SAP HANA-installation på majoritetsnoden. Så **cln** och **MSL** resurser visas som stoppas. Det är viktigt att det visar rätt totalt antal virtuella datorer, **7**. Alla virtuella datorer som ingår i klustret måste visas med **statusen Online**. Den aktuella primära huvudnoden måste kännas igen korrekt. I det här exemplet är det **hso-hana-vm-s1-0:**

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Ett viktigt inslag i Pacemaker är underhållsläge. I det här läget kan du göra ändringar utan att provocera fram en omedelbar klusteråtgärd. Ett exempel är en omstart av den virtuella datorn. Ett vanligt användningsfall skulle planeras underhåll av operativsystemet eller Azure-infrastrukturen. Se [Planerat underhåll](#planned-maintenance). Använd följande kommando för att placera Pacemaker i underhållsläge:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

När du kontrollerar med **crm-status**märker du i utdata att alla resurser är markerade som **ohanterade**. I det här läget reagerar klustret inte på några ändringar som att starta eller stoppa SAP HANA.
Följande exempel visar utdata från **kommandot crm-status** medan klustret är i underhållsläge:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Det här kommandots exempel visar hur du avslutar underhållsläget för kluster:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Ett annat **crm-kommando** får hela klusterkonfigurationen till en redigerare, så att du kan redigera den. När ändringarna har sparats startar klustret lämpliga åtgärder:

<pre><code>
crm configure edit
</code></pre>

Om du vill titta på hela klusterkonfigurationen använder du alternativet **crm show:**

<pre><code>
crm configure show
</code></pre>



Efter fel på klusterresurser visar **kommandot crm-status** en lista över **misslyckade åtgärder**. Se följande exempel på den här utdata:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Det är nödvändigt att göra en klusterrensning efter fel. Använd **crm-kommandot** igen och använd kommandoalternativet **rensning** för att bli av med dessa misslyckade åtgärdsposter. Namnge motsvarande klusterresurs enligt följande:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Kommandot ska returnera utdata som följande exempel:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Redundans eller uppköp

Som diskuterats i [Viktiga anteckningar](#important-notes)bör du inte använda en standard graciös avstängning för att testa kluster redundans eller SAP HANA HSR-övertagande. I stället rekommenderar vi att du utlöser en kernel-panik, tvingar en resursmigrering eller eventuellt stänger av alla nätverk på os-nivån för en virtuell dator. En annan metod är kommandot **för vänteläge \<för crm-nod.\> ** Se [SUSE-dokumentet][sles-12-ha-paper]. 

Följande tre exempelkommandon kan tvinga fram en klusterväxling:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Som beskrivs i [Planerat underhåll](#planned-maintenance), ett bra sätt att övervaka klusteraktiviteter är att köra **SAPHanaSR-showAttr** med **klockan** kommandot:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Det hjälper också att titta på SAP HANA-landskapsstatus som kommer från ett SAP Python-skript. Klusterinställningarna söker efter det här statusvärdet. Det blir tydligt när du tänker på ett arbetsnodfel. Om en arbetsnod går ned returnerar INTE SAP HANA omedelbart ett fel för hälsan för hela skalningssystemet. 

Det finns några försök att undvika onödiga redundans. Klustret reagerar bara om statusen ändras från **Ok**, returnerar värde **4**till **fel**, returnerar värde **1**. Så det är korrekt om utdata från **SAPHanaSR-showAttr** visar en virtuell dator med tillståndet **offline**. Men det finns ingen aktivitet ännu att byta primära och sekundära. Ingen klusteraktivitet utlöses så länge SAP HANA inte returnerar ett fel.

Du kan övervaka HÄLSOTILLSTÅNDSSTATUSEN FÖR SAP HANA-landskapet som användare ** \<HANA\>SID adm** genom att anropa SAP Python-skriptet enligt följande. Du kanske måste anpassa banan:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Utdata från det här kommandot ska se ut som följande exempel. Kolumnen **Värdstatus** och den **övergripande värdstatusen** är båda viktiga. Den faktiska utdata är bredare, med ytterligare kolumner.
För att göra utdatatabellen mer läsbar i det här dokumentet togs de flesta kolumnerna på höger sida bort:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Det finns ett annat kommando för att kontrollera aktuella klusteraktiviteter. Se följande kommando och utdatasvansen efter att huvudnoden för den primära platsen har dödats. Du kan se listan över övergångsåtgärder som **att befordra** den tidigare sekundära huvudnoden, **hso-hana-vm-s2-0**, som den nya primära huvudhanteraren. Om allt är bra och alla aktiviteter är klara måste listan **Övergångssammanfattning** vara tom.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Planerat underhåll 

Det finns olika användningsfall när det gäller planerat underhåll. En fråga är om det bara är infrastrukturunderhåll som ändringar på OS-nivå och diskkonfiguration eller en HANA-uppgradering.
Du kan hitta ytterligare information i dokument från SUSE som [Mot noll driftstopp][sles-zero-downtime-paper] eller [SAP HANA SR Prestandaoptimerat Scenario][sles-12-for-sap]. Dessa dokument innehåller också exempel som visar hur du manuellt migrerar en primär.

Intensiv intern testning gjordes för att verifiera användningsfallet för underhåll av infrastruktur. För att undvika problem relaterade till migrering av den primära, bestämde vi oss för att alltid migrera en primär innan du sätter ett kluster i underhållsläge. På så sätt är det inte nödvändigt att få klustret att glömma den tidigare situationen: vilken sida som var primär och vilken som var sekundär.

Det finns två olika situationer i detta avseende:

- **Planerat underhåll på den aktuella sekundära**. I det här fallet kan du bara placera klustret i underhållsläge och göra jobbet på den sekundära utan att påverka klustret.

- **Planerat underhåll på den aktuella primära**. Så att användarna kan fortsätta att arbeta under underhåll, måste du tvinga en redundans. Med den här metoden måste du utlösa kluster redundans av Pacemaker och inte bara på SAP HANA HSR-nivå. Pacemaker-installationen utlöser automatiskt SAP HANA-övertagandet. Du måste också utföra redundansen innan du sätter klustret i underhållsläge.

Förfarandet för underhåll på den aktuella sekundära platsen är följande:

1. Sätt klustret i underhållsläge.
2. Utför arbetet på den sekundära platsen. 
3. Avsluta underhållsläget för klustret.

Proceduren för underhåll på den aktuella primära platsen är mer komplex:

1. Utlösa en redundans- eller SAP HANA-uppköp manuellt via en pacemakerresursmigrering. Se detaljer som följer.
2. SAP HANA på den tidigare primära platsen stängs av av klusterinställningarna.
3. Sätt klustret i underhållsläge.
4. När underhållsarbetet är klart registrerar du den tidigare primärt som den nya sekundära platsen.
5. Rensa klusterkonfigurationen. Se detaljer som följer.
6. Avsluta underhållsläget för klustret.


Migrera en resurs lägger till en post i klusterkonfigurationen. Ett exempel är att tvinga fram en redundans. Du måste rensa dessa poster innan du avslutar underhållsläget. Se följande exempel.

Tvinga först en klusterväxling genom att migrera **msl-resursen** till den aktuella sekundära huvudnoden. Det här kommandot ger en varning om att ett **flyttvillkor** har skapats:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Kontrollera redundansprocessen via kommandot **SAPHanaSR-showAttr**. Om du vill övervaka klusterstatusen öppnar du ett dedikerat skalfönster och startar kommandot med **klockan:**

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Utdata ska visa den manuella redundansen. Den tidigare sekundära master noden **blev befordrad**, i detta urval, **hso-hana-vm-s2-0**. Den tidigare primära platsen **stoppades, lss** värde **1** för tidigare primära huvudnod **hso-hana-vm-s1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Efter kluster redundans och SAP HANA övertagande, sätta klustret i underhållsläge som beskrivs i [Pacemaker](#pacemaker).

Kommandona **SAPHanaSR-showAttr** och **crm-status** anger ingenting om de begränsningar som skapas av resursmigreringen. Ett alternativ för att göra dessa villkor synliga är att visa hela klusterresurskonfigurationen med följande kommando:

<pre><code>
crm configure show
</code></pre>

I klusterkonfigurationen hittar du ett nytt platsvillkor som orsakas av den tidigare manuella resursmigreringen. Det här exemplet börjar med **plats cli-**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Tyvärr kan sådana begränsningar påverka det övergripande klusterbeteendet. Så det är obligatoriskt att ta bort dem igen innan du tar upp hela systemet igen. Med kommandot **unmigrate** är det möjligt att rensa de platsbegränsningar som skapades tidigare. Namngivningen kan vara lite förvirrande. Den försöker inte migrera resursen tillbaka till den ursprungliga virtuella datorn som den migrerades från. Det tar bara bort platsbegränsningar och returnerar även motsvarande information när du kör kommandot:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

I slutet av underhållsarbetet stoppar du underhållsläget för klustret enligt [Pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report för att samla in loggfiler

För att analysera pacemakerklusterproblem är det användbart och även begärt av SUSE-stöd för att köra **verktyget hb_report.** Den samlar alla viktiga loggfiler som du behöver för att analysera vad som hände. Det här exempelanropet använder en start- och sluttid där en viss incident inträffade. Se också [Viktiga anteckningar:](#important-notes)

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Kommandot talar om var de komprimerade loggfilerna anges:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Du kan sedan extrahera de enskilda filerna via **standardtjärkommandot:**

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

När du tittar på de extraherade filerna hittar du alla loggfiler. De flesta av dem sattes i separata kataloger för varje nod i klustret:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Inom det angivna tidsintervallet dödades den aktuella huvudnoden **hso-hana-vm-s1-0.** Du kan hitta poster relaterade till den här händelsen i **journal.log:**

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Ett annat exempel är Pacemaker-loggfilen på den sekundära huvudbonaden, som blev den nya primära huvudbonaden. Det här utdraget visar att statusen för den dödade primära huvudnoden angavs till **offline:**

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


Följande utdrag kommer från SAP HANA **global.ini-filen** på klusterplats 2. I det här exemplet visas värdens lösningsposter för att använda olika nätverk för SAP HANA internodekommunikation och HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

Klusterlösningen tillhandahåller ett webbläsargränssnitt som erbjuder ett GUI för användare som föredrar menyer och grafik till att ha alla kommandon på skalnivå.
Om du vill använda webbläsargränssnittet ersätter du ** \<noden\> ** med en faktisk SAP HANA-nod i följande URL. Ange sedan autentiseringsuppgifterna för klustret **(användarkluster):**

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Den här skärmbilden visar klusterinstrumentpanelen:


![Instrumentpanel för hökkluster](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


I det här exemplet visas de platsbegränsningar som orsakas av en klusterresursmigrering enligt förklaras i [Planerat underhåll:](#planned-maintenance)


![Begränsningar för hawk-lista](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Du kan också **hb_report** ladda upp hb_report-utdata i Hawk under **Historik**, som visas på följande sätt. Se hb_report för att samla in loggfiler: 

![Hawk ladda upp hb_report utgång](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Med **historikutforskaren**kan du sedan gå igenom alla klusterövergångar som ingår i hb_report-utdata: **hb_report**

![Hawk övergångar i hb_report produktionen](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Den här slutliga skärmbilden visar **avsnittet Detaljer i** en enda övergång. Klustret reagerade på en primär huvudnodkrasch, nod **hso-hana-vm-s1-0**. Det är nu främja den sekundära noden som ny mästare, **hso-hana-vm-s2-0:**

![Hawk enda övergång](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Nästa steg

Den här felsökningsguiden beskriver hög tillgänglighet för SAP HANA i en skalningskonfiguration. Förutom databasen är en annan viktig komponent i ett SAP-landskap SAP NetWeaver-stacken. Lär dig mer om [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer som använder SUSE Enterprise Linux Server][sap-nw-ha-guide-sles].

