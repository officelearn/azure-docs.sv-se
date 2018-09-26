---
title: Felsökning av SAP HANA 2.0 skalbar HSR Pacemaker installationsprogrammet med SLES 12 SP3 på Azure virtual machines | Microsoft Docs
description: Guiden för att kontrollera och felsöka en komplexa SAP HANA skalbar konfiguration för hög tillgänglighet baserat på SAP HANA System Replication (HSR) och Pacemaker på SLES 12 SP3 som körs på virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 6c0d6397246e8b8db1d59c26229e37a722d49f48
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184979"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verifiera och felsöka installationen av SAP HANA skalbar hög tillgänglighet på SLES 12 SP3 

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


Den här artikeln skrevs för att se Pacemaker klusterkonfigurationen för SAP HANA-utskalning som körs på virtuella Azure-datorer. Kluster-installation gjordes i kombination med SAP HANA System Replication (HSR) och SUSE-RPM paketera SAPHanaSR skalning. Alla tester har utförts på SUSE SLES 12 SP3 endast. Det finns flera avsnitt som beskriver olika områden och inkluderar exempelkommandon och utdrag från konfigurationsfiler. De här exemplen rekommenderas som en metod för att verifiera och kontrollera inställningen för hela klustret.



## <a name="important-notes"></a>Obs!

Alla tester för SAP HANA skalbar tillsammans med SAP HANA System Replication och Pacemaker gjordes med SAP HANA 2.0 endast. Versionen av operativsystemet var SUSE Linux Enterprise Server 12 SP3 för SAP-program. Dessutom har det senaste RPM-paketet SAPHanaSR skalning från SUSE för pacemaker klustret.
SUSE publicerat en detaljerad beskrivning av inställningen prestandaoptimerad som du hittar [här][sles-hana-scale-out-ha-paper]

För VM-typer som stöds för SAP HANA skalbar Kontrollera den [SAP HANA-certifierade IaaS directory][sap-hana-iaas-list]

Det uppstod ett tekniskt problem med SAP HANA skalbar i kombination med flera undernät och virtuella nätverkskort och hur du konfigurerar HSR. Det är obligatoriskt att använda de senaste uppdateringarna för SAP HANA 2.0 där det här problemet har lösts. Följande SAP HANA-versioner stöds: 

**REV2.00.024.04 eller högre & rev2.00.032 eller högre.**

Om det ska finnas en situation som kräver support från SUSE Följ den här [guide][suse-pacemaker-support-log-files]. Samla in all information om SAP HANA HA klustret enligt beskrivningen i artikeln. SUSE support behöver den här informationen för ytterligare analys.

Under interna tester hände det att konfiguration av de fick förvirrad av en normal VM korrekt avslutning via Azure portal. Därför rekommenderar vi för att testa klusterredundans med andra metoder. Använda metoder som framtvingar en kernel panic-meddelande eller stänga av nätverk eller migrera den **msl** resurs (Mer information finns i avsnitten nedan). Antas att det sker en standard avstängning med avsikt. Det bästa exemplet för en avsiktlig avstängning är Underhåll (Mer information finns i avsnittet om planerat underhåll).

Under interna tester hände det att konfiguration av fick förväxlas efter en manuell SAP HANA ta över vid klustret är i underhållsläge. Vi rekommenderar för att växla den tillbaka manuellt igen innan du avslutar underhållsläget för klustret. Ett annat alternativ är att utlösa redundans innan du sätter klustret i underhållsläge (se avsnittet om planerat underhåll för mer information). I dokumentationen från SUSE beskriver hur du kan återställa klustret i detta avseende fungerar med crm-kommando. Men den metoden som vi nämnde innan visat sig vara robust under interna tester och aldrig visade eventuella oväntade sidoeffekter.

När använda crm migrerar kommandot Missa inte rensa klusterkonfigurationen. Det lägger till Platsbegränsningar, vilket du inte kanske är medveten om. Dessa begränsningar kan påverka beteendet kluster (se mer information finns i avsnittet om planerat underhåll).



## <a name="test-system-description"></a>Testa Systembeskrivning

För SAP HANA skalbar HA kontroll och certifiering som en konfiguration har använts, som består av två system med tre noder för SAP HANA var - och en huvudnod och två arbetsroller. Här är listan över namn på virtuella datorer och interna IP-adresser. Alla verifiering exempel ytterligare som ned har utförts på dessa virtuella datorer. Med namn på virtuella datorer och IP bör-adresser i kommandoexempel hjälpa till att bättre förstå kommandon och deras produktion.


| Nodtyp | VM-namn | IP-adress |
| --- | --- | --- |
| Huvudnoden på plats 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Underordnad nod 1 på plats 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Underordnad nod 2 på plats 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Huvudnoden på plats 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Underordnad nod 1 på plats 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Underordnad nod 2 på plats 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Maker majoritetsnod | hso-hana-dm | 10.0.0.13 |
| Uppstår enheten servern | hso-hana-uppstår | 10.0.0.19 |
| | | |
| NFS-server 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS-server 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Flera undernät och virtuella nätverkskort

Följande rekommendationer för SAP HANA-nätverk skapades tre undernät inom en Azure-nätverk. SAP HANA skala ut på Azure måste ha installerats i icke-delade läge, vilket innebär att varje nod använder lokala volymer för **/hana/data** och **/hana/log**. På grund av med hjälp av endast lokala volymer är det inte nödvändigt att definiera ett separat undernät för lagring:

- 10.0.2.0/24 för SAP HANA-kommunikation mellan noder
- 10.0.1.0/24 för SAP HANA-Systemreplikering HSR
- 10.0.0.0/24 för alla andra

Information om SAP HANA-konfiguration som rör bruket av flera virtuella nätverk finns i avsnittet **global.ini** längre ner.

Motsvarar antalet undernät varje virtuell dator i klustret har tre virtuella nätverkskort. [Detta] [ azure-linux-multiple-nics] artikel beskriver ett potentiellt routningsproblem på Azure när du distribuerar en Linux-VM. Den här specifika routning avsnittet gäller endast för användning av flera virtuella nätverkskort. Problemet kan lösas genom SUSE standard i SLES 12 SP3. Artikeln från SUSE om det här avsnittet finns [här][suse-cloud-netconfig].


Som en grundläggande kontroll för att kontrollera om SAP HANA är korrekt konfigurerad för att använda flera nätverk, bara köra kommandona nedan. Första steget är att kontrollera på nivån för att alla tre interna IP-adresser för alla tre undernät är aktiva. Om du har definierat undernät med olika IP-adressintervall som du behöver anpassa kommandon:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Här är ett exempel på utdata från andra arbetsnoden på plats 2. Du kan se tre olika interna IP-adresser från eth0 och eth1 eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Andra steget är verifiering av SAP HANA-portar för namnserver och HSR. SAP HANA ska lyssna på motsvarande undernät. Du måste anpassa kommandon beroende på antalet SAP HANA-instans. För testsystemet instans-antalet var **00**. Det finns olika sätt att ta reda på vilka portar som används. 

Nedan visas ett SQL-uttryck som returnerar instans-ID och instansnummer bland annat:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

För att hitta rätt portnummer, du kan leta, till exempel i HANA Studio under ”**Configuration**” eller via ett SQL-uttryck:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Du hittar alla portar som används i SAP programstack inklusive SAP HANA, söka [här][sap-list-port-numbers].

Givet talet instans **00** testsystemet SAP HANA 2.0 är portnumret för namnservern **30001**. Portnumret för HSR meta kommunikationen är **40002**. Ett alternativ är att logga in till en arbetsnod och sedan kontrollera huvudnoden-tjänster. Här gjordes kontrollen på arbetsnoden 2 på plats 2 försöker ansluta till huvudnoden på plats 2.

Kontrollera namnserver-port:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Resultatet bör se ut som exemplet utdata nedan för att bevisa att kommunikationen mellan noder använder undernätet **10.0.2.0/24**.
Endast Anslut via undernät **10.0.2.0/24** ska lyckas:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Nu söka efter porten som HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Resultatet bör se ut som exemplet utdata nedan för att bevisa att HSR kommunikationen använder undernätet **10.0.1.0/24**.
Endast Anslut via undernät **10.0.1.0/24** ska lyckas:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Konfigurationsfilen corosync måste vara korrekt på varje nod i klustret, inklusive maker majoritetsnod. Om anslutning till kluster för en nod inte fungerar som förväntat, skapa och/eller kopiera **/etc/corosync/corosync.conf** manuellt på/till alla noder och starta om tjänsten.

Här är innehållet i **corosync.conf** testsystem som exempel.

Första delen är **totem** enligt beskrivningen i det här [dokumentation] [ sles-pacemaker-ha-guide] (avsnittet klusterinstallationen steg 11). Du kan ignorera värdet för **mcastaddr**. Bara behålla den befintliga posten. Poster för **token** och **caiq** måste anges enligt dokumentationen för Microsoft Azure SAP HANA, som du hittar [här][sles-pacemaker-ha-guide]

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

Det andra avsnittet **loggning** har inte ändrats från de angivna standardvärdena:

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

De tredje avsnittet visar den **nodelist**. Alla noder i klustret måste visas med deras nod-id:

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

I det sista avsnittet **kvorum**, är det viktigt att ange värdet för **expected_votes** korrekt. Det måste vara antalet noder, inklusive maker majoritetsnod. Och värdet för **two_node** måste vara **0**. Ta inte bort posten helt. Anger värdet till **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Starta om tjänsten via **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Uppstår enhet

Mer information finns i dokumentationen om hur du ställer in en uppstår-enhet på en Azure VM [här] [ sles-pacemaker-ha-guide] (avsnittet uppstår avgränsningar).

Första du ska kontrollera är att titta på uppstår server-dator, om det finns ACL-posterna för varje nod i klustret. Kör följande kommando på uppstår server-dator:


<pre><code>
targetcli ls
</code></pre>


På testsystemet tittat kommandots utdata som exemplet nedan. ACL: namn som **iqn.2006-04.hso-db-0.local:hso-db-0** måste anges som motsvarande Initierarnamn på de virtuella datorerna. Varje virtuell dator måste ha ett annat namn.

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

Kontrollera därefter att initierare namnen på alla virtuella datorer skiljer sig och motsvarar de poster som visas ovan. Här är ett exempel från arbetsnod 1 på platsen 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Utdata såg ut som exemplet nedan:

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

Därefter kontrollerar du om den **identifiering** fungerar korrekt och kör följande kommando på varje nod i klustret med hjälp av IP-adressen för uppstår server-dator:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Utdata bör se ut som exemplet nedan:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Nästa bevis punkt är att kontrollera att noden ser SDB enheten. Kolla på varje nod, inklusive maker majoritetsnod:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Utdata bör se ut som exemplet nedan. Tänk på att namnen kan variera (enhetsnamn kan också ändras när den virtuella datorn startas om):

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Beroende på status för systemet, kan det ibland bidra till att starta om iscsi-tjänster för att lösa problem. Kör sedan följande kommandon:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Från en nod kan du kontrollera om alla noder är **Rensa**. Se upp bara att använda rätt enhetens namn på en viss nod:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Utdata bör visa **Rensa** för varje nod i klustret:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


En annan uppstår Kontrollera är den **dump** alternativet kommandots uppstår. Här är en exempel-kommando och utdata från maker-majoritetsnod där enhetsnamnet inte var **sdm** men **sdd**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Utdata (med undantag för namnet på enheten) bör se ut samma på alla noder:

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

En mer kontroll för uppstår är möjligheten att skicka ett meddelande till en annan nod. Du kör följande kommando på arbetsnoden 1 på plats 2 för att skicka ett meddelande till underordnad nod 2 på plats 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

På mål-VM på klientsidan – som var **hso-hana-vm-s2-2** i det här exemplet – du kan hitta följande post i **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Kontrollera om posterna i **/etc/sysconfig/sbd** motsvarar beskrivningen i vår [dokumentation] [ sles-pacemaker-ha-guide] (avsnittet uppstår avgränsningar). Kontrollera att start i **/etc/iscsi/iscsid.conf** är inställd på automatik.

Viktiga poster i **/etc/sysconfig/sbd** (Anpassa id-värdet om det behövs):

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Ett annat objekt att kontrollera är Start-inställningen i **/etc/iscsi/iscsid.conf**. Den obligatoriska inställningen ska ha hänt genom den **iscsiadm** kommando visas nedan, vilket beskrivs i dokumentationen. Det vara bra att verifiera och anpassa kanske den manuellt med **vi** om det är olika.

Kommando för att ange appkonfigurering:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Post i **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Under testning och verifiering förekomster hände där efter omstart av en virtuell dator uppstår enheten inte visas längre. Det uppstod en diskrepans mellan startinställningen och vilka yast2 visade. Utför stegen nedan om du vill kontrollera inställningarna:

1. Starta yast2
2. Välj **nätverkstjänster** till vänster
3. Rulla ned till höger till **iSCSI-initierare** och välj den
4. På nästa skärm under den **Service** fliken bör du se initierarnamnet unikt för nod
5. Ovan Initierarnamn, se till att den **tjänsten startar** värdet är inställt på **när du startar**
6. Om det inte är fallet, ange den som **när du startar** i stället för **manuellt**
7. Växla sedan fliken längst upp till **anslutna mål**
8. Du bör se en post för enheten uppstår som det här exemplet på skärmen ansluten mål: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**
9. Kontrollera om start-värdet anges till ”**onboot**”
10. Om inte, Välj **redigera** och ändra den
11. Spara ändringarna och stänga yast2



## <a name="pacemaker"></a>Pacemaker

När allt är korrekt konfigurerad, kan du köra följande kommando på varje nod för att kontrollera status för tjänsten pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

Överst utdata bör se ut som exemplet nedan. Det är viktigt som status efter **Active** visas som **lästs in** och **aktiv (körs)**. Status efter ”läsa in” måste visas som **aktiverat**.

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

Om inställningen är stanna kvar på **inaktiverad**, kör du följande kommando:

<pre><code>
systemctl enable pacemaker
</code></pre>

Om du vill se alla konfigurerade resurser i pacemaker, kör du följande kommando:

<pre><code>
crm status
</code></pre>

Utdata bör se ut som exemplet nedan. Det är ok att cln och msl resurser visas som stoppats på merparten maker VM (**hso-hana-dm**). Det finns inga SAP HANA-installation på maker majoritetsnod. Därför den **cln** och **msl** resurser visas när slutade. Det är viktigt att den visar rätt totala antalet virtuella datorer (**7**). Alla virtuella datorer som ingår i klustret måste finnas i listan med statusen **Online**. Aktuella primära huvudnoden måste vara en korrekt (i det här exemplet är det **hso-hana-vm-s1-0**).

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

En viktig funktion i pacemaker är att försätta i underhållsläge. Det här läget tillåter att göra ändringar (till exempel en omstart av virtuell dator) utan provoking en omedelbar kluster-åtgärd. Ett vanligt användningsfall är planerat underhåll för OS- eller Azure-infrastruktur (Se även separat avsnitt om planerat underhåll). Använd följande kommando för att försätta pacemaker i underhållsläge:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Vid sökning med **crm status**, du ser i utdata som alla resurser som är markerade som **ohanterade**. I det här tillståndet reagerar klustret inte på ändringar som Starta/Stoppa SAP HANA.
Här är ett exempel på utdata från den **crm status** kommandot när klustret är i underhållsläge:

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


Och i kommandot exemplet nedan visas hur du avslutar underhållsläget för klustret:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


En annan crm-kommandot kan hämta den fullständiga klusterkonfigurationen i ett redigeringsprogram med möjlighet att redigera. När du har sparat ändringarna börjar klustret åtgärder:

<pre><code>
crm configure edit
</code></pre>

För att bara titta på den fullständiga klusterkonfigurationen, använder du i crm **visa** alternativet:

<pre><code>
crm configure show
</code></pre>



Vid fel i klusterresurser det händer som den **crm status** kommandot visar en lista över **misslyckades åtgärder**. Se ett exempel för den här utdatan nedan:


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

Det är nödvändigt att göra en kluster-rensning efter fel. Bara använda crm-kommandot igen och Använd kommandoalternativet **Rensa** att ta bort dessa misslyckades åtgärden poster namngivning av motsvarande klusterresursen enligt nedan:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Kommandot ska returnera utdata, som ser ut som exemplet nedan:

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



## <a name="failover--takeover"></a>Redundans / gäller

Du bör inte använda en standard korrekt avslutning testa klusterredundans eller SAP HANA HSR ta över som nämnts redan i det första avsnittet med viktiga meddelanden. I stället rekommenderar vi att utlösa, till exempel en kernel panic-meddelande eller tvinga fram en resursmigrering eller kanske stänga av alla nätverk på operativsystemnivå för en virtuell dator. En annan metod är den **crm \<nod\> vänteläge** kommando. Se även SUSE-dokumentet som du hittar [här][sles-12-ha-paper]. Nedan kan du se tre exempelkommandon för att tvinga klusterredundans:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Som också beskrivs i avsnittet om planerat underhåll är ett bra sätt att övervaka aktiviteter för klustret att köra **SAPHanaSR showAttr** med den **watch** kommando:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Dessutom hjälper dig för att se enhetens status för SAP HANA-landskap som kommer från en SAP python-skriptet. Den här statusvärdet är det som konfiguration som är ute efter. Det blir tydligt när en worker-nodfel. Om en underordnad nod slutar att fungera returnerar SAP HANA inte omedelbart ett fel för hälsostatus för helt skalbar. Det finns vissa nya försök att undvika onödiga redundans. Endast om ändras statusen från Ok (returvärdet 4) till fel (returnerade värdet 1) reagerar klustret. Därför är det rätt, om utdata från **SAPHanaSR showAttr** visar en virtuell dator med tillståndet **offline** men det finns ingen aktivitet ännu för att växla primära och sekundära. Ingen klusteraktivitet aktiveras så länge som SAP HANA inte returnerar ett fel.

Du kan övervaka hälsotillståndet för SAP HANA-landskap som användare \<HANA SID\>adm genom att anropa SAP-python-skript på följande sätt (du kan behöva anpassa sökvägen):

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Kommandots utdata bör se ut som exemplet nedan. Viktigt är det **Värdstatusen** kolumnen samt de **värd status för övergripande**. Faktiska utdata är i själva verket bredare med ytterligare kolumner.
De flesta kolumner till höger om du vill göra utdatatabellen lättare att läsa i det här dokumentet har tas bort:

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


Det finns ett annat kommando för att kontrollera den aktuella klustret aktiviteter. Nedan ser du kommandot och slutet av utdata när huvudnoden för den primära platsen har avslutats. Du kan se en lista över övergången åtgärder som att **befordrar** tidigare sekundära huvudnoden (**hso-hana-vm-s2-0**) som den nya primära master. Om allt är det ok och alla aktiviteter är slutförda är den här listan över **övergången sammanfattning** måste vara tom.

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

Det finns olika användningsfall när det gäller planerat underhåll. En fråga är till exempel om det är bara infrastrukturunderhåll som ändringar på operativsystemsnivån och diskkonfiguration eller en HANA-uppgradering.
Du hittar mer information i dokument från SUSE som [här] [ sles-zero-downtime-paper] eller [en annan en här][sles-12-for-sap]. De här dokumenten innehåller även exempel hur du migrerar en primär manuellt.

Intensiv interna tester har utförts för att verifiera användningsfall för infrastruktur-underhåll. Om du vill undvika alla typer av problem som rör migrerar primärt gjordes beslutet att migrera en primär alltid innan du gör klustret i underhållsläge. På så sätt behöver inte göra klustret glömma tidigare situationen (vilken sida var primära och vilken sida sekundär).

Det finns två olika situationer i detta avseende fungerar:

1. Planerat underhåll på den aktuella sekundärt. 
   Du kan i så fall bara försätta klustret i underhållsläge och göra jobbet på sekundärt utan att påverka klustret

2. Planerat underhåll på den aktuella primärt. 
   Om du vill tillåta användare att fortsätta arbeta under underhållet, är det nödvändigt att tvinga fram en redundansväxling. Med den här metoden måste du aktivera klusterredundans av pacemaker och inte bara på SAP HANA HSR nivå. Pacemaker installationsprogrammet startar automatiskt övertagande SAP HANA. Dessutom är det nödvändigt att utföra redundansväxlingen innan du sätter klustret i underhållsläge.

Proceduren för underhåll på den aktuella sekundära platsen vill stegen nedan:

1. Försätta klustret i underhållsläge
2. Slutföra arbetet på den sekundära platsen 
3. Avbryta underhållsläget för kluster

Proceduren för underhåll på den aktuella primära platsen är mer komplexa:

1. Manuellt utlösa redundans / SAP HANA ta över via en Pacemaker resursmigrering (se detaljer nedan)
2. SAP HANA på den primära platsen tidigare komma stängs av installationsprogrammet för kluster
3. Försätta klustret i underhållsläge
4. När underhållsarbetet är klar, registrerar du den tidigare versionen primärt som den nya sekundära platsen
5. Rensa klusterkonfiguration (se detaljer nedan)
6. Avbryta underhållsläget för kluster


Migrera en resurs (till exempel att tvinga fram en redundansväxling) läggs en post klusterkonfigurationen. Du måste rensa upp de här posterna innan du avslutar underhållsläget. Här är ett exempel:

Första steget är att tvinga klusterredundans genom att migrera msl resursen till aktuella sekundära huvudnoden. Kommandot nedan ger en varning att villkoret ”flytta” har skapats.

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Kontrollera redundansprocessen via kommandot **SAPHanaSR showAttr**. Det hjälper dig för att övervaka klusterstatusen är att öppna en dedikerad shell-fönstret och börja kommandot med **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Utdata bör återspegla manuell växling vid fel. Har du tidigare sekundära huvudnoden **befordras** (i det här exemplet **hso-hana-vm-s2-0**) och den primära platsen som tidigare har stoppats (**lss** värdet **1** för tidigare primära huvudnoden **hso-hana-vm-s1-0**): 

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

När du klusterredundans och ta över SAP HANA, att försätta klustret i underhållsläge enligt beskrivningen i avsnittet pacemaker.

Kommandona **SAPHanaSR showAttr** eller **crm status** indikerar något om de begränsningar som skapats av resursmigrering. Ett alternativ ska synas dessa villkor är att visa den fullständiga klusterkonfigurationen resurs med följande kommando:

<pre><code>
crm configure show
</code></pre>

Inom klusterkonfigurationen hittar du en ny platsbegränsning på grund av den tidigare versionen manuell resursmigrering. Här är ett exempel (post som börjar med **plats cli -**):

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Tyvärr kan sådana begränsningar påverka det övergripande beteendet för klustret. Därför är det obligatoriskt att ta bort dem igen innan du hämtar in hela systemet säkerhetskopiera. Med den **unmigrate** kommandot är det möjligt att rensa Platsbegränsningar, som skapades före. Namngivningen kan vara lite förvirrande. Det innebär inte att skulle den försöka migrera resursen tillbaka till dess ursprungliga virtuella datorn som migrerades. Den tar bara bort Platsbegränsningar och returnerar motsvarande information även när du kör kommandot:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

I slutet av underhållsarbetet stoppa underhållsläge klustret enligt avsnittet pacemaker.



## <a name="hbreport-to-collect-log-files"></a>hb_report att samla in loggfiler

För att analysera pacemaker kluster problem, det är bra och även begärda av SUSE-stöd för att köra den **hb_report** verktyget. Den samlar in alla viktiga logfiles som gör att analysen av vad som hände. Här är ett exempel-anrop med hjälp av en start- och -tid, där en specifik incident inträffade (även se första avsnittet om viktiga meddelanden):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Kommandot spill ut var det placera komprimerade loggfilerna:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Du kan sedan extrahera enskilda filer via standarden **tar** kommando:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Titta på de extrahera filerna hitta du alla loggfiler. De flesta av dem placerades i separata kataloger för varje nod i klustret:

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


Inom tidsintervallet som har angetts på den aktuella överordnade noden **hso-hana-vm-s1-0** har avslutats. I den **journal.log** du kan hitta poster som relaterar till den här händelsen:

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

Ett annat exempel är pacemaker loggfil på den sekundära master, vilket dessutom blev både den nya primära hanteraren. Här är ett utdrag som visar att status för avslutats primära huvudnoden har angetts till **offline**.

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


Nedan kan du se utdrag från SAP HANA global.ini filen på klusterplatser 2, som exempel för att visa poster för hostname-lösning för att använda olika nätverk för SAP HANA-kommunikation mellan noder och HSR:

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



## <a name="hawk"></a>HAWK

Klusterlösningen innehåller också ett webbgränssnitt som erbjuder en bra GUI för personer som föredrar menyer och grafik i förhållande till alla kommandon på shell-nivå.
Om du vill använda webbläsarens gränssnitt, tar den URL som visas nedan och Ersätt **\<nod\>** genom ett SAP HANA-nodens och sedan ange autentiseringsuppgifterna för klustret (användare **hacluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Följande skärmbild visar instrumentpanelen för klustret:


![HAWK klusterinstrumentpanel](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


På den andra skärmbilden ser du ett exempel på Platsbegränsningar på grund av en resurs klustermigrering enligt beskrivningen i avsnittet planerat underhåll:


![HAWK lista begränsningar](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


En annan bra funktion är möjligheten att ladda upp en **hb_report** utdata (se avsnittet om **hb_report**) i **HAWK** under **historik** som visas på nästa skärmbild:

![HAWK uppladdning hb_report utdata](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Den **historik Explorer** sedan kan gå igenom alla kluster övergångar som ingår i den **hb_report** utdata:

![HAWK titt på övergångar inom hb_report utdata](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

På den sista skärmbilden visas informationsavsnittet på en enkel övergång som visar att klustret uppvisat på en primär huvudnod krasch (noden **hso-hana-vm-s1-0**) och är nu att uppgradera den sekundära noden som ny master (**hso-hana-vm-s2-0**):

![HAWK titt på en enkel övergång](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Nästa steg

Den här guiden Felsök handlar om hög tillgänglighet för SAP HANA i en skalbar konfiguration. En annan viktig komponent inom en SAP-landskap förutom databasen är den SAP NetWeaver-stacken. Därefter bör du läsa om hög tillgänglighet för SAP NetWeaver på Azure virtuella datorer med SUSE Enterprise Linux Server i [detta] [ sap-nw-ha-guide-sles] artikeln.

