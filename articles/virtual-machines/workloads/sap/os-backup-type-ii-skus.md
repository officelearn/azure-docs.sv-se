---
title: 'Operativsystemet säkerhetskopiering och återställning av SAP HANA på Azure (stora instanser) skriver du II-SKU: er | Microsoft Docs'
description: 'Utföra Operatign system säkerhetskopiering och återställning för SAP HANA på Azure (stora instanser) typ II-SKU: er'
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
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436348"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>OS-säkerhetskopiering och återställning för typ II-SKU: er

Det här dokumentet beskriver steg för att utföra en operativsystemet säkerhetskopiering på filnivå och återställning av den **typ II-SKU: er** för HANA stora instanser. 

>[!NOTE]
>De säkerhetskopiera OS-skript använder bakre programvara, vilket är förinstallerad i servern.  

När etableringen har slutförts av Microsoft Service Management-teamet, som standard servern är konfigurerad med två säkerhetskopieringar schema för säkerhetskopiering av filsystemet på Säkerhetskopiera av operativsystemet. Du kan kontrollera schemat för jobbet med hjälp av följande kommando:
```
#crontab –l
```
Du kan ändra schemat för säkerhetskopiering när som helst med hjälp av följande kommando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hur du utför en manuell säkerhetskopiering?

Operativsystemet filsystemets säkerhetskopia har schemalagts med hjälp av en **cron-jobb** redan. Du kan dock utföra operativsystemet på säkerhetskopian samt manuellt. Om du vill utföra en manuell säkerhetskopiering, kör du följande kommando:

```
#rear -v mkbackup
```
Följande skärm visar visar exempel manuell säkerhetskopiering:

![Så här](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Hur du återställer en säkerhetskopia?

Du kan återställa en fullständig säkerhetskopia eller en enskild fil från säkerhetskopian. Om du vill återställa, använder du följande kommando:

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

Relax och Återställ (baksida)-paket är **förinstallerade** i den **typ II-SKU: er** av stora HANA-instanser och ingen åtgärd behövs från dig. Du kan direkt börja använda bakom för säkerhetskopiering för operativsystemet.
I de fall där du behöver installera paket i din egen, kan du följa de listade stegen för att installera och konfigurera verktyget bakre.

Installera den **bakre** säkerhetskopiera paket, använder du följande kommandon:

För **SLES** operativsystem, använder du följande kommando:
```
#zypper install <rear rpm package>
```
För **RHEL** operativsystem, använder du följande kommando: 
```
#yum install rear -y
```
Om du vill konfigurera bakre verktyget måste du uppdatera parametrar **OUTPUT_URL** och **BACKUP_URL** i den *filen /etc/rear/local.conf*.
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
