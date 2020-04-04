---
title: Felsöka pushinstallation för Mobilitetstjänster med Azure Site Recovery
description: Felsöka installationsfel för Mobilitetstjänster när du aktiverar replikering för haveriberedskap med Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656310"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Felsöka pushinstallation av mobilitetstjänster

Mobilitetstjänstinstallationen är ett viktigt steg för att aktivera replikering. Hur framgångsrikt det här steget lyckas beror på att uppfylla förutsättningar och arbeta med konfigurationer som stöds. De vanligaste felen du kan uppstå vid installationen av mobilitetstjänsten beror på:

* [Fel vid autentiseringsuppgifter/behörighet](#credentials-check-errorid-95107--95108)
* [Inloggningsfel](#login-failures-errorid-95519-95520-95521-95522)
* [Anslutningsfel](#connectivity-failure-errorid-95117--97118)
* [Fel vid fil- och skrivardelning](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-fel (Windows Management Instrumentation)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Operativsystem som inte stöds](#unsupported-operating-systems)
* [Startkonfigurationer som inte stöds](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Vss-installationsfel (Volume Shadow Copy Service)](#vss-installation-failures)
* [Enhetsnamn i GRUB-konfiguration i stället för enhet UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Lvm-volym (Logical Volume Manager)](#lvm-support-from-920-version)
* [Varningar för omstart](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

När du aktiverar replikering försöker Azure Site Recovery installera Mobility-tjänstagenten på din virtuella dator (VM). Som en del av den här processen försöker konfigurationsservern ansluta till den virtuella datorn och kopiera agenten. Om du vill aktivera en lyckad installation följer du felsökningsvägledningen steg för steg.

## <a name="credentials-check-errorid-95107--95108"></a>Autentiseringsuppgifter (ErrorID: 95107 & 95108)

Kontrollera om användarkontot som valts under aktivera replikering är giltigt och korrekt. Azure Site Recovery kräver **rotkontot** eller användarkontot med **administratörsbehörighet** för att utföra en push-installation. Annars blockeras push-installationen på källmaskinen.

För Windows (**fel 95107**) kontrollerar du att användarkontot har administrativ åtkomst på källdatorn, med antingen ett lokalt konto eller ett domänkonto. Om du inte använder ett domänkonto måste du inaktivera kontrollen Fjärråtkomst på den lokala datorn.

* Så här lägger du till en registernyckel som inaktiverar kontrollen Fjärråtkomst manuellt:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Lägg till `DWORD`en ny:`LocalAccountTokenFilterPolicy`
  * Ange värdet till`1`

* Om du vill lägga till registernyckeln kör du följande kommando från en kommandotolk:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

För Linux (**fel 95108**) måste du välja **rotkontot** för en lyckad installation av Mobility-serviceagenten. Dessutom bör SSH File Transfer Protocol (SFTP) tjänster köras. Så här aktiverar du SFTP-undersystemet och lösenordsautentiseringen i _sshd_config-filen:_

1. Logga in som **rot**.
1. Gå till _filen /etc/ssh/sshd_config_hittar du raden som `PasswordAuthentication`börjar med .
1. Ta av raden och ändra värdet `yes`till .
1. Leta reda på raden `Subsystem`som börjar med och ta bort kommentaren.
1. Starta `sshd` om tjänsten.

Om du vill ändra autentiseringsuppgifterna för det valda användarkontot följer du [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Otillräckligt privilegier fel (ErrorID: 95517)

När användaren som väljer att installera Mobilitetsagenten inte har administratörsbehörighet tillåts inte konfigurationsservern/skalningsprocessservern att kopiera programvaran Mobility agent till källdatorn. Det här felet är ett resultat av ett nekat åtkomstfel. Kontrollera att användarkontot har administratörsbehörighet.

Om du vill ändra autentiseringsuppgifterna för det valda användarkontot följer du [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Otillräckligt privilegier fel (ErrorID: 95518)

När domänförtroenderelationsetableringen mellan den primära domänen och arbetsstationen misslyckas när du försöker logga in på källdatorn misslyckas installationen av mobilitetsagenten med fel-ID 95518. Kontrollera att användarkontot som används för att installera Mobilitetsagenten har administratörsbehörighet för att logga in via källdatorns primära domän.

Om du vill ändra autentiseringsuppgifterna för det valda användarkontot följer du [instruktionerna](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Inloggningsfel (ErrorID: 95519, 95520, 95521, 95522)

I det här avsnittet beskrivs felmeddelanden om autentiseringsuppgifter och inloggning.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Autentiseringsuppgifterna för användarkontot har inaktiverats (ErrorID: 95519)

Användarkontot som valdes under aktivera replikering inaktiverades. Om du vill aktivera användarkontot läser du den [här artikeln](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) eller kör följande kommando genom att ersätta _textanvändarnamnet_ med det faktiska användarnamnet.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Autentiseringsuppgifter utelåst på grund av flera misslyckade inloggningsförsök (ErrorID: 95520)

Flera misslyckade försök att komma åt en dator kommer att låsa användarkontot. Felet kan bero på:

* Autentiseringsuppgifterna som anges under konfigurationskonfigurationen är felaktiga.
* Användarkontot som valts under aktivera replikering är fel.

Ändra de autentiseringsuppgifter som valts genom att följa [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) och försöka igen.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Inloggningsservrar är inte tillgängliga på källmaskinen (ErrorID: 95521)

Det här felet uppstår när inloggningsservrarna inte är tillgängliga på källdatorn. Om inloggningsservrar inte är tillgängliga misslyckas inloggningsbegäranden och mobilitetsagenten kan inte installeras. För en lyckad inloggning, se till att inloggningsservrar är tillgängliga på källmaskinen och starta Netlogon-tjänsten. Mer information finns i [Windows Logon Scenarios](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Inloggningstjänsten körs inte på källmaskinen (ErrorID: 95522)

Inloggningstjänsten körs inte på källdatorn och orsakade fel på inloggningsbegäran. Mobilitetsagenten kan inte installeras. LÃ¶s problemet genom att anse `Netlogon` nÃ¥ll av nÃ¤r du ansÃ¤nder du en av följande metoder för att starta tjänsten på källdatorn:

* Om du `Netlogon` vill starta tjänsten från `net start Netlogon`en kommandotolk kör du kommandot .
* Starta tjänsten från `Netlogon` Aktivitetshanteraren.

## <a name="connectivity-failure-errorid-95117--97118"></a>Anslutningsfel (ErrorID: 95117 & 97118)

Konfigurationsserver/utskalningsprocessserver försöker ansluta till käll-VM för att installera Mobilitetsagenten. Det här felet uppstår när källdatorn inte kan nås eftersom det finns problem med nätverksanslutningen.

Så här löser du felet:

* Se till att du kan pinga källmaskinen från konfigurationsservern. Om du har valt skalningsprocessservern under aktivera replikering kontrollerar du att du kan pinga källdatorn från processservern.

* Från kommandoraden för källservermaskinen använder du `Telnet` för att pinga konfigurationsservern eller skalningsprocessservern på HTTPS-port 135 enligt följande kommando. Det här kommandot kontrollerar om det finns några problem med nätverksanslutning eller problem med blockering av brandväggsporter.

  `telnet <CS/ scale-out PS IP address> <135>`

* Dessutom, för en Virtuell Linux:Dessutom, för en Virtuell Linux-dator:
  * Kontrollera om de senaste OpenSSH-, OpenSSH-server- och OpenSSL-paketen är installerade.
  * Kontrollera och se till att Secure Shell (SSH) är aktiverat och körs på port 22.
  * SFTP-tjänster bör köras. Så här aktiverar du SFTP-undersystem och lösenordsautentisering i _sshd_config-filen:_

    1. Logga in som **rot**.
    1. Gå till _/etc/ssh/sshd_config_ fil, hitta den rad `PasswordAuthentication`som börjar med .
    1. Ta av raden och ändra värdet `yes`till .
    1. Hitta raden som börjar `Subsystem`med och ta bort kommentering av raden
    1. Starta `sshd` om tjänsten.

* Ett anslutningsförsök kunde ha misslyckats om det inte finns några riktiga svar efter en viss tid, eller om en etablerad anslutning misslyckades eftersom en ansluten värd inte kunde svara.
* Det kan vara ett problem med anslutnings-/nätverks-/domänrelaterade. Det kan också bero på att problemet med DNS-namn löser eller TCP-portutmattningsproblem. Kontrollera om det finns några sådana kända problem i domänen.

## <a name="connectivity-failure-errorid-95523"></a>Anslutningsfel (ErrorID: 95523)

Det här felet uppstår när nätverket som källdatorn finns inte hittas, kan ha tagits bort eller inte längre är tillgängligt. Det enda sättet att lösa felet är att se till att nätverket finns.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontroll av fil- och skrivardelningstjänster (ErrorID: 95105 & 95106)

Kontrollera om delningstjänsten Fil och skrivare är aktiverad på den virtuella datorn efter en anslutningskontroll. Dessa inställningar krävs för att kopiera Mobilitetsagenten till källmaskinen.

För **Windows 2008 R2 och tidigare versioner:**

* Om du vill aktivera fil- och utskriftsdelning via Windows-brandväggen
  1. Öppna **Kontrollpanelen** > **System och Säkerhet** > **Windows-brandväggen**. Välj **Avancerade inställningar** > **inkommande regler i** konsolträdet i den vänstra rutan.
  1. Leta reda på regler fil- och skrivardelning (NB-session-In) och fil- och skrivardelning (SMB-In).
  1. Högerklicka på regeln för varje regel och klicka sedan på **Aktivera regel**.

* Så här aktiverar du fildelning med grupprincip:
  1. Gå **Start**till Start `gpmc.msc` , skriv och sök.
  1. Öppna följande mappar i navigeringsfönstret: Administrativa > **mallar** > för lokal **datorprincipanvändarkonfiguration** > **User Configuration****Windows Components** > **Network Sharing**.
  1. Dubbelklicka på **Förhindra att användare delar filer i sin profil**i informationsfönstret.

     Om du vill inaktivera grupprincipinställningen och aktivera användarens möjlighet att dela filer väljer du **Inaktiverad**.

  1. Spara ändringarna genom att välja **OK**.

  Mer information finns i [Aktivera eller inaktivera fildelning med grupprincip](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Om du vill aktivera fil- och skrivardelning för senare versioner av Windows eller Linux följer du anvisningarna i [Installera mobilitetstjänsten för haveriberedskap av virtuella datorer och fysiska servrar](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Konfigurationskontroll (WMI) (WMI) (Felkod: 95103)

När fil- och skrivartjänster kontrollerar du WMI-tjänsten för privata, offentliga och domänprofiler via brandväggen. Dessa inställningar krävs för att slutföra fjärrkörning på källdatorn.

Så här aktiverar du WMI:

1. Gå till > **Kontrollpanelens säkerhet** och välj **Windows-brandväggen**. **Control Panel**
1. Välj **Ändra inställningar** och välj sedan fliken **Undantag.**
1. Markera kryssrutan för WMI (Windows Management Instrumentation) i fönstret **Undantag** för att aktivera WMI-trafik genom brandväggen.

Du kan också aktivera WMI-trafik genom brandväggen från kommandotolken med följande kommando:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Andra WMI felsökning artiklar kunde hittas på följande artiklar.

* [Grundläggande WMI-testning](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Felsökning av WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Felsöka problem med WMI-skript och WMI-tjänster](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Operativsystem som inte stöds

En annan vanlig orsak till fel kan vara på grund av ett operativsystem som inte stöds. Använd ett operativsystem och en kernel-version som stöds för en lyckad installation av mobilitetstjänsten. Undvik användning av privata plåster.

Information om hur du visar listan över operativsystem och kärnversioner som stöds av Azure Site Recovery finns i [supportmatrisdokumentet](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Konfigurationer av startdiskar som inte stöds (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Start- och systempartitioner/volymer är inte samma disk (ErrorID: 95309)

Före 9.20-versionen var start- och systempartitioner/volymer på olika diskar en konfiguration som inte stöds. Från och med [9.20-versionen](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)stöds den här konfigurationen.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Startdisken är inte tillgänglig (ErrorID: 95310)

En virtuell dator utan startdiskett kan inte skyddas. En startdiskett säkerställer en smidig återställning av en virtuell dator under en redundansåtgärd. Frånvaro av en startdiskett resulterar i ett fel att starta datorn efter redundans. Kontrollera att den virtuella datorn innehåller en startdiskett och försöker igen. Dessutom stöds inte flera startdiskar på samma dator.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Flera startdiskar finns på källdatorn (ErrorID: 95311)

En virtuell dator med flera startdiskar är inte en [konfiguration som stöds.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systempartition på flera diskar (ErrorID: 95313)

Före 9.20-versionen var en rotpartition eller volyminställning på flera diskar en konfiguration som inte stöds. Från och med [9.20-versionen](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)stöds den här konfigurationen.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Aktivera skydd misslyckades som enhetsnamn som nämns i GRUB-konfigurationen i stället för UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Möjlig orsak

Konfigurationsfilerna för Grand Unified Bootloader (GRUB)_(/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, eller _/etc/default/grub_) kan innehålla värdet för parametrarna **rot** och **återupptas** som de faktiska enhetsnamnen i stället för en universellt unik identifierare (UUID). Site Recovery ger UUID-metoden mandat eftersom enhetsnamnen kan ändras över omstart av den virtuella datorn. Den virtuella datorn kanske till exempel inte är online med samma namn vid redundans och det resulterar i problem.

Ett exempel:

- Följande rad är från GRUB-filen _/boot/grub2/grub.cfg:_

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Följande rad är från GRUB-filen _/boot/grub/menu.lst:_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB-raderna innehåller faktiska enhetsnamn för parametrarna **rot** och **återuppta** snarare än UUID.

### <a name="how-to-fix"></a>Så här fixar du

Enhetsnamnen bör ersättas med motsvarande UUID.

1. Hitta enhetens UUID genom att `blkid \<device name>`köra kommandot .

   Ett exempel:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Nu ersätta enhetens namn med dess UUID i formatet som `root=UUID=\<UUID>`. Till exempel, om vi ersätter enhetsnamnen med UUID för rot- och återuppta parameter som nämns i filerna _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_, eller _/etc/default/grub,_ ser raderna i filerna ut som följande rad:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Starta om skyddet.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installera mobilitetstjänsten avslutad med en varning om att starta om (ErrorID: 95265 & 95266)

Site Recovery Mobility-tjänsten har många komponenter, varav en kallas filterdrivrutin. Filterdrivrutinen läses bara in i systemminnet under en omstart av systemet. Filterdrivrutinskorrigeringar kan bara realiseras när en ny filterdrivrutin läses in vid tidpunkten för en omstart av systemet.

> [!IMPORTANT]
> Detta är en varning och befintlig replikering kommer att fungera även efter den nya agentuppdateringen. Du kan välja att starta om när du vill få fördelarna med nya filterdrivrutinen, men om du inte startar om fortsätter den gamla filterdrivrutinen att fungera. Så, efter en uppdatering utan en omstart, med undantag för filterdrivrutinen, **fördelarna med andra förbättringar och korrigeringar i Mobilitet tjänsten få realiseras**. Även om det rekommenderas är det inte obligatoriskt att starta om efter varje uppgradering. Om du vill ha information om när en omstart är obligatorisk anger du avsnittet [Starta om efter uppgradering av mobilitetstjänsten](service-updates-how-to.md#reboot-after-mobility-service-upgrade) i Tjänstuppdateringar i Azure Site Recovery.

> [!TIP]
>Bästa praxis för schemaläggning av uppgraderingar under underhållsfönstret finns i [supporten för de senaste operativsystem/kärnan](service-updates-how-to.md#support-for-latest-operating-systemskernels) i tjänstuppdateringar i Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-stöd från 9.20 version

Före 9.20-versionen stöddes LVM (Logical Volume Manager) endast för datadiskar. Partitionen `/boot` ska finnas på en diskpartition och inte en LVM-volym.

Från och med [9,20-versionen](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)stöds [OS-disken på LVM.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="insufficient-space-errorid-95524"></a>Otillräckligt utrymme (ErrorID: 95524)

När mobilitetsagenten kopieras till källdatorn krävs minst 100 MB ledigt utrymme. Kontrollera att källmaskinen har den mängd ledigt utrymme som krävs och att du försöker igen.

## <a name="vss-installation-failures"></a>VSS-installationsfel

VSS-installationen (Volume Shadow copy Service) är en del av installation av mobilitetsagenter. Den här tjänsten används i processen för att generera programkonsekventa återställningspunkter. Fel under VSS-installationen kan uppstå på grund av flera orsaker. Information om de exakta felen finns i _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Några av de vanligaste felen och lösningsstegen markeras i följande avsnitt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fel -2147023170 [0x800706BE] - utgångskod 511

Det här problemet ses oftast när antivirusprogram blockerar åtgärder för Azure Site Recovery-tjänster.

Gör så här för att lösa problemet:

1. Granska listan över [mapputeslutningar från antivirusprogrammet](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Följ de riktlinjer som publiceras av antivirusleverantören för att häva blockeringen av registreringen av DLL i Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fel 7 [0x7] - utgångskod 511

Det här felet är ett körningsfel som orsakas eftersom det inte finns tillräckligt med minne för att installera VSS. Öka diskutrymmet för att åtgärden ska slutföras.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fel -2147023824 [0x80070430] - utgångskod 517

Det här felet uppstår när Azure Site Recovery VSS Provider-tjänsten [har markerats för borttagning](/previous-versions/ms838153(v=msdn.10)). Försök att installera VSS manuellt på källdatorn genom att köra följande kommando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fel -2147023841 [0x8007041F] - utgångskod 512

Det här felet uppstår när Azure Site Recovery VSS Provider-tjänstdatabasen är [låst](/previous-versions/ms833798(v=msdn.10)). Försök att installera VSS manuellt på källdatorn genom att köra följande kommando från en kommandotolk:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

När det är fel kontrollerar du om något antivirusprogram eller andra tjänster har fastnat i **ett starttillstånd.** En process i **ett starttillstånd** kan behålla låset på databastjänster. Det kommer att leda till fel i installationen av VSS-providern. Kontrollera att ingen tjänst är i **ett starttillstånd** och försök sedan igen med ovanstående åtgärd.

### <a name="vss-exit-code-806"></a>VSS-utgångskod 806

Det här felet uppstår när användarkontot som används för `CSScript` installationen inte har behörighet att köra kommandot. Ange nödvändiga behörigheter till användarkontot för att köra skriptet och försöka igen.

### <a name="other-vss-errors"></a>Andra VSS-fel

Försök att installera VSS-providertjänsten manuellt på källdatorn genom att köra följande kommando från en kommandotolk:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS-fel - 0x8004E00F

Det här felet uppstår vanligtvis under installationen av `DCOM` `DCOM` Mobility-agenten på grund av problem i och är i ett kritiskt tillstånd.

Använd följande procedur för att fastställa orsaken till felet.

### <a name="examine-the-installation-logs"></a>Undersök installationsloggarna

1. Öppna installationsloggen på _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. Förekomsten av följande fel indikerar det här problemet:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Så här löser du problemet:

Kontakta [Microsoft Windows-plattformsteamet](https://aka.ms/Windows_Support) för att få hjälp med att lösa DCOM-problemet.

När DCOM-problemet är löst installerar du om Azure Site Recovery VSS Provider manuellt med följande kommando från en kommandotolk:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Om programkonsekvens inte är avgörande för dina krav på haveriberedskap kan du kringgå VSS Provider-installationen.

Så här kringgår du installationen av Azure Site Recovery VSS Provider och installerar azure site recovery VSS Provider efter installationen:

1. Installera mobilitetstjänsten. Installationen misslyckas i steget: **Post installera konfiguration**.
1. Så här kringgår du VSS-installationen:
   1. Öppna installationskatalogen för Azure Site Recovery Mobility Service som finns på:

      _C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent_

   1. Ändra installationsskripten för Azure Site Recovery VSS Provider _InMageVSSProvider_Install_ och _InMageVSSProvider_Uninstall.cmd_ för att alltid lyckas genom att lägga till följande rader:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Gör en manuell installation av mobilitetsagenten.
1. När installationen lyckas och flyttas till nästa **steg, Konfigurera**, ta bort de rader som du har lagt till.
1. Om du vill installera VSS-providern öppnar du en kommandotolk som administratör och kör följande kommando:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Kontrollera att Azure Site Recovery VSS Provider är installerad som en tjänst i Windows Services. Öppna MMC för komponenttjänst för att bekräfta att VSS-providern finns med i listan.
1. Om VSS-providerinstallationen fortsätter att misslyckas kan du arbeta med teknisk support för att lösa behörighetsfelen i CAPI2 (Cryptographic Application Programming Interface).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS-providerinstallationen misslyckas eftersom klustertjänsten aktiveras på en icke-klusterdator

Det här problemet medför att Azure Site Recovery Mobility Agent-installationen misslyckas under azure site recovery VSS-providerinstallationen. Felet beror på att det `COM+` finns ett problem med som förhindrar VSS-providerinstallationen.

### <a name="to-identify-the-issue"></a>Så här identifierar du problemet

I loggen på konfigurationsservern på _C:\ProgramData\ASRSetupLogs\UploadedLogs\<datum-tid>UA_InstallLogFile.log_ hittar du följande undantag:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Så här löser du problemet:

1. Kontrollera att den här datorn är en icke-klusterdator och att klusterkomponenterna inte används.
1. Om komponenterna inte används tar du bort klusterkomponenterna från datorn.

## <a name="drivers-are-missing-on-the-source-server"></a>Drivrutiner saknas på källservern

Om mobility agent-installationen misslyckas undersöker du loggarna under _C:\ProgramData\ASRSetupLogs_ för att avgöra om några av de nödvändiga drivrutinerna saknas i vissa kontrolluppsättningar.

Så här löser du problemet:

1. Öppna registret med en `regedit.msc`registerredigerare, till exempel .
1. Öppna `HKEY_LOCAL_MACHINE\SYSTEM` noden.
1. Leta `SYSTEM` reda på kontrolluppsättningarna i noden.
1. Öppna varje kontrolluppsättning och kontrollera att det finns följande Windows-drivrutiner:

   * Atapi
   * Vmbus (vmbus)
   * Storflt (storflt)
   * Storvsc (storvsc)
   * Intelide (av Intelide)

1. Installera om alla drivrutiner som saknas.

## <a name="next-steps"></a>Nästa steg

[Läs mer](vmware-azure-tutorial.md) om hur du konfigurerar haveriberedskap för virtuella datorer med VMware.
