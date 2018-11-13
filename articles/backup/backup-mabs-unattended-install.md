---
title: Tyst installation av Azure Backup Server V2
description: Använda ett PowerShell-skript för obevakad installation Azure Backup Server V2. Den här typen av installation är en förkortning av en obevakad installation.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: markgal
ms.openlocfilehash: 3e106d7f669cf14014114ed0fe63651a1a2fe0eb
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578126"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Kör en obevakad installation av Azure Backup Server

Lär dig hur du kör en obevakad installation av Azure Backup Server.

De här stegen gäller inte om du installerar Azure Backup Server V1.

## <a name="install-backup-server"></a>Installera säkerhetskopieringsserver

1. På den server som är värd för Azure Backup Server V2 eller senare, skapa en textfil. (Du kan skapa filen i anteckningar eller något annat textredigeringsprogram.) Spara filen som MABSSetup.ini.

2. Klistra in följande kod i filen MABSSetup.ini. Ersätt texten inom klamrarna (\< \>) med värden från din miljö. Följande text är ett exempel:

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Spara filen. Vid en upphöjd kommandotolk på installationsservern, skriver du följande kommando:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Du kan använda dessa flaggor för installationen:</br>
**/f**: ini-filsökväg</br>
**/l**: loggsökväg</br>
**/i**: installationssökvägen</br>
**/x**: avinstallera sökväg</br>

## <a name="next-steps"></a>Nästa steg
När du har installerat Backup Server lär du dig hur du förbereder din server eller börjar skydda en arbetsbelastning.

- [Förbereda Backup Server-arbetsbelastningar](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-server med hjälp av Backup Server](backup-azure-backup-server-vmware.md)
- [Säkerhetskopiera SQL Server med hjälp av Backup Server](backup-azure-sql-mabs.md)
- [Lägga till Modern Backup Storage till säkerhetskopieringsserver](backup-mabs-add-storage.md)
