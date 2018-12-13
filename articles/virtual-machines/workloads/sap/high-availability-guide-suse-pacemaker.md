---
title: Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure | Microsoft Docs
description: Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: c6dee6fc26f540ad93f5a4b4e6e2f9432f757a6c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076363"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Det finns två alternativ för att konfigurera ett Pacemaker kluster i Azure. Du kan antingen använda en hägna in-agent som tar hand om att starta om noden via Azure-API: er eller du kan använda en uppstår-enhet.

Uppstår enheten kräver minst en ytterligare virtuell dator som fungerar som en iSCSI-målservern och tillhandahåller en uppstår. Dessa iSCSI-målservrar kan dock delas med andra Pacemaker-kluster. Fördelen med att använda en uppstår-enhet är en snabbare failover-tid och, om du använder uppstår enheter lokalt, kräver inte några ändringar på hur du använder pacemaker klustret. Du kan använda upp till tre uppstår enheter i ett kluster med Pacemaker för att tillåta en uppstår enhet blir otillgänglig, till exempel under OS-korrigering av iSCSI-målservern. Om du vill använda flera uppstår enheter per Pacemaker kontrollerar du att distribuera flera iSCSI-målservrar och ansluta en uppstår från varje iSCSI-målservern. Vi rekommenderar att du använder en uppstår enhet eller tre. Pacemaker kommer inte att kunna fence en nod i klustret automatiskt om du bara konfigurera två uppstår enheter och en av dem är inte tillgänglig. Om du vill kunna avgränsningstecken när en iSCSI-målservern är nere måste du använda tre uppstår enheter och därför tre iSCSI-målservrar.

Om du inte vill investera i en ytterligare virtuell dator, kan du också använda Azure avgränsningstecken agenten. Nackdelen är att en redundansväxling kan ta mellan 10 till 15 minuter om det inte går att stoppa en resurs eller noderna i klustret inte kan kommunicera som varandra längre.

![Pacemaker på SLES-översikt](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> När du planerar och distribuerar Linux Pacemaker klustrade noder och uppstår enheter, är det viktigt för den fullständiga klusterkonfigurationen som routning mellan de virtuella datorerna som ingår och de virtuella datorer som är värd för uppstår enhet(er) passerar inte genom övergripande tillförlitlighet alla andra enheter som [Nva](https://azure.microsoft.com/solutions/network-appliances/). I annat fall kan problem och underhållshändelser med NVA ha en negativ inverkan på stabilitet och tillförlitlighet för övergripande klusterkonfigurationen. För att undvika sådana hinder inte definiera regler för routning med nva: er eller [användaren definierats routningsregler](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) att vidarebefordra trafik mellan klustrade noder och uppstår enheter via nva: er och liknande enheter när du planerar och distribuerar Linux Pacemaker klustrade noder och uppstår enheter. 
>

## <a name="sbd-fencing"></a>Att hägna in uppstår

Följ dessa steg om du vill använda en uppstår enhet för att hägna in.

### <a name="set-up-iscsi-target-servers"></a>Konfigurera iSCSI-målservrar

Du måste först skapa iSCSI target virtuella datorer. iSCSI-målservrar kan delas med flera Pacemaker kluster.

1. Distribuera nya SLES 12 SP1 eller virtuella datorer med högre och ansluta till dem via ssh. Datorerna behöver inte vara stora. En VM-storlek som Standard_E2s_v3 eller Standard_D2s_v3 räcker. Se till att använda Premium storage OS-disken.

Kör följande kommandon på alla **iSCSI virtuella måldatorer**.

1. Uppdatera SLES

   <pre><code>sudo zypper update
   </code></pre>

1. Ta bort paket

   Avinstallera följande paket för att undvika ett känt problem med targetcli och SLES 12 SP3. Du kan ignorera fel om paket som inte kan hittas

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Installera iSCSI target-paket

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Aktivera iSCSI-Måltjänsten

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Skapa iSCSI-enhet på iSCSI-målserver

Kör följande kommandon på alla **iSCSI virtuella måldatorer** att skapa iSCSI-diskar för kluster som används av dina SAP-system. I följande exempel skapas uppstår enheter för flera kluster. Den visar hur du använder en iSCSI-målservern för flera kluster. Enheterna som uppstår placeras på OS-disken. Se till att du har tillräckligt med utrymme.

**NFS** används för att identifiera NFS-klustret **ascsnw1** används för att identifiera ASCS-kluster med **NW1**, **dbnw1** används för att identifiera databasen kluster **NW1**, **nfs-0** och **nfs-1** är värdnamnen för klusternoderna NFS **nw1-xscs-0** och  **nw1-xscs-1** är värdnamnen för den **NW1** ASCS klusternoder och **nw1-db-0** och **nw1-db-1** är värdnamnen för databasen klusternoder. Ersätt dem med värdnamnen för klusternoderna och SID för SAP-system.

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

Du kan kontrollera om allt är korrekt konfigurerad med

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

### <a name="set-up-sbd-device"></a>Konfigurera uppstår enhet

Ansluta till iSCSI-enhet som har skapats i det sista steget från klustret.
Kör följande kommandon på noderna i det nya klustret som du vill skapa.
Följande objekt har prefixet antingen **[A]** – gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Ansluta till iSCSI-enheter

   Först aktivera de iSCSI och tjänster som uppstår.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Ändra initieraren namnet på den första noden

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen för att matcha ACL: er som du använde när du skapar iSCSI-enhet på iSCSI-målservern, till exempel för NFS-servern.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Ändra namnet på den andra noden på initieraren

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen för att matcha ACL: er som du använde när du skapar iSCSI-enhet på iSCSI-målserver

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]**  Starta om tjänsten iSCSI

   Nu startar du om iSCSI-tjänsten för att tillämpa ändringen

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Anslut iSCSI-enheter. I exemplet nedan 10.0.0.17 är IP-adressen för iSCSI-målservern och 3260 är standardporten. <b>iqn.2006 04.nfs.local:nfs</b> är en av de mål som anges när du kör det första kommandot nedan (iscsiadm -m discovery).

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

   Kontrollera att iSCSI-enheter är tillgängliga och anteckna enhetsnamn (i följande exempel/dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Nu kan hämta ID: N för iSCSI-enheter.

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

   Kommandot listar tre enhets-ID för varje enhet uppstår. Vi rekommenderar att det är att använda det ID som börjar med scsi-3 i exemplet ovan detta

   * **/dev/disk/by-ID/SCSI-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-ID/SCSI-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]**  Skapa uppstår-enhet

   Använda enhets-ID för iSCSI-enheter för att skapa de nya enheterna uppstår på den första noden i klustret.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]**  Anpassa uppstår config

   Öppna konfigurationsfilen uppstår

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Ändra egenskapen för enheten uppstår, aktivera pacemaker-integrering och ändra startläget för uppstår.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Skapa konfigurationsfilen softdog

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Läsa in modulen nu

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Klusterinstallationen

Följande objekt har prefixet antingen **[A]** – gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Uppdatera SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]**  Konfigurera operativsystem

   I vissa fall kan Pacemaker skapar många processer och använt all därmed det tillåtna antalet processer. I detta fall är kan ett pulsslag mellan noder i klustret misslyckas och leda till redundans för dina resurser. Vi rekommenderar att öka de högsta tillåtna processerna genom att ange följande parameter.

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

   Minska storleken på den informationen i cachen. Mer information finns i [låg skrivprestanda på SLES 11/12 servrar med stora RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]**  Aktivera ssh-åtkomst

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Installera avgränsningstecken agenter
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. **[A]**  Konfigurera matcha värdnamn

   Du kan använda en DNS-server, eller så kan du ändra i/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon. Fördelen med att använda/etc/hosts är att klustret blir oberoende av DNS, vilket kan vara en enda åtkomstpunkt för fel för.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet till matchar din miljö   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Installera kluster

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]**  Lägg till noden till klustret

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]**  Ändra hacluster lösenord till samma lösenord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]**  Konfigurera corosync om du vill använda andra transport och lägga till nodelist. Klustret fungerar inte annars.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Lägg till följande fetstil innehåll i filen om värdena inte är det eller en annan. Se till att ändra token till 30000 att tillåta minne bevarande underhåll. Mer information finns i [i den här artikeln för Linux] [ virtual-machines-linux-maintenance] eller [Windows][virtual-machines-windows-maintenance]. Se också till att ta bort parametern-mcastaddr.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
     # remove parameter mcastaddr
     <b># mcastaddr: IP</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
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

   Starta om tjänsten corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Skapa Azure avgränsningstecken agenten STONITH enhet

STONITH enheten använder ett huvudnamn för tjänsten för att godkänna mot Microsoft Azure. Följ dessa steg om du vill skapa ett huvudnamn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaper och anteckna Directory-ID. Det här är den **klient-ID**.
1. Klicka på App-registreringar
1. Klicka på Lägg till
1. Ange ett namn, väljer typen ”Web app/API”, ange en inloggnings-URL (till exempel http://localhost) och klicka på Skapa
1. Inloggnings-URL: en används inte och kan vara vilken giltig URL
1. Välj den nya appen och klicka på nycklar på fliken Inställningar
1. Ange en beskrivning för en ny nyckel, Välj ”upphör aldrig att gälla” och klicka på Spara
1. Anteckna värdet. Den används som den **lösenord** för tjänstens huvudnamn
1. Anteckna programmets ID. Den används som användarnamnet (**inloggnings-ID** i stegen nedan) för tjänstens huvudnamn

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Skapa en anpassad roll för agenten avgränsningstecken

Tjänstens huvudnamn har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvudnamn behörigheter att starta och stoppa (frigöra) alla virtuella datorer i klustret. Om du inte redan har skapat den anpassade rollen, kan du skapa den med hjälp av [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) eller [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)

Använd följande innehåll för indatafilen. Du måste anpassa innehåll till dina prenumerationer som är, Ersätt c276fc76-9cd4-44c9-99a7-4fd71546436e och e91d47c4-76f3-4271-a796-21b4ecfe3624 med ID: N för din prenumeration. Om du bara har en prenumeration kan du ta bort den andra posten i AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Och tilldela den anpassade rollen till tjänstens huvudnamn

Tilldela den anpassade rollen ”Linux avgränsningstecken agenten roll” som har skapats i det senaste kapitlet till tjänstens huvudnamn. Använd inte ägarrollen längre!

1. Gå till https://portal.azure.com
1. Öppna bladet alla resurser
1. Välj den virtuella datorn från den första noden i klustret
1. Klicka på åtkomstkontroll (IAM)
1. Klicka på Lägg till rolltilldelning
1. Välj roll ”Linux avgränsningstecken agenten roll”
1. Ange namnet på programmet som du skapade ovan
1. Klicka på Spara

Upprepa stegen ovan för den andra noden i klustret.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Skapa STONITH-enheter

När du har redigerat behörigheterna för de virtuella datorerna kan du konfigurera STONITH-enheter i klustret.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Pacemaker standardkonfigurationen för uppstår

1. **[1]**  Aktivera användning av en STONITH enhet och ange fördröjning av avgränsningstecken

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

## <a name="next-steps"></a>Nästa steg

* [Azure virtuella datorer, planering och implementering av SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][sles-nfs-guide]
* [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program][sles-guide]
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure Virtual Machines i [hög tillgänglighet för SAP HANA på Azure Virtual Machines (VM)][sap-hana-ha]
