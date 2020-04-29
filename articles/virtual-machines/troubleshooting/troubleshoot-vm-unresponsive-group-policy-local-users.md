---
title: Den virtuella datorn svarar inte vid tillämpning av principen grupprincip lokala användare & grupper
description: Den här artikeln innehåller steg för att lösa problem där load-skärmen fastnar med en princip vid start i en virtuell Azure-dator (VM).
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620862"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Den virtuella datorn svarar inte vid tillämpning av principen grupprincip lokala användare & grupper

Den här artikeln innehåller steg för att lösa problem där load-skärmen fastnar med en princip, under start, i en virtuell Azure-dator (VM).

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att Visa skärm bilden för den virtuella datorn ser du att skärmen fastnar i inläsningen med meddelandet: *tillämpar Grupprincip lokala användare och grupper princip*.

![Alternativ text: skärm som visar hur du använder grupprincip lokala användare och grupper princip inläsningar (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Alternativ text: skärm som visar hur du använder grupprincip lokala användare och grupper princip inläsningar (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Orsak

Symptomen på denna frysning orsakas av ett kod fel i Windows Profile service Dynamic Link Library (*profsvc. dll*).

> [!NOTE]
> Detta fel gäller endast för Windows Server 2012 och Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Principen i fråga

Principen som tillämpas som inte slutför processerna är:

* *Computer Datorkonfiguration\principer\administrativa templates \ system/User Profiles\Delete User profiler som är äldre än ett visst antal dagar vid omstart av systemet*

Den här principen låser sig bara om följande sex villkor är uppfyllda:

* *Ta bort användar profiler som är äldre än ett visst antal dagar efter omstart av datorn* har Aktiver ATS.
* Du har profiler som uppfyller ålders kraven för att kräva rensning.
* Du har komponenter som har registrerats för borttagnings meddelande för profiler.
* Komponenterna gör anrop (direkta eller indirekta) som behöver hämta data från SCM-komponenter (Service Control Manager) i Windows, till exempel starta, stoppa eller fråga efter information om en tjänst.
* Du har en tjänst konfigurerad för *Automatisk*start.
* Den här tjänsten är inställd på att köras under kontexten för ett domän konto (i stället för att använda ett inbyggt konto, till exempel ett lokalt system).

### <a name="the-code-defect"></a>Kod defekt

Felaktig kod är på grund av Service Control Manager (SCM) och profil tjänsterna som försöker tillämpa lås på varandra samtidigt. Det finns lås för att förhindra att flera tjänster gör ändringar på samma data samtidigt, vilket skulle orsaka skada. Vanligt vis orsakar flera lås begär Anden ett problem. Men eftersom detta sker under starten kan ingen av tjänsterna utföra sina processer, eftersom de fastnar i väntan på varandra.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS-bugg 5880648-Service Control Manager-deadlock med principen "ta bort användar profiler vid omstart"

Det finns två åtgärder som överlappar varandra så att:

* Åtgärd 1 hämtar profil låset men har ännu inte skaffat SCM-låset.

  **SÄRSKILT**

* Åtgärd 2 hämtar SCM-låset men har ännu inte förvärvat profil låset.

När detta deadlock uppstår låser det nya låset som krävs åtgärden.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Åtgärd 1-gammal avisering om borttagning av profil (har **profil lås**, kräver **SCM-lås**)

1. Först får principen som tar bort gamla profiler ett internt lås för profil tjänsten.

   * Det här låset kan förhindra att två trådar interagerar med profilerna medan *borttagnings åtgärden* pågår.

2. Principen söker efter profiler som är tillräckligt gamla för att tas bort.
3. Som en del av profil borttagningen försöker en komponent som har registrerat sig för meddelanden om borttagning av en profil att **starta en tjänst**.
4. Innan du startar tjänsten måste Service Control Manager (SCM) Hämta ett **internt SCM-lås** som innehas av trådar i **åtgärd 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Åtgärd 2 – profil belastning/skapa för användarspecifika data (har **SCM-lås**, behöver **profil lås**)

1. Vid start måste SCM beställa alla tjänster för *automatisk start* per grupp, samt alla tjänster som dessa tjänster är beroende av.

2. **SCM hämtar ett internt SCM-lås** som används för att kontrol lera åtkomsten till att starta, stoppa eller konfigurera tjänster när de beställer tjänsterna.

3. När tjänsterna är i ordning, loopar SCM via varje tjänst och startar den.

4. Om tjänsten körs under ett domän kontos kontext måste en profil antingen läsas in eller skapas för domän kontot, så att den kan lagra användarspecifika data.

5. Den här begäran skickas till **profil tjänsten**.

6. Profil tjänsten behöver åtkomst till det **interna lås** som erhållits i **åtgärd 1**.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt

1. Skapa och få åtkomst till en virtuell reparations dator
2. Aktivera serie konsol och samling av minnes dum par
3. Återskapa den virtuella datorn
4. Samla in minnesdumpen

   > [!NOTE]
   > Gäst operativ systemet fungerar inte när det här start felet påträffas. Du ska felsöka i offline-läge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att förbereda en reparations-VM.
2. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Aktivera serie konsol och samling av minnes dum par

Om du vill aktivera samlings-och serie konsolen för minnes dum par kör du skriptet nedan:

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Kör följande kommandon:

   * Aktivera serie konsol:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Kontrol lera att det lediga utrymmet på OS-disken är så mycket som minnes storleken (RAM) på den virtuella datorn.

   * Om det inte finns tillräckligt med utrymme på OS-disken, bör du ändra platsen där minnesdumpen skapas och se om det finns en datadisk som är ansluten till den virtuella datorn som har tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter `%SystemRoot%` du med enhets beteckningen (till exempel "F:") för data disken i följande kommandon.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Föreslagen konfiguration för att aktivera OS-dump

**Läs in trasig OS-disk:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Aktivera på ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Aktivera på ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att bygga upp den virtuella datorn igen.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

För att lösa det här problemet behöver du först samla in minnesdumpen för kraschen och kontakta supporten med minnesdumpen. Samla in dumpfilen genom att följa dessa steg:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1. Använd steg [1-3 i reparations kommandona för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att förbereda en ny reparations-VM.

2. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1. På den reparera virtuella datorn går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som har tilldelats till den anslutna OS-disken är F, måste du gå till F:\Windows.

2. Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.

3. Om du har problem med att hitta Memory. dmp-filen kanske du vill använda [icke-maskbart avbrott (NMI) i serie konsolen i](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) stället. Du kan följa guiden för att [Generera en kernel-eller fullständig kraschdump](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) -fil med NMI-anrop.
