---
title: Tyst installation av Azure Backup Server v2
description: Använd ett PowerShell-skript för att installera Azure Backup Server v2 obevakat. Den här typen av installation kallas även för en obevakad installation.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 4bce7aeebee729ed253d39720ef520880c261a22
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639658"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Köra en obevakad installation av Azure Backup Server

Lär dig hur du kör en obevakad installation av Azure Backup Server.

De här stegen gäller inte om du installerar Azure Backup Server v1.

## <a name="install-backup-server"></a>Installera säkerhets kopierings Server

1. Skapa en textfil på den server som är värd för Azure Backup Server v2 eller senare. (Du kan skapa filen i anteckningar eller i en annan text redigerare.) Spara filen som MABSSetup. ini.

2. Klistra in följande kod i filen MABSSetup. ini. Ersätt texten innanför hakparenteserna (\< \>) med värden från din miljö. Följande text är ett exempel:

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

3. Spara filen. Ange sedan följande kommando i en upphöjd kommando tolk på installations servern:

   ```
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Du kan använda dessa flaggor för installationen:</br>
**/f**:. ini fil Sök väg</br>
**/l**: Logg Sök väg</br>
**/i**: Installationssökväg</br>
**/x**: Avinstallera sökväg</br>

## <a name="next-steps"></a>Nästa steg
När du har installerat säkerhets kopierings servern lär du dig hur du förbereder servern eller börjar skydda en arbets belastning.

- [Förbered arbets belastningar för säkerhets kopierings Server](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-Server med hjälp av backup server](backup-azure-backup-server-vmware.md)
- [Använd backup server för att säkerhetskopiera SQL Server](backup-azure-sql-mabs.md)
- [Lägg till Modern Backup Storage på säkerhets kopierings Server](backup-mabs-add-storage.md)
