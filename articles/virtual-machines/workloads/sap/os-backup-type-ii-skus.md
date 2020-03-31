---
title: 'Säkerhetskopiering och återställning av SAP HANA på Azure (stora instanser) typ II SKU: er| Microsoft-dokument'
description: Utföra säkerhetskopiering och återställning av operativsystem för SAP HANA på Azure (Stora instanser) Typ II SKU:er
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616870"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Operativsystemet säkerhetskopiering och återställning för typ II SKU:er för Revision 3-frimärken

I det här dokumentet beskrivs stegen för att utföra en säkerhetskopia och återställning på operativsystemfilnivå för **skriv-II-SKU:erna** för DEA Stora instanserna av revision 3. 

>[!Important]
> **Den här artikeln gäller inte för SKU-distributioner av typ II i revision 4 stora förekomststämplar för hana.** Boot LUNS av typ II HANA Stora instansenheter som distribueras i Revision 4 HANA Stora instans stämplar kan säkerhetskopieras med lagring ögonblicksbilder eftersom detta är fallet med typ I SKU:er redan i Revision 3 stämplar


>[!NOTE]
>Operativsystemets säkerhetskopieringsskript använder ReaR-programvaran, som är förinstallerad på servern.  

När etableringen har slutförts `Service Management` av Microsoft-teamet konfigureras som standard servern med två säkerhetskopieringsscheman för att säkerhetskopiera filsystemnivån på baksidan av operativsystemet. Du kan kontrollera schemana för säkerhetskopieringsjobben med hjälp av följande kommando:
```
#crontab –l
```
Du kan ändra schemat för säkerhetskopiering när som helst med följande kommando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hur man tar en manuell backup?

Säkerhetskopian av os-filsystemet är schemalagd med ett **cron-jobb** redan. Du kan dock också säkerhetskopiera operativsystemets filnivå manuellt. Kör följande kommando om du vill utföra en manuell säkerhetskopiering:

```
#rear -v mkbackup
```
På följande skärm visas exempel på manuell säkerhetskopiering:

![Hur](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Hur återställer man en säkerhetskopia?

Du kan återställa en fullständig säkerhetskopia eller en enskild fil från säkerhetskopian. Om du vill återställa använder du följande kommando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Efter återställningen återställs filen i den aktuella arbetskatalogen.

Följande kommando visar återställning av en fil */etc/fstabfrom* *säkerhetskopian.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Du måste kopiera filen till önskad plats när den har återställts från säkerhetskopian.

Följande skärmbild visar återställningen av en fullständig säkerhetskopia:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Hur man installerar ReaR-verktyget och ändra konfigurationen? 

ReaR-paketen (Relax-and-Recover) är **förinstallerade** i **skriv-II-SKU:erna** för STORA HANA-instanser och ingen åtgärd behövs från dig. Du kan direkt börja använda ReaR för säkerhetskopiering av operativsystem.
Under de omständigheter där du behöver installera paketen på egen hand kan du följa de angivna stegen för att installera och konfigurera ReaR-verktyget.

Om du vill installera ReaR-säkerhetskopieringspaketen använder du följande kommandon: **ReaR**

För **SLES-operativsystem** använder du följande kommando:
```
#zypper install <rear rpm package>
```
För **RHEL-operativsystem** använder du följande kommando: 
```
#yum install rear -y
```
Om du vill konfigurera ReaR-verktyget måste du uppdatera parametrar **OUTPUT_URL** och **BACKUP_URL** i *filen /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Följande skärmbild visar återställningen av ![en fullständig säkerhetskopia: RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
