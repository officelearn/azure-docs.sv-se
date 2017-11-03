---
title: Tyst installation av Azure Backup Server v2 | Microsoft Docs
description: "Använda ett PowerShell-skript för att installera Azure Backup Server v2. Den här typen av installation kallas även en obevakad installation."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Kör en obevakad installation av Azure Backup Server v2

Lär dig hur du kör en obevakad installation av Azure Backup Server v2. 

De här stegen gäller inte om du installerar Azure Backup Server v1.

## <a name="install-backup-server-v2"></a>Installera reservserver v2

1. Skapa en textfil på den server som är värd för Azure Backup Server v2. (Du kan skapa filen i anteckningar eller något annat textredigeringsprogram.) Spara filen som MABSSetup.ini. 

2. Klistra in följande kod i filen MABSSetup.ini. Ersätt texten inom hakparentes (\< \>) med värden från din miljö. Följande är ett exempel:

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
**/l**: loggsökvägen</br>
**/i**: installationssökväg</br>
**/x**: avinstallera sökväg</br>

## <a name="next-steps"></a>Nästa steg
När du har installerat Backup Server lär du dig hur du förbereder din server eller börja skydda en arbetsbelastning.

- [Förbereda säkerhetskopiering serverarbetsbelastningar](backup-azure-microsoft-azure-backup.md)
- [Använd Backup Server för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md)
- [Använd Backup Server för att säkerhetskopiera SQL Server](backup-azure-sql-mabs.md)
- [Lägg till moderna Säkerhetskopieringslagring Backup-Server](backup-mabs-add-storage.md)
