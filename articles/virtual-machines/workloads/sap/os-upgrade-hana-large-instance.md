---
title: Uppgradering av operativsystemet för SAP HANA på Azure (stora instanser)| Microsoft-dokument
description: Utföra uppgradering av operativsystemet för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7fea0f74a90bc7b786a9b302d6282f9fb70e5412
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991491"
---
# <a name="operating-system-upgrade"></a>Uppgradering av operativsystem
I det här dokumentet beskrivs information om uppgraderingar av operativsystemet på de stora HANA-instanserna.

>[!NOTE]
>Os-uppgraderingen är kundens ansvar, Microsofts driftsupport kan guida dig till de viktigaste områdena för att se upp under uppgraderingen. Du bör kontakta din operativsystem leverantör också innan du planerar för en uppgradering.

Under HLI-enhetsetablering installerar Microsofts driftteam operativsystemet.
Med tiden måste du underhålla operativsystemet (Exempel: Korrigering, justering, uppgradering etc.) på HLI-enheten.

Innan du gör större ändringar i operativsystemet (till exempel Uppgradera SP1 till SP2) måste du kontakta Microsoft Operations-teamet genom att öppna en supportbiljett för att konsultera.

Inkludera i din biljett:

* Ditt HLI-prenumerations-ID.
* Ditt servernamn.
* Plåstret nivå du planerar att tillämpa.
* Det datum då du planerar ändringen. 

Vi rekommenderar att du öppnar den här biljetten minst en vecka före det önskvärda uppgraderingsdatumet på grund av att operationsteamet kontrollerar om en uppgradering av den inbyggda programvaran kommer att behövas på serverbladet.


För supportmatrisen för de olika SAP HANA-versionerna med de olika Linux-versionerna finns i [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Kända problem

Följande är de få vanliga kända problem under uppgraderingen:
- På SKU Type II-klass SKU tas programvaran (SFS) bort efter OS-uppgraderingen. Du måste installera om den kompatibla SFS efter OS-uppgraderingen.
- Ethernet-kortdrivrutiner (ENIC och FNIC) återställs till äldre version. Du måste installera om den kompatibla versionen av drivrutinerna efter uppgraderingen.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA Large Instance (typ I) rekommenderad konfiguration

Konfigurationen av operativsystemet kan glida från de rekommenderade inställningarna över tid på grund av korrigering, systemuppgraderingar och ändringar som gjorts av kunder. Dessutom identifierar Microsoft uppdateringar som behövs för befintliga system för att säkerställa att de är optimalt konfigurerade för bästa prestanda och återhämtning. I enlighet med instruktionerna beskrivs rekommendationer som behandlar nätverksprestanda, systemstabilitet och optimal HANA-prestanda.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatibla eNIC/fNIC-drivrutinsversioner
  För att ha rätt nätverksprestanda och systemstabilitet rekommenderas att den OS-specifika lämpliga versionen av eNIC- och fNIC-drivrutiner installeras enligt följande kompatibilitetstabell. Servrar levereras till kunder med kompatibla versioner. Observera att i vissa fall, under OS / Kernel korrigering, kan drivrutiner få återställas till standarddrivrutinen versioner. Kontrollera att lämplig drivrutinsversion körs efter OS/Kernel-korrigeringsåtgärder.
       
      
  |  OS-leverantör    |  Os-paketversion     |  Version av inbyggd programvara  |  eNIC-drivrutin |  fNIC-drivrutin | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   Suse        |  SLES 12 SP4            |   3.2.3b           |  2.3.0.47    |   2.0.0.54   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Kommandon för uppgradering av drivrutiner och för att rengöra gamla rpm-paket
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Kommandon för att bekräfta
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB uppdateringsfel
SAP på Azure HANA Large Instances (Typ I) kan vara i ett icke-startbart tillstånd efter uppgraderingen. Nedanstående procedur löser problemet.
#### <a name="execution-steps"></a>Körningssteg


*   Kör `multipath -ll` kommandot.
*   Hämta LUN-ID vars storlek är cirka 50G eller använd kommandot:`fdisk -l | grep mapper`
*   Uppdatera `/etc/default/grub_installdevice` fil `/dev/mapper/<LUN ID>`med rad . Exempel: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID varierar från server till server.


### <a name="disable-edac"></a>Inaktivera EDAC 
   Modulen Felidentifiering och korrigering (EDAC) hjälper till att upptäcka och korrigera minnesfel. Den underliggande maskinvaran för SAP HANA på Azure Large Instances (Typ I) utför dock redan samma funktion. Med samma funktion aktiverad på maskinvaru- och operativsystem (OS) nivåer kan orsaka konflikter och kan leda till enstaka, oplanerade avstängningar av servern. Därför rekommenderas att inaktivera modulen från operativsystemet.

#### <a name="execution-steps"></a>Körningssteg

* Kontrollera om EDAC-modulen är aktiverad. Om en utdata returneras i kommandot nedan betyder det att modulen är aktiverad. 
```
lsmod | grep -i edac 
```
* Inaktivera modulerna genom att lägga till följande rader i filen`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
En omstart krävs för att göra ändringar på plats. Kör `lsmod` kommandot och kontrollera att modulen inte finns där i utdata.


### <a name="kernel-parameters"></a>Kärnparametrar
   Kontrollera att rätt `transparent_hugepage`inställning `numa_balancing` `processor.max_cstate`för `ignore_ce` `intel_idle.max_cstate` , , och tillämpas.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=aldrig
* numa_balancing=inaktivera
* mce=ignore_ce


#### <a name="execution-steps"></a>Körningssteg

* Lägga till dessa `GRB_CMDLINE_LINUX` parametrar på raden i filen`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Skapa en ny grub-fil.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Starta om systemet.


## <a name="next-steps"></a>Nästa steg
- Refer [Backup and restore](hana-overview-high-availability-disaster-recovery.md) for OS backup Type I SKU class.
- Se [OS Backup för typ II SKU:er för revision 3-stämplar](os-backup-type-ii-skus.md) för klass typ II SKU.
