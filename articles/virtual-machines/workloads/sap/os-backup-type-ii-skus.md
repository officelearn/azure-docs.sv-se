---
title: 'Skriv II SKU: er operativsystemet säkerhetskopiering och återställning av SAP HANA i Azure (stora instanser) | Microsoft Docs'
description: 'Utföra Operatign system säkerhetskopiering och återställning för SAP HANA på Azure (stora instanser) typ II SKU: er'
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f01a32612b335003856a372ece15ef300b9d93db
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063282"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>OS-säkerhetskopiering och återställning för typ II SKU: er

Det här dokumentet beskriver steg för att utföra en operativsystemet nivån säkerhetskopiering och återställning av den **typ II SKU: er** av stora HANA-instanser. 

>[!NOTE]
>OS säkerhetskopiering skript använder bakre programvara, vilket är förinstallerad på servern.  

När etableringen har slutförts av Microsoft Service Management-teamet som standard servern har konfigurerats med två säkerhetskopieringar schema för säkerhetskopiering av filsystemet nivå säkerhetskopiera av operativsystemet. Du kan kontrollera schemat för jobbet med hjälp av följande kommando:
```
#crontab –l
```
Du kan ändra schemat för säkerhetskopiering med hjälp av följande kommando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hur du utför en manuell säkerhetskopiering?

Filsystemsäkerhetskopia för operativsystemet är schemalagd med hjälp av en **cron jobbet** redan. Dock kan du utföra operativsystemet nivån säkerhetskopian samt manuellt. Om du vill utföra en manuell säkerhetskopiering, kör du följande kommando:

```
#rear -v mkbackup
```
Följande skärmbild visas visar exempel manuell säkerhetskopiering:

![hur](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Hur du återställer en säkerhetskopia?

Du kan återställa en fullständig säkerhetskopia eller en enskild fil från säkerhetskopian. Använd följande kommando för att återställa:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Efter återställningen återställs filen i den aktuella arbetskatalogen.

Följande kommando visar återställning av en fil */etc/fstabfrom* säkerhetskopian *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Du måste kopiera filen till önskad plats när den har återställts från säkerhetskopian.

Följande skärmbild visar återställning av en fullständig säkerhetskopiering:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Hur du installerar verktyget bakre och ändra konfigurationen? 

Paketen Relax och Återställ (baksida) **förinstallerat** i den **typ II SKU: er** HANA stora instanser och ingen åtgärd behövs från dig. Du kan direkt börja använda baksidan för säkerhetskopiering för operativsystemet.
I de fall där du måste installera paket i ditt eget, kan du följa visas stegen för att installera och konfigurera verktyget bakre.

Så här installerar du den **bakre** säkerhetskopiera paket, Använd följande kommandon:

För **SLES** operativsystem, använder du följande kommando:
```
#zypper install <rear rpm package>
```
För **RHEL** operativsystem, använder du följande kommando: 
```
#yum install rear -y
```
Om du vill konfigurera verktyget bakre, måste du uppdatera parametrar **OUTPUT_URL** och **BACKUP_URL** i den *filen /etc/rear/local.conf*.
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

Följande skärmbild visar återställning av en fullständig säkerhetskopiering: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
