---
title: Tyst installation av Azure Backup Server V2
description: Använd ett PowerShell-skript för att tyst installera Azure Backup Server V2. Denna typ av installation kallas också en obevakad installation.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172248"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Köra en obevakad installation av Azure Backup Server

Lär dig hur du kör en obevakad installation av Azure Backup Server.

De här stegen gäller inte om du installerar Azure Backup Server V1.

## <a name="install-backup-server"></a>Installera säkerhetskopieringsserver

1. Skapa en textfil på servern som är värd för Azure Backup Server V2 eller senare. (Du kan skapa filen i Anteckningar eller i en annan textredigerare.) Spara filen som MABSSetup.ini.

2. Klistra in följande kod i filen MABSSetup.ini. Ersätt texten inuti hakparenteserna (\< \>) med värden från din miljö. Följande text är ett exempel:

   ```text
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

3. Spara filen. Ange sedan det här kommandot vid en upphöjd kommandotolk på installationsservern:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Du kan använda dessa flaggor för installationen:</br>
**/f**: .ini-filsökväg</br>
**/l**: Loggsökväg</br>
**/i**: Installationssökväg</br>
**/x**: Avinstallationssökväg</br>

## <a name="next-steps"></a>Nästa steg

När du har installerat Backup Server kan du läsa om hur du förbereder servern eller börjar skydda en arbetsbelastning.

- [Förbereda arbetsbelastningar för säkerhetskopieringsserver](backup-azure-microsoft-azure-backup.md)
- [Använda Säkerhetskopieringsserver för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md)
- [Använda Säkerhetskopieringsserver för säkerhetskopiering av SQL Server](backup-azure-sql-mabs.md)
- [Lägga till modern säkerhetskopieringslagring i Säkerhetskopieringsserver](backup-mabs-add-storage.md)
