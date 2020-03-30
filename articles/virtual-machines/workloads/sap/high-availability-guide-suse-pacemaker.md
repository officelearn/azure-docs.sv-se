---
title: Konfigurera Pacemaker på SLES i Azure | Microsoft-dokument
description: Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure
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
ms.date: 03/17/2020
ms.author: radeltch
ms.openlocfilehash: 9d3d0ddbd1282827f17cd82228fcf0f3fba3a60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471990"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Det finns två alternativ för att konfigurera ett Pacemaker-kluster i Azure. Du kan antingen använda en fäktningsagent som tar hand om att starta om en misslyckad nod via Azure-API:erna eller så kan du använda en SBD-enhet.

SBD-enheten kräver minst en ytterligare virtuell dator som fungerar som en iSCSI-målserver och tillhandahåller en SBD-enhet. Dessa iSCSI-målservrar kan dock delas med andra Pacemaker-kluster. Fördelen med att använda en SBD-enhet är en snabbare redundanstid och, om du använder SBD-enheter lokalt, kräver inga ändringar i hur du använder pacemakerklustret. Du kan använda upp till tre SBD-enheter för ett Pacemaker-kluster för att tillåta att en SBD-enhet blir otillgänglig, till exempel vid OS-korrigering av iSCSI-målservern. Om du vill använda mer än en SBD-enhet per Pacemaker, se till att distribuera flera iSCSI-målservrar och ansluta en SBD från varje iSCSI-målserver. Vi rekommenderar att du använder antingen en SBD-enhet eller tre. Pacemaker kan inte automatiskt staket en klusternod om du bara konfigurerar två SBD-enheter och en av dem inte är tillgänglig. Om du vill kunna staket när en iSCSI målserver är nere, måste du använda tre SBD-enheter och därför tre iSCSI målservrar.

Om du inte vill investera i ytterligare en virtuell dator kan du också använda Azure Fence-agenten. Nackdelen är att en redundans kan ta mellan 10 till 15 minuter om ett resursstopp misslyckas eller klusternoderna inte längre kan kommunicera vilka varandra.

![Pacemaker på SLES översikt](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> När du planerar och distribuerar Klustrade Noder och SBD-enheter i Linux Pacemaker är det viktigt för den övergripande tillförlitligheten hos den fullständiga klusterkonfigurationen att routningen mellan de inblandade virtuella datorerna och den eller de virtuella datorerna som är värdar för SBD-enheten(enheter) inte passerar genom andra enheter som [NVAs](https://azure.microsoft.com/solutions/network-appliances/). Annars kan problem och underhållshändelser med NVA ha en negativ inverkan på stabiliteten och tillförlitligheten i den övergripande klusterkonfigurationen. För att undvika sådana hinder ska du inte definiera routningsregler för NVA:er eller [användardefinierade routningsregler](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) som dirigerar trafik mellan klustrade noder och SBD-enheter via NVA:er och liknande enheter när du planerar och distribuerar klustrade noder och SBD-enheter från Linux Pacemaker. 
>

## <a name="sbd-fencing"></a>SBD stängsel

Följ dessa steg om du vill använda en SBD-enhet för stängsel.

### <a name="set-up-iscsi-target-servers"></a>Konfigurera iSCSI-målservrar

Du måste först skapa virtuella iSCSI-måldatorer. iSCSI-målservrar kan delas med flera Pacemaker-kluster.

1. Distribuera nya SLES 12 SP1 eller högre virtuella datorer och anslut till dem via ssh. Maskinerna behöver inte vara stora. En storlek på den virtuella datorn som Standard_E2s_v3 eller Standard_D2s_v3 är tillräcklig. Se till att använda Premium-lagring os-disken.

Kör följande kommandon på alla **virtuella iSCSI-måldatorer**.

1. Uppdatera SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Du kan behöva starta om operativsystemet när du har uppgraderat eller uppdaterat operativsystemet. 

1. Ta bort paket

   Om du vill undvika ett känt problem med targetcli och SLES 12 SP3 avinstallerar du följande paket. Du kan ignorera fel om paket som inte kan hittas

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Installera iSCSI-målpaket

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Aktivera iSCSI-måltjänsten

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Skapa iSCSI-enhet på iSCSI-målserver

Kör följande kommandon på alla **virtuella iSCSI-måldatorer** för att skapa iSCSI-diskarna för de kluster som används av DINA SAP-system. I följande exempel skapas SBD-enheter för flera kluster. Den visar hur du skulle använda en iSCSI-målserver för flera kluster. SBD-enheterna placeras på OS-disken. Se till att du har tillräckligt med utrymme.

**`nfs`** används för att identifiera NFS-klustret, **ascsnw1** används för att identifiera ASCS-klustret av **NW1**, **dbnw1** används för att identifiera databasklustret för **NW1,** **nfs-0** och **nfs-1** är värdnamn för NFS-klusternoderna. **nw1-xscs-0** och **nw1-xscs-1** är värdnamnen för **NW1** ASCS-klusternoderna och **nw1-db-0** och **nw1-db-1** är värdnamn för databasklusternoderna. Ersätt dem med värdnamnen för klusternoderna och SID för DITT SAP-system.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Du kan kontrollera om allt har ställts in korrekt med

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Konfigurera SBD-enhet

Anslut till iSCSI-enheten som skapades i det sista steget från klustret.
Kör följande kommandon på noderna i det nya kluster som du vill skapa.
Följande objekt föregås av antingen **[A]** - som gäller för alla noder, **[1]** - endast gäller för nod 1 eller **[2]** - endast gäller för nod 2.

1. **[A]** Anslut till iSCSI-enheterna

   Aktivera först iSCSI- och SBD-tjänsterna.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Ändra initierarnamnet på den första noden

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen så att det matchar de ACL:er som du använde när du skapade iSCSI-enheten på iSCSI-målservern, till exempel för NFS-servern.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Ändra initierarnamnet på den andra noden

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen så att det matchar de åtkomstkontrollistor som du använde när du skapade iSCSI-enheten på iSCSI-målservern

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Starta om iSCSI-tjänsten

   Starta nu om iSCSI-tjänsten för att tillämpa ändringen

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Anslut iSCSI-enheterna. I exemplet nedan är 10.0.0.17 IP-adressen för iSCSI-målservern och 3260 är standardporten. <b>iqn.2006-04.nfs.local:nfs</b> är ett av målnamnen som visas när du kör det första kommandot nedan (iscsiadm -m-identifiering).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Kontrollera att iSCSI-enheterna är tillgängliga och anteckna enhetsnamnet (i följande exempel /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Nu, hämta ID:n för iSCSI-enheter.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Kommandot listar tre enhets-ID:er för varje SBD-enhet. Vi rekommenderar att du använder ID som börjar med scsi-3, i exemplet ovan är detta

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Skapa SBD-enheten

   Använd enhets-ID:t för iSCSI-enheterna för att skapa de nya SBD-enheterna på den första klusternoden.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Anpassa SBD-konfigen

   Öppna SBD-config-filen

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Ändra egenskapen för SBD-enheten, aktivera pacemakerintegrationen och ändra startläget för SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Skapa `softdog` konfigurationsfilen

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Nu ladda modulen

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Klusterinstallation

Följande objekt föregås av antingen **[A]** - som gäller för alla noder, **[1]** - endast gäller för nod 1 eller **[2]** - endast gäller för nod 2.

1. **[A]** Uppdatera SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Installera komponent, som behövs för klusterresurser

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Installera azure-lb-komponent, som behövs för klusterresurser

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Kontrollera versionen av paketets resursagenter och se till att minimikraven för den minsta versionen är uppfyllda:  
   > - För SLES 12 SP4/SP5 måste versionen vara minst resursagenter-4.3.018.a7fb5035-3.30.1.  
   > - För SLES 15/15 SP1 måste versionen vara minst resursagenter-4.3.0184.6ee15eb2-4.13.1.  

1. **[A]** Konfigurera operativsystemet

   I vissa fall skapar Pacemaker många processer och uttömmer därmed det tillåtna antalet processer. I så fall kan ett hjärtslag mellan klusternoderna misslyckas och leda till redundans för dina resurser. Vi rekommenderar att du ökar de högsta tillåtna processerna genom att ange följande parameter.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Minska storleken på den smutsiga cachen. Mer information finns i [Låg skrivprestanda på SLES 11/12-servrar med stort RAM-minne](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Konfigurera cloud-netconfig-azure för HA Cluster

   Ändra konfigurationsfilen för nätverksgränssnittet enligt nedan för att förhindra att plugin-programmet för molnnätverket tar bort den virtuella IP-adressen (Pacemaker måste styra VIP-tilldelningen). Mer information finns i [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Aktivera ssh-åtkomst

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Aktivera ssh-åtkomst

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Aktivera ssh-åtkomst

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Installera stängselagenter
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Om du använder Suse Linux Enterprise Server för SAP 15 bör du tänka på att du måste aktivera ytterligare modul och installera ytterligare komponenter, vilket är en förutsättning för att använda Azure Fence Agent. Mer information om SUSE-moduler och tillägg finns [i Moduler och tillägg förklaras](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Följ instruktionerna nedan för att installera Azure Python SDK. 

   Följande instruktioner om hur du installerar Azure Python SDK gäller endast för Suse Enterprise Server för SAP **15**.  

    - Om du använder Bring-Your-Own-Subscription följer du dessa instruktioner  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Om du använder prenumerationen för användningsbaserad betalning följer du dessa instruktioner  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** Namnmatchning för installationsprogrammet

   Du kan antingen använda en DNS-server eller ändra /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon. Fördelen med att använda /etc/hosts är att klustret blir oberoende av DNS, vilket kan vara en enda felpunkt också.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader till /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Installera kluster

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Lägg till nod i klustret

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Ändra hacluster-lösenord till samma lösenord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Justera corosync-inställningarna.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Lägg till följande fetstilade innehåll i filen om värdena inte finns eller är annorlunda. Se till att ändra token till 30000 så att minne bevarar underhåll. Mer information finns i [den här artikeln för Linux][virtual-machines-linux-maintenance] eller [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Starta sedan om corosync-tjänsten

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Skapa Azure Fence agent STONITH-enhet

STONITH-enheten använder en tjänstansvarig för att auktorisera mot Microsoft Azure. Följ dessa steg för att skapa en tjänsthuvudnamn.

1. Gå till <https://portal.azure.com>
1. Öppna Azure Active Directory-bladet  
   Gå till Egenskaper och skriv ned katalog-ID. Det här är **klient-ID**.
1. Klicka på Appregistreringar
1. Klicka på Ny registrering
1. Ange ett namn, välj "Konton i den här organisationskatalogen" 
2. Välj Programtyp "Webb", ange en inloggnings-URL\/(till exempel http: /localhost) och klicka på Lägg till  
   Inloggnings-URL:en används inte och kan vara en giltig WEBBADRESS
1. Välj Certifikat och hemligheter och klicka sedan på Ny klienthemlighet
1. Ange en beskrivning för en ny nyckel, välj "Aldrig upphör att gälla" och klicka på Lägg till
1. Skriv ned värdet. Den används som **lösenord** för servicehuvudmannen
1. Välj Översikt. Skriv ner program-ID: t. Det används som användarnamn **(inloggnings-ID** i stegen nedan) av Service Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Skapa en anpassad roll för stängslet agent

Tjänsthuvudmannen har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvudnamn behörighet att starta och stoppa (deallocate) alla virtuella datorer i klustret. Om du inte redan har skapat den anpassade rollen kan du skapa den med [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) eller [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli)

Använd följande innehåll för indatafilen. Du måste anpassa innehållet till dina prenumerationer som är, ersätta c276fc76-9cd4-44c9-99a7-4fd71546436e och e91d47c4-76f3-4271-a796-21b4ecfe3624 med IDs av din prenumeration. Om du bara har en prenumeration tar du bort den andra posten i AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action", 
    "Microsoft.Compute/virtualMachines/powerOff/action" 
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Tilldela den anpassade rollen till tjänsthuvudgivaren

Tilldela den anpassade rollen "Linux Fence Agent Role" som skapades i det sista kapitlet till tjänsthuvudstadshuvudmannen. Använd inte ägarrollen längre!

1. Gå till[https://portal.azure.com](https://portal.azure.com)
1. Öppna bladet Alla resurser
1. Välj den virtuella datorn för den första klusternoden
1. Klicka på Åtkomstkontroll (IAM)
1. Klicka på Lägg till rolltilldelning
1. Välj rollen "Linux Fence Agent Role"
1. Ange namnet på programmet som du skapade ovan
1. Klicka på Spara

Upprepa stegen ovan för den andra klusternoden.

### <a name="1-create-the-stonith-devices"></a>**[1]** Skapa STONITH-enheterna

När du har redigerat behörigheterna för de virtuella datorerna kan du konfigurera STONITH-enheterna i klustret.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Standard pacemakerkonfiguration för SBD

1. **[1]** Aktivera användningen av en STONITH-enhet och ställ in stängslets fördröjning

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemaker-konfiguration för azure-schemalagda händelser

Azure erbjuder [schemalagda händelser](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Schemalagda händelser tillhandahålls via metadatatjänsten och ger tid för programmet att förbereda sig för händelser som vm-avstängning, omdistribution av virtuella datorer osv. Azure-events för **[resursagenten](https://github.com/ClusterLabs/resource-agents/pull/1161)** för schemalagda Azure-händelser. Om händelser identifieras försöker agenten stoppa alla resurser på den påverkade virtuella datorn och flytta dem till en annan nod i klustret. För att uppnå att ytterligare Pacemaker-resurser måste konfigureras. 

1. **[A]** Kontrollera att paketet för **azure-events-agenten** redan är installerat och uppdaterat. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Konfigurera resurserna i Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > När du har konfigurerat Pacemaker-resurserna för azure-events-agenten kan du få varningsmeddelanden som:  
     VARNING: cib-bootstrap-alternativ: okänt attribut "hostName_ <strong>värdnamn"</strong>  
     VARNING: cib-bootstrap-alternativ: okänt attribut "azure-events_globalPullState"  
     VARNING: cib-bootstrap-alternativ: okänt attribut "hostName_ <strong>värdnamn"</strong>  
   > Dessa varningsmeddelanden kan ignoreras.

## <a name="next-steps"></a>Nästa steg

* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][sles-nfs-guide]
* [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program][sles-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (virtuella datorer)][sap-hana-ha]
