---
title: Remote Desktop Services inte startar på en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker problem med Remote Desktop Services när du ansluter till en virtuell dator | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989250"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Remote Desktop Services startar inte på en Azure VM

Den här artikeln beskriver hur du felsöker problem med att ansluta till en Azure virtuell dator (VM) när Remote Desktop Services (TermService) inte är startar eller misslyckas med att starta.

>[!NOTE]
>Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder Resource Manager-distributionsmodellen. Vi rekommenderar att du använder den här modellen för nya distributioner istället för att använda den klassiska distributionsmodellen.

## <a name="symptoms"></a>Symtom

När du försöker ansluta till en virtuell dator händer följande scenarier:

- VM-skärmbilden visar operativsystemet är fullständigt inläst och väntar på autentiseringsuppgifter.
- Alla program på den virtuella datorn är fungerar som förväntat och kan nås.
- Den virtuella datorn svarar på TCP-anslutningen i Microsoft Remote Desktop Protocol (RDP)-porten (standard: 3389).
- Du inte ange några autentiseringsuppgifter när du försöker göra en RDP-anslutning.

## <a name="cause"></a>Orsak

Det här problemet uppstår eftersom Remote Desktop Services inte körs på den virtuella datorn. Orsaken kan vara beroende av följande scenarier:

- TermService-tjänsten har ställts in **inaktiverad**.
- TermService tjänsten kraschar eller hängande.

## <a name="solution"></a>Lösning

Använd någon av följande lösningar för att lösa problemet, eller försök lösningar i taget:

### <a name="solution-1-using-the-serial-console"></a>Lösning 1: Använda Seriekonsolen

1. Åtkomst till den [Seriekonsolen](serial-console-windows.md) genom att välja **Support och felsökning** > **seriekonsol (förhandsversion)**. Om funktionen är aktiverad på den virtuella datorn, kan du ansluta den virtuella datorn.

2. Skapa en ny kanal för en CMD-instans. Typ **CMD** att starta kanalen för att få dess namn.

3. Växla till kanalen som körs CMD-instans, i det här fallet det bör vara kanal 1.

   ```
   ch -si 1
   ```

4. Tryck på **RETUR** igen och ange ett giltigt användarnamn och lösenord (lokal eller domänåtkomst-ID) för den virtuella datorn.

5. Fråga status för tjänsten TermService.

   ```
   sc query TermService
   ```

6. Om status för tjänsten visar **stoppad**, försök att starta tjänsten.

   ```
   sc start TermService
   ```

7. Skicka frågor till tjänsten igen för att se till att tjänsten har startats.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>Lösning 2: Använda en virtuell dator för återställning för att aktivera tjänsten

[Säkerhetskopiera OS-disken](../windows/snapshot-copy-managed-disk.md) och [koppla OS-disken till en undsättning VM](../windows/troubleshoot-recovery-disks-portal.md). Öppna en upphöjd CMD-instans och kör följande skript på Räddade VM:

>[!NOTE]
>Vi förutsätter att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Ersätt den med lämpligt värde i den virtuella datorn. När detta är gjort kan du koppla bort disken från den Virtuella återställningsdatorn och [återskapa den virtuella datorn](../windows/create-vm-specialized.md). För ytterligare felsökning kan du använda **lösning 1** eftersom Seriekonsolen har aktiverats.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
