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
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d5caf5836b96555e01b55d408e51f3df2407d35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657612"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>OS-säkerhetskopiering och återställning för typ II SKU: er

Det här dokumentet beskriver steg för att säkerhetskopiera operativsystemet och återställning av den **typ II SKU: er** av stora HANA-instanser. 

>[!NOTE]
>OS säkerhetskopiering skript använder bakre programvara, vilket är förinstallerad på servern.  

När etableringen har slutförts av Microsoft Service Management-teamet som standard konfigureras servern med två säkerhetskopieringar schema för säkerhetskopiering av det fullständiga operativsystemet. Du kan kontrollera schemat för jobbet med hjälp av följande kommando:
```
#crontab –l
```
Du kan ändra schemat för säkerhetskopiering med hjälp av följande kommando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hur du utför en manuell säkerhetskopiering?

Säkerhetskopia av operativsystemet är schemalagd med hjälp av en **cron jobbet** redan. Dock kan du utföra säkerhetskopieringen operativsystem samt manuellt. Om du vill utföra en manuell säkerhetskopiering, kör du följande kommando:

```
#rear -v mkbackup
```
Följande skärmbild visas visar exempel manuell säkerhetskopiering:

![Hur](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


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
