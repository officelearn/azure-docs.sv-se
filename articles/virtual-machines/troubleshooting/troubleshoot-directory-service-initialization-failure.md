---
title: Felsöka Windows stoppfel – katalog tjänst initierings fel
description: Lösa problem där en virtuell dator med Active Directory domänkontrollanten (VM) i Azure fastnar i en slinga som anger att den måste startas om.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 909481964f8aa3272715e235fa011562225a9422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028370"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Felsöka Windows stoppfel – katalog tjänst initierings fel

Den här artikeln innehåller steg för att lösa problem där en Active Directory virtuell dator med domänkontrollanten (VM) i Azure är fastnat i en loop och anger att den måste startas om.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn, visar skärm bilden att den virtuella datorn måste startas om på grund av ett fel och visar stopp koden **0XC00002E1** i Windows Server 2008 R2 eller **0xC00002E2** i Windows Server 2012 eller senare.

![Start skärms tillstånd för Windows Server 2012 "din dator stötte på ett problem och måste startas om. Vi samlar bara in fel information och startar sedan om. ".](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Orsak

Felkoden **0xC00002E2** representerar **STATUS_DS_INIT_FAILURE**och felkod **0xC00002E1** representerar **STATUS_DS_CANT_START**. Båda felen inträffar när det är problem med katalog tjänsten.

När operativ systemet startas, tvingas det att starta om automatiskt av den lokala säkerhetsautentiserings servern (**LSASS.exe**), som autentiserar användar inloggningar. Autentisering kan inte ske när operativ systemet på den virtuella datorn är en domänkontrollant som inte har Läs-/Skriv behörighet till den lokala Active Directory databasen. På grund av brist på åtkomst till **Active Directory (AD)** kan LSASS.exe inte autentisera, och det tvingas att starta om operativ systemet.

Det här felet kan orsakas av något av följande villkor:

- Det finns ingen åtkomst till den disk som innehåller den lokala AD-databasen (**NTDS. DIT**).
- Disken som innehåller den lokala AD-databasen (NTDS. DIT) har slut på ledigt utrymme.
- Den lokala AD-databasen (NTDS. DIT) saknas.
- Den virtuella datorn har flera diskar och SAN-principen (Storage Area Network) är felaktigt konfigurerad. SAN-principen är inte inställd på **ONLINEALL**, och diskar som inte är OS-diskar är anslutna i offline-läge på disk hanteraren.
- Den lokala AD-databasen (NTDS. DIT) är skadad.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt:

1. Skapa och få åtkomst till en reparations-VM.
1. Ledigt utrymme på disken.
1. Kontrol lera att enheten som innehåller AD-databasen är ansluten.
1. Aktivera återställnings läge för katalog tjänster.
1. **Rekommenderas**: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par.
1. Återskapa den virtuella datorn.
1. Konfigurera om SAN-principen.

> [!NOTE]
> Gäst operativ systemet fungerar inte när det här felet påträffas. Du kommer att felsöka i offline-läge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att förbereda en reparations-VM.
1. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="free-up-space-on-disk"></a>Frigör utrymme på disken

Eftersom disken nu är ansluten till en virtuell reparations dator kontrollerar du att disken som innehåller den Active Directory interna databasen har tillräckligt med utrymme för att utföra korrekt.

1. Kontrol lera om disken är full genom att högerklicka på enheten och välja **Egenskaper**.
1. Om disken har mindre än 300 MB ledigt utrymme [expanderar du den till maximalt 1 TB med hjälp av PowerShell](../windows/expand-os-disk.md).
1. Om disken har uppnått 1 TB använt utrymme utför du en disk rensning.

   1. Använd PowerShell för att [Koppla bort data disken](../windows/detach-disk.md#detach-a-data-disk-using-powershell) från den brutna virtuella datorn.
   1. [Koppla bort data disken](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm) till en fungerande virtuell dator när den är frånkopplad från den brutna virtuella datorn.
   1. Använd [disk rensnings verktyget](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) för att frigöra ytterligare utrymme.

1. **Valfritt** – om mer utrymme behövs öppnar du en cmd-instans och anger `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` kommandot för att utföra en avfragmentering på enheten:

  * Ersätt `<LETTER ASSIGNED TO THE OS DISK>` med OS-diskens bokstav i kommandot. Om exempelvis disk beteckningen är `F:` , är kommandot `defrag F: /u /x /g` .

  * Det kan ta flera timmar beroende på Fragmenteringens nivå.

Fortsätt till nästa uppgift om det finns tillräckligt med utrymme på disken.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Kontrol lera att enheten som innehåller Active Directorys databasen är kopplad

1. Öppna en upphöjd kommando instans och kör följande kommandon:

   1. Läs in register fil:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      Beteckningen `f:` förutsätter att disken är enhet `F:` . Använd enhets beteckningen som tillhör enheten som innehåller OS-disken.

   1. Fastställ enhets beteckningen och mappen för **NTDS. DIT**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Ta bort register fil:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Kontrol lera i Azure Portal att enheten är av typen NTDS. DIT konfigureras läggs till i den virtuella datorn.
1. Använd disk hanterings konsolen från gäst operativ systemet för att kontrol lera att disken som innehåller NTDS. DIT är online.
   1. Verktyget disk hantering finns i **administrations verktyg > dator hantering > lagring**eller kan nås med hjälp av `diskmgmt.msc` kommandot i en cmd-instans.
1. Om disken inte är ansluten till den virtuella datorn kan du ansluta data disken för att åtgärda problemet.

   Om disken anslöts normalt fortsätter du med nästa uppgift.

### <a name="enable-directory-services-restore-mode"></a>Aktivera återställnings läge för katalog tjänster

Konfigurera den virtuella datorn så att den startar i läget för **återställnings läge för katalog tjänster (DSRM)** för att kringgå kontroll av att NTDS är tillgänglig. DIT-fil under start.

1. Innan du fortsätter kontrollerar du att du har slutfört de tidigare uppgifterna för att koppla disken till en virtuell reparations dator och har fastställt vilken disk som är NTDS. DIT-filen finns i.
1. Med en upphöjd kommando instans anger du information om startpartitionen på arkivet för att hitta identifieraren från den aktiva partitionen:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Ersätt `< Drive Letter >` med den bokstav som anges i föregående steg.

   ![Skärm bilden visar en upphöjd kommando instansen när du har angett "bcdedit/Store <enhets bokstaven>: \boot\bcd/Enum", som visar Windows Boot Manager med identifieraren.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Aktivera `safeboot DsRepair` flaggan på startpartitionen:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Ersätt `< Drive Letter >` och `< Identifier >` med de värden som fastställs i föregående steg.

1. Fråga efter start alternativen igen för att se till att ändringen har angetts korrekt.

   ![Skärm bilden visar en upphöjd kommando instansen när du har aktiverat flaggan safeboot DsRepair.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Rekommenderas: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par

Om du vill aktivera samling av minnes dum par och seriell konsol kör du följande skript genom att öppna en upphöjd kommando tolks session (kör som administratör) och köra följande kommandon.

1. Aktivera serie konsolen:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Kontrol lera att det lediga utrymmet på OS-disken minst motsvarar minnes storleken (RAM) på den virtuella datorn.

  1. Om det inte finns tillräckligt med utrymme på OS-disken ändrar du den plats där minnesdumpen skapas och kontrollerar att det finns en datadisk som är ansluten till den virtuella datorn som har tillräckligt med ledigt utrymme.

     Om du vill ändra plats ersätter du `%SystemRoot%` med enhets beteckningen (till exempel `F:` ) för data disken i följande kommandon.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>Följande konfiguration rekommenderas för att aktivera OS-dump:

  **Läs in trasig OS-disk**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **Aktivera på ControlSet001**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Aktivera på ControlSet002**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Ta bort skadad OS-disk**:

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

1. Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att bygga upp den virtuella datorn igen.

### <a name="reconfigure-the-storage-area-network-policy"></a>Konfigurera om nätverks principen för lagrings utrymme

1. När du startar i DSRM-läge är den enda användare som är tillgänglig för inloggning den återställnings administratör som användes när den virtuella datorn uppgraderades till en domänkontrollant. Alla andra användare kommer att visa ett autentiseringsfel.

   1. Om ingen annan DOMÄNKONTROLLANT är tillgänglig måste du logga in lokalt med `.\administrator` eller `machinename\administrator` och DSRM-lösenordet.

1. Konfigurera SAN-principen så att alla diskar är online.

   1. Öppna en upphöjd kommando instans och ange `DISKPART` .
   1. Fråga efter listan över diskarna.

      `DISKPART> list disk`

   1. Ange följande kommandon för att välja den disk som måste anslutas till Internet och ändra SAN-principen:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. När problemet har åtgärd ATS kontrollerar du att flaggan `DsRepair safeboot` tas bort:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Starta om den virtuella datorn.

   > [!NOTE]
   > Om den virtuella datorn har migrerats från lokalt och du vill migrera fler domänkontrollanter från lokal plats till Azure bör du överväga att följa stegen i artikeln nedan för att förhindra att problemet uppstår i framtida migreringar:
   >
   > [Ladda upp befintliga lokala Hyper-V-domänkontrollanter till Azure med hjälp av Azure PowerShell](https://support.microsoft.com/help/2904015)
