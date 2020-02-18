---
title: Uppgradering av operativ system för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Utför uppgradering av operativ system för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0275d30f2314073af07eced224ade47c49e580c1
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425384"
---
# <a name="operating-system-upgrade"></a>Uppgradering av operativ system
I det här dokumentet beskrivs information om uppgraderingar av operativ system på de stora och HANA-instanserna.

>[!NOTE]
>Operativ system uppgraderingen är kundens ansvar, Microsoft Operations-support kan hjälpa dig med viktiga områden för att se under uppgraderingen. Du bör kontakta operativ system leverantören och innan du planerar att uppgradera.

Under HLI enhets etablering installerar Microsoft Operations-teamet operativ systemet.
Under tiden måste du underhålla operativ systemet (exempel: korrigering, justering, uppgradering osv.) på HLI-enheten.

Innan du gör större ändringar i operativ systemet (till exempel uppgradera SP1 till SP2) måste du kontakta Microsoft Operations team genom att öppna ett support ärende för att konsultera.

Ta med i din biljett:

* Ditt HLI-prenumerations-ID.
* Ditt Server namn.
* Korrigerings nivån som du planerar att tillämpa.
* Det datum då du planerar den här ändringen. 

Vi rekommenderar att du öppnar den här biljetten minst en vecka före det önskvärda uppgraderings datumet på grund av att drifts teamet kontrollerar om en uppgradering av inbyggd program vara kommer att vara nödvändig på Server bladet.


En support mat ris för olika SAP HANA versioner med olika Linux-versioner finns i [SAP Obs! #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Kända problem

Följande är några vanliga kända problem under uppgraderingen:
- I SKU Type II klass-SKU: n för program varu Foundation (SFS) tas den bort efter uppgraderingen av operativ systemet. Du måste installera om den kompatibla SFS efter uppgraderingen av operativ systemet.
- Ethernet-kort driv rutiner (ENIC och FNIC) återställdes till en äldre version. Du måste installera om den kompatibla versionen av driv rutinerna efter uppgraderingen.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA stor instans (typ I)-Rekommenderad konfiguration

Konfiguration av operativ system kan utföras av de rekommenderade inställningarna över tid på grund av korrigeringar, system uppgraderingar och ändringar som gjorts av kunderna. Dessutom identifierar Microsoft uppdateringar som krävs för befintliga system för att säkerställa att de är optimalt konfigurerade för bästa prestanda och återhämtning. Följande anvisningar beskriver rekommendationer som hanterar nätverks prestanda, system stabilitet och optimal HANA-prestanda.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatibla driv rutins versioner för eNIC/fNIC
  För att få korrekt nätverks prestanda och system stabilitet bör du se till att den OS-/regionsspecifika lämpliga versionen av eNIC-och fNIC-drivrutinerna installeras som beskrivs i följande kompatibilitetslista. Servrar levereras till kunder med kompatibla versioner. Observera att i vissa fall under operativ system/kernel-uppdatering kan driv rutiner återställas till standard driv rutins versionerna. Kontrol lera att rätt driv rutins version kör åtgärder för att publicera operativ system/kernel.
       
      
  |  OS-leverantör    |  OS-paketets version     |  eNIC-drivrutin  |  fNIC-drivrutin |
  |---------------|-------------------------|---------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7,2               |   2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Kommandon för driv rutins uppgradering och rensa gamla rpm-paket
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Kommandon som ska bekräftas
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>Uppdaterings problem för SuSE HLIs-GRUB
SAP på Azure HANA stora instanser (typ I) kan vara i ett icke startbart tillstånd efter uppgraderingen. I proceduren nedan åtgärdas det här problemet.
#### <a name="execution-steps"></a>Körnings steg


*   Kör `multipath -ll`-kommandot.
*   Hämta det LUN-ID vars storlek är ungefär 50G eller Använd kommandot: `fdisk -l | grep mapper`
*   Uppdatera `/etc/default/grub_installdevice`-filen med rad `/dev/mapper/<LUN ID>`. Exempel:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN-ID varierar från server till server.


### <a name="disable-edac"></a>Inaktivera EDAC 
   Modulen för fel identifiering och korrigering (EDAC) hjälper till att identifiera och korrigera minnes fel. Den underliggande maskin varan för SAP HANA på stora Azure-instanser (typ I) utför dock redan samma funktion. Att ha samma funktion aktive rad på maskin-och operativ system nivå kan orsaka konflikter och kan leda till tillfälliga, oplanerade avstängningar av servern. Därför rekommenderar vi att du inaktiverar modulen från operativ systemet.

#### <a name="execution-steps"></a>Körnings steg

* Kontrol lera om EDAC-modulen är aktive rad. Om utdata returneras i nedanstående kommando innebär det att modulen är aktive rad. 
```
lsmod | grep -i edac 
```
* Inaktivera modulerna genom att lägga till följande rader i filen `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
En omstart krävs för att göra ändringar på plats. Kör `lsmod` kommando och kontrol lera att modulen inte finns där i utdata.


### <a name="kernel-parameters"></a>Kernel-parametrar
   Kontrol lera att rätt inställning för `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` och `intel_idle.max_cstate` tillämpas.

* intel_idle. max_cstate = 1
* processor. max_cstate = 1
* transparent_hugepage = aldrig
* numa_balancing = inaktivera
* MCE = ignore_ce


#### <a name="execution-steps"></a>Körnings steg

* Lägg till dessa parametrar till `GRB_CMDLINE_LINUX` raden i filen `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Skapa en ny grub-fil.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Starta om systemet.


## <a name="next-steps"></a>Nästa steg
- Hänvisa till [säkerhets kopiering och återställning](hana-overview-high-availability-disaster-recovery.md) av säkerhets kopierings typ I SKU-klass.
- Se [säkerhets kopiering av OS för typ II SKU: er av revision 3-stämplar](os-backup-type-ii-skus.md) för typ II SKU-klass.
