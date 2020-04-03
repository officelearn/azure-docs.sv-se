---
title: Virtual Machine svarar inte när du använder principen "Lokala grupprinciper & grupper"
description: Den här artikeln innehåller steg för att lösa problem där inläsningsskärmen har fastnat med en princip under uppstart i en virtuell virtuell dator (Virtuell dator).
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
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620862"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Virtual Machine svarar inte när du använder principen "Lokala grupprinciper & grupper"

Den här artikeln innehåller steg för att lösa problem där inläsningsskärmen har fastnat med en princip, under start, i en virtuell virtuell dator (Virtuell dator).

## <a name="symptom"></a>Symptom

När du använder [Startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att visa skärmbilden av den virtuella datorn ser du att skärmen har fastnat med meddelandet: *Tillämpa principen Lokala användare och grupper i grupprinciper*.

![Alternativ text: Skärm som visar Inläsning av grupprincip lokala användare och grupper (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Alternativ text: Skärm som visar Inläsning av grupprincip lokala användare och grupper (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Orsak

Symptomen på denna frysning orsakas av ett kodfel i Windows Profile Service Dynamic Link Library *(profsvc.dll*).

> [!NOTE]
> Det här felet gäller endast windows server 2012 och Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Politiken i fråga

Den princip som tillämpas som inte avslutar dess processer är:

* *Datorkonfiguration\Principer\Administrativa mallar\System-/användarprofiler\Ta bort användarprofiler som är äldre än ett angivet antal dagar vid omstart av systemet*

Denna policy kommer bara att hängas om följande sex villkor är sanna:

* *Principen Ta bort användarprofiler som är äldre än ett angivet antal dagar vid omstart av systemet* är aktiverad.
* Du har profiler som har uppfyllt ålderskraven för att kräva rensning.
* Du har komponenter som har registrerats för borttagning av profiler.
* Komponenterna anropar (direkt eller indirekt) som behöver hämta data från SCM-komponenterna (Service Control Manager) i Windows, till exempel Start-, Stopp- eller Frågeinformation om en tjänst.
* Du har konfigurerat en tjänst för att starta som *automatisk*.
* Den här tjänsten är inställd på att köras under kontexten för ett domänkonto (i motsats till att använda ett inbyggt konto, till exempel ett lokalt system).

### <a name="the-code-defect"></a>Koddefekten

Kodfelet beror på Service Control Manager (SCM) och profiltjänsterna som försöker använda lås på varandra samtidigt. Lås finns för att förhindra att flera tjänster gör ändringar på samma data samtidigt, vilket skulle orsaka skador. Vanligtvis skulle flera låsbegäranden inte orsaka problem. Men eftersom detta händer under uppstart, kan ingen av tjänsterna slutföra sina processer, eftersom de har fastnat väntar på varandra.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 - Service Control Manager dödlägen med principen "Ta bort användarprofiler vid omstart"

Det finns två åtgärder som överlappar varandra så att:

* Åtgärd 1 hämtar profillåset men har ännu inte skaffat SCM-låset.

  **Och**

* Åtgärd 2 förvärvar SCM-låset men har ännu inte fått profillåset.

När detta dödläge inträffar, hänger försöket att förvärva det andra nödvändiga låset åtgärden.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Åtgärd 1 - Meddelande om borttagning av gammal profil (har **profillås**, behöver **SCM-lås)**

1. Först hämtar principuppsättningen för att ta bort gamla profiler ett internt profiltjänstlås.

   * Det här låset finns för att förhindra att två trådar interagerar med profilerna medan *borttagningsåtgärden* är förlopp.

2. Principen hittar profiler som är tillräckligt gamla för att tas bort.
3. Som en del av profilborttagningen försöker en komponent som har registrerats för meddelanden om borttagningar av en profil starta **en tjänst**.
4. Innan tjänsten startas måste Service Control Manager (SCM) skaffa ett **internt SCM-lås** som innehas av trådar i **åtgärd 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Åtgärd 2 - Profilbelastning/skapande för användarspecifika data (har **SCM Lock**, behöver **profillås)**

1. Vid uppstart måste SCM beställa alla tjänster för *automatisk start* av sin grupp, liksom alla tjänster som dessa tjänster är beroende av.

2. **SCM förvärvar ett internt SCM-lås** som används för att styra åtkomsten till start-, stoppa- eller konfigureringstjänster när den beställer tjänsterna.

3. När tjänsterna är i ordning loopar SCM genom varje tjänst och startar den.

4. Om tjänsten körs under kontexten för ett domänkonto måste en profil antingen läsas in eller skapas för domänkontot, så att den kan lagra användarspecifika data.

5. Den här begäran skickas till **profiltjänsten**.

6. Profiltjänsten behöver åtkomst till det **interna lås som** förvärvats i åtgärd **1**.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Översikt över processer

1. Skapa och komma åt en virtuell reparations-VM
2. Aktivera seriekonsol och minnesdumpsamling
3. Återskapa den virtuella datorn
4. Samla in minnesdumpfilen

   > [!NOTE]
   > När du stöter på det här startfelet fungerar inte gästoperativsystemet. Du felsöker i offlineläge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och komma åt en virtuell reparations-VM

1. Använd [steg 1-3 i VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att förbereda en virtuell reparations-VM.
2. Använda Anslutning till fjärrskrivbordsanslutning till den virtuella reparationsdatorn.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Aktivera seriekonsol och minnesdumpsamling

Om du vill aktivera insamling av minnesdump och Serial Console kör du skriptet nedan:

1. Öppna en upphöjd kommandotolkssession (Kör som administratör).
2. Kör följande kommandon:

   * Aktivera seriekonsol:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Kontrollera att det lediga utrymmet på OS-disken är lika mycket som minnesstorleken (RAM) på den virtuella datorn.

   * Om det inte finns tillräckligt med utrymme på OS-disken bör du ändra platsen där minnesdumpfilen skapas och hänvisa den till alla datadiskar som är anslutna till den virtuella datorn och som har tillräckligt med ledigt utrymme. Om du vill `%SystemRoot%` ändra platsen ersätter du med enhetsbeteckningen (till exempel "F:") på datadisken i kommandona nedan.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Föreslagen konfiguration för att aktivera OS-dump

**Ladda trasig OS-disk:**

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

Använd [steg 5 i vm-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att sätta ihop den virtuella datorn igen.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpfilen

För att lösa problemet måste du först samla in minnesdumpfilen för krasch- och kontaktsupporten med minnesdumpfilen. Så här samlar du in dumpfilen:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny reparerad virtuell dator

1. Använd steg [1-3 i VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att förbereda en ny reparations-VM.

2. Använda Anslutning till fjärrskrivbordsanslutning till den virtuella reparationsdatorn.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka en supportbiljett

1. På den virtuella reparationsdatorn går du till windowsmappen i den anslutna OS-disken. Om drivrutinsbrevet som är tilldelat till den anslutna OS-disken är F måste du gå till F:\Windows.

2. Leta reda på filen memory.dmp och skicka sedan [en supportbiljett](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpfilen.

3. Om du har problem med att hitta filen memory.dmp kanske du vill använda [NMI-anrop (Non-Maskable interrupt) i seriell konsol i](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) stället. Du kan följa guiden för att [generera en kärna eller fullständig kraschdumpfil](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) med NMI-anrop.
