---
title: Felsöka push-installation för Mobilitetstjänster med Azure Site Recovery f
description: Felsöka installationsfel för Mobilitetstjänster när du aktiverar replikering för haveriberedskap med Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953777"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Felsöka pushinstallation av Mobilitetstjänsten 

Installation av mobilitetstjänsten är ett viktigt steg under Aktivera replikering. Hur framgångsrikt det här steget lyckas beror enbart på att uppfylla förutsättningar och arbeta med konfigurationer som stöds. De vanligaste felen du möter under installationen av mobilitetstjänsten beror på:

* [Fel vid autentiseringsuppgifter/behörighet](#credentials-check-errorid-95107--95108)
* [Inloggningsfel](#login-failures-errorid-95519-95520-95521-95522)
* [Anslutningsfel](#connectivity-failure-errorid-95117--97118)
* [Fel vid fil- och skrivardelning](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-fel](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Operativsystem som inte stöds](#unsupported-operating-systems)
* [Startkonfigurationer som inte stöds](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-installationsfel](#vss-installation-failures)
* [Enhetsnamn i GRUB-konfiguration i stället för enhet UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM-volym](#lvm-support-from-920-version)
* [Varningar för omstart](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

När du aktiverar replikering försöker Azure Site Recovery att driva installationsmobilitetstjänstagenten på din virtuella dator. Som en del av detta försöker configuration-servern ansluta till den virtuella datorn och kopiera agenten. För att möjliggöra en lyckad installation följer du felsökningsvägledningen steg för steg nedan.

## <a name="credentials-check-errorid-95107--95108"></a>Autentiseringsuppgifter (ErrorID: 95107 & 95108)

* Kontrollera om användarkontot som valts under aktivera replikering är **giltigt, korrekt**.
* Azure Site Recovery kräver **ROOT-konto** eller användarkonto med **administratörsbehörighet** för att utföra push-installation. Annars blockeras push-installationen på källmaskinen.
  * För Windows (**fel 95107**) kontrollera om användarkontot har administrativ åtkomst, antingen lokal eller domän, på källdatorn.
  * Om du inte använder ett domänkonto måste du inaktivera kontrollen Fjärråtkomst på den lokala datorn.
    * Om du vill inaktivera kontrollen För fjärråtkomst HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System-registernyckeln, lägger du till en ny DWORD: LocalAccountTokenFilterPolicy. Ange värdet 1. Kör följande kommando från kommandotolken om du vill köra det här steget:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * För Linux (**fel 95108**) måste du välja rotkonto för framgångsrik installation av mobilitetsagent. Dessutom bör SFTP-tjänster köras. Så här aktiverar du SFTP-undersystem och lösenordsautentisering i sshd_config-filen:
    1. Logga in som rot.
    2. Gå till filen /etc/ssh/sshd_config, hitta den rad som börjar med PasswordAuthentication.
    3. Ta av raden och ändra värdet till ja.
    4. Leta reda på raden som börjar med Delsystemet och ta bort kommentaren.
    5. Starta om sshd-tjänsten.

Om du vill ändra autentiseringsuppgifterna för det valda användarkontot följer du instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Otillräckligt privilegier fel (ErrorID: 95517)

När användaren som valt att installera mobilitetsagenten inte har administratörsbehörighet, kommer configuration server/scale-out process server inte tillåtas att kopiera programvaran för mobilitetsagenten till källdatorn. Det här felet är alltså ett resultat av nekad åtkomst. Kontrollera att användarkontot har administratörsbehörighet.

Om du vill ändra autentiseringsuppgifterna för det valda användarkontot följer du instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Otillräckligt privilegier fel (ErrorID: 95518)

När domänförtroenderelationsetablering mellan den primära domänen och arbetsstationen misslyckas när du försöker logga in på källdatorn misslyckas installationen av mobilitetsagenten med fel-ID 95518. Se därför till att användarkontot som används för att installera mobilitetsagenten har administratörsbehörighet för att logga in via den primära domänen för källdatorn.

Om du vill ändra autentiseringsuppgifterna för det valda användarkontot följer du instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Inloggningsfel (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Autentiseringsuppgifterna för användarkontot har inaktiverats (ErrorID: 95519)

Användarkontot som valdes under Aktivera replikering har inaktiverats. Om du vill aktivera användarkontot läser du artikeln [här](https://aka.ms/enable_login_user) eller kör följande kommando genom att ersätta *textanvändarnamnet* med det faktiska användarnamnet.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Autentiseringsuppgifter utelåst på grund av flera misslyckade inloggningsförsök (ErrorID: 95520)

Flera misslyckade försök att komma åt en dator kommer att låsa användarkontot. Felet kan bero på:

* Autentiseringsuppgifter som anges under konfigurationskonfigurationen är felaktiga ELLER
* Användarkontot som valdes under Aktivera replikering är fel

Så, ändra autentiseringsuppgifter som valts genom att följa instruktionerna som ges [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) och försöka igen åtgärden efter någon gång.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Inloggningsservrar är inte tillgängliga på källmaskinen (ErrorID: 95521)

Det här felet uppstår när inloggningsservrarna inte är tillgängliga på källdatorn. Brist på inloggningsservrar kommer att leda till fel på inloggningsbegäran och därmed kan mobilitetsagenten inte installeras. För lyckad inloggning, se till att Inloggningsservrar är tillgängliga på källmaskinen och starta inloggningstjänsten. Detaljerade instruktioner finns i KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: Det finns för närvarande inga inloggningsservrar tillgängliga.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Inloggningstjänsten körs inte på källmaskinen (ErrorID: 95522)

Inloggningstjänsten körs inte på källdatorn och orsakade fel på inloggningsbegäran. Mobilitetsagenten kan därför inte installeras. Lös problemet genom att se till att inloggningstjänsten körs på källdatorn för lyckad inloggning. Om du vill starta inloggningstjänsten kör du kommandot "net start Logon" från kommandotolken eller startar tjänsten "NetLogon" från Aktivitetshanteraren.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Anslutningsfel (ErrorID: 95117 & 97118)**

Konfigurationsserver/utskalningsprocessserver försöker ansluta till käll-VM för att installera Mobilitetsagenten. Det här felet uppstår när källdatorn inte kan nås på grund av problem med nätverksanslutningen. För att lösa,

* Se till att du kan pinga källmaskinen från konfigurationsservern. Om du har valt skalningsprocessserver under aktivera replikering kontrollerar du att du kan pinga källdatorn från processservern.
  * Från Source Server-kommandoraden använder du Telnet för att pinga konfigurationsservern/utskalningsprocessservern med https-port (135) som visas nedan för att se om det finns några problem med nätverksanslutning eller problem med blockering av brandväggsporter.

     `telnet <CS/ scale-out PS IP address> <135>`
* Dessutom, för **Linux VM**,
  * Kontrollera om senast öppnas, öppnassh-server och openssl-paket är installerade.
  * Kontrollera och se till att Secure Shell (SSH) är aktiverat och körs på port 22.
  * SFTP-tjänster bör köras. Om du vill aktivera SFTP-undersystem och lösenordsautentisering i sshd_config-filen
    * Logga in som rot.
    * Gå till filen /etc/ssh/sshd_config, hitta den rad som börjar med PasswordAuthentication.
    * Avkommenta raden och ändra värdet till ja
    * Hitta raden som börjar med Delsystemet och ta bort kommentering av raden
    * Starta om sshd-tjänsten.
* Ett anslutningsförsök kunde ha misslyckats om det inte finns något korrekt svar efter en viss tid, eller etablerad anslutning misslyckades eftersom den anslutna värden inte har svarat.
* Det kan vara ett problem med anslutnings-/nätverks-/domänrelaterade. Det kan också bero på dns-namn lösa problem eller TCP port utmattningsproblem. Kontrollera om det finns några sådana kända problem i domänen.

## <a name="connectivity-failure-errorid-95523"></a>Anslutningsfel (ErrorID: 95523)

Det här felet uppstår när det nätverk där källdatorn finns inte hittas eller kan ha tagits bort eller inte längre är tillgängligt. Det enda sättet att lösa felet är att se till att nätverket finns.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontroll av fil- och skrivardelningstjänster (ErrorID: 95105 & 95106)

Kontrollera om fil- och skrivardelningstjänsten är aktiverad på den virtuella datorn efter anslutningskontroll. Dessa inställningar krävs för att kopiera Mobilitetsagenten till källmaskinen.

För **windows 2008 R2 och tidigare versioner**

* Om du vill aktivera fil- och utskriftsdelning via Windows-brandväggen
  * Öppna kontrollpanelen -> System och säkerhet -> Windows-brandväggen -> i den vänstra rutan klickar du på Avancerade inställningar > klickar på Regler för inkommande information i konsolträdet.
  * Leta reda på regler fil- och skrivardelning (NB-session-In) och fil- och skrivardelning (SMB-In). Högerklicka på regeln för varje regel och klicka sedan på **Aktivera regel**.
* Om du vill aktivera fildelning med grupprincip
  * Gå till Start, skriv gpmc.msc och sök.
  * Öppna följande mappar i navigeringsfönstret: Lokal datorprincip, Användarkonfiguration, Administrativa mallar, Windows-komponenter och Nätverksdelning.
  * Dubbelklicka på **Förhindra att användare delar filer i sin profil**i informationsfönstret. Om du vill inaktivera grupprincipinställningen och aktivera användarens möjlighet att dela filer klickar du på Inaktiverad. Spara ändringarna genom att klicka på OK. Mer information finns i [Aktivera eller inaktivera fildelning med grupprincip](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

För **senare versioner**följer du instruktionerna i Installera [mobilitetstjänsten för haveriberedskap av virtuella datorer med VMware och fysiska servrar](vmware-azure-install-mobility-service.md) för att aktivera fil- och skrivardelning.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Konfigurationskontroll (WMI) (WMI) (Felkod: 95103)

När fil- och skrivartjänster kontrollerar aktiverar du WMI-tjänst för privata, offentliga och domänprofiler via brandväggen. Dessa inställningar krävs för att slutföra fjärrkörning på källdatorn. Om du vill aktivera

* Gå till Kontrollpanelen, klicka på Säkerhet och klicka sedan på Windows-brandväggen.
* Klicka på Ändra inställningar och sedan på fliken Undantag.
* Markera kryssrutan för WMI (Windows Management Instrumentation) i fönstret Undantag för att aktivera WMI-trafik genom brandväggen. 

Du kan också aktivera WMI-trafik genom brandväggen i kommandotolken. Använda följande kommando`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Andra WMI felsökning artiklar kunde hittas på följande artiklar.

* [Grundläggande WMI-testning](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Felsökning av WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Felsöka problem med WMI-skript och WMI-tjänster](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Operativsystem som inte stöds

En annan vanligaste orsaken till fel kan bero på operativsystem som inte stöds. Se till att du är på den operativsystem/kernel-version som stöds för en lyckad installation av mobilitetstjänsten. Undvik användning av privat plåster.
Om du vill visa listan över operativsystem och kärnversioner som stöds av Azure Site Recovery läser du vårt [supportmatrisdokument](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Konfigurationer av startdiskar som inte stöds (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Start- och systempartitioner /volymer är inte samma disk (ErrorID: 95309)

Före 9.20-versionen var start- och systempartitioner/volymer på olika diskar en konfiguration som inte stöds. Från [9.20-versionen](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)stöds den här konfigurationen. Använd den senaste versionen för det här stödet.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Startdisken är inte tillgänglig (ErrorID: 95310)

Det går inte att skydda en virtuell dator utan startdiskett. Detta för att säkerställa smidig återställning av virtuell dator under redundansåtgärd. Avsaknad av startdisken resulterar i att datorn inte kan startas efter redundans. Kontrollera att den virtuella datorn innehåller startdisketten och försöker igen. Observera också att flera startdiskar på samma dator inte stöds.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Flera startdiskar finns på källdatorn (ErrorID: 95311)

En virtuell dator med flera startdiskar är inte en [konfiguration som stöds](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systempartition på flera diskar (ErrorID: 95313)

Före 9.20-versionen var rotpartition eller volym som lades på flera diskar en konfiguration som inte stöds. Från [9.20-versionen](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)stöds den här konfigurationen. Använd den senaste versionen för det här stödet.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Aktivera skydd misslyckades som enhetsnamn som nämns i GRUB-konfigurationen i stället för UUID (ErrorID: 95320)

**Möjlig orsak:** </br>
GRUB-konfigurationsfilerna ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" eller "/etc/default/grub") kan innehålla värdet för parametrarna **rot** och **återupptas** som de faktiska enhetsnamnen istället för UUID. Site Recovery mandat UUID-metoden som enheter namn kan ändras över omstart av den virtuella datorn som vm kanske inte komma upp med samma namn på redundans vilket resulterar i problem. Ett exempel: </br>


- Följande rad är från GRUB-filen **/boot/grub2/grub.cfg**. <br>
  *linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2 ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts Linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2 ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts Linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts Linux /*


- Följande rad är från GRUB-filen **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Om du observerar den fetstilta strängen ovan har GRUB faktiska enhetsnamn för parametrarna "root" och "resume" i stället för UUID.
 
**Så här åtgärdar du:**<br>
Enhetsnamnen bör ersättas med motsvarande UUID.<br>


1. Hitta enhetens UUID genom att köra kommandot \<"blkid device name>". Ett exempel:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Nu ersätta enhetens namn med dess UUID i formatet\<som "root=UUID = UUID>". Till exempel, om vi ersätter enhetsnamnen med UUID för rot- och återuppta parameter som nämns ovan i filerna "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" eller "/etc/default/grub: då ser raderna i filerna ut. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Starta om skyddet igen

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installera Mobilitetstjänsten kompletterad med en varning om att starta om (ErrorID: 95265 & 95266)

Site Recovery mobilitetstjänsten har många komponenter, varav en kallas filterdrivrutin. Filterdrivrutinen läses in i systemminnet endast vid en omstart av systemet. Det innebär att filterdrivrutinen fixar kan endast realiseras när en ny filterdrivrutin laddas; som kan ske endast vid tidpunkten för omstart av systemet.

**Observera** att detta är en varning och befintlig replikering kommer att fungera även efter den nya agentuppdateringen. Du kan välja att starta om när du vill få fördelarna med nya filterdrivrutinen men om du inte startar om den gamla filterdrivrutinen fortsätter att fungera. Så, efter en uppdatering utan omstart, bortsett från filterdrivrutinen, **fördelarna med andra förbättringar och korrigeringar i mobilitetstjänsten blir realiserad**. Så, men rekommenderas, är det inte obligatoriskt att starta om efter varje uppgradering. Om du vill ha information om när en omstart är obligatorisk anger du [avsnittet Starta om källdatorn efter uppgraderingsavsnittet för mobilitetsagent](https://aka.ms/v2a_asr_reboot) i Tjänstuppdateringar i Azure Site Recovery.

> [!TIP]
>Bästa praxis för schemaläggning av uppgraderingar under underhållsfönstret finns i [supporten för de senaste OS/kernel-versionerna](https://aka.ms/v2a_asr_upgrade_practice) i Tjänstuppdateringar i Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-stöd från 9.20 version

Före 9.20-versionen stöddes LVM endast för datadiskar. /boot bör vara på en diskpartition och inte vara en LVM-volym.

Från [9,20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)stöds [OS-disk på LVM.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) Använd den senaste versionen för det här stödet.

## <a name="insufficient-space-errorid-95524"></a>Otillräckligt utrymme (ErrorID: 95524)

När Mobilitetsagenten kopieras till källdatorn krävs minst 100 MB ledigt utrymme. Så se till att källdatorn har ledigt utrymme och försök igen.

## <a name="vss-installation-failures"></a>VSS-installationsfel

VSS-installation är en del av installationen för mobilitetsagenten. Den här tjänsten används för att generera programkonsekventa återställningspunkter. Fel under VSS-installationen kan uppstå på grund av flera orsaker. Information om de exakta felen finns **i c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Få vanliga fel och lösningsstegen markeras i följande avsnitt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fel -2147023170 [0x800706BE] - utgångskod 511

Det här problemet visas oftast när antivirusprogram blockerar åtgärder för Azure Site Recovery-tjänster. Gör så här för att lösa problemet:

1. Uteslut alla mappar som nämns [här](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Följ de riktlinjer som publiceras av antivirusleverantören för att häva blockeringen av registreringen av DLL i Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fel 7 [0x7] - utgångskod 511

Detta är ett körningsfel och orsakas på grund av otillräckligt minne för att installera VSS. Se till att öka diskutrymmet för att slutföra den här åtgärden.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fel -2147023824 [0x80070430] - utgångskod 517

Det här felet uppstår när Azure Site Recovery VSS Provider-tjänsten [har markerats för borttagning](https://msdn.microsoft.com/library/ms838153.aspx). Försök att installera VSS manuellt på källmaskinen genom att köra följande kommandorad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fel -2147023841 [0x8007041F] - utgångskod 512

Det här felet uppstår när Azure Site Recovery VSS Provider-tjänstdatabasen är [låst](https://msdn.microsoft.com/library/ms833798.aspx). Försök att installera VSS manuellt på källmaskinen genom att köra följande kommandorad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Om det går fel kontrollerar du om något antivirusprogram eller andra tjänster har fastnat i "Start"-läge. Detta skulle kunna behålla låset på databastjänster. Det kommer att leda till fel i installationen av VSS-providern. Kontrollera att ingen tjänst är i "Start" tillstånd och sedan försöka igen åtgärden ovan.

### <a name="vss-exit-code-806"></a>VSS-utgångskod 806

Det här felet uppstår när användarkontot som används för installationen inte har behörighet att köra CSScript-kommandot. Ange nödvändiga behörigheter till användarkontot för att köra skriptet och försöka igen.

### <a name="other-vss-errors"></a>Andra VSS-fel

Försök att installera VSS-providertjänsten manuellt på källdatorn genom att köra följande kommandorad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS-fel - 0x8004E00F

Det här felet uppstår vanligtvis under installationen av mobilitetsagenten på grund av problem i DCOM och DCOM är i ett kritiskt tillstånd.

Använd följande procedur för att fastställa orsaken till felet.

**Undersök installationsloggarna**

1. Öppna installationsloggen som finns på c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Förekomsten av följande fel indikerar det här problemet:

    Avregistrera det befintliga programmet...  Skapa katalogobjektet Hämta samlingen av program 

    Fel:

    - Felkod: -2147164145 [0x8004E00F]
    - Utmatningskod: 802

Så här löser du problemet:

Kontakta [Microsoft Windows-plattformsteamet](https://aka.ms/Windows_Support) för att få hjälp med att lösa DCOM-problemet.

När DCOM-problemet är löst installerar du om Azure Site Recovery VSS Provider manuellt med följande kommando:
 
**C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent>"C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Om programkonsekvens inte är avgörande för dina krav på haveriberedskap kan du kringgå VSS Provider-installationen. 

Så här kringgår du installationen av Azure Site Recovery VSS Provider och installerar azure site recovery VSS Provider efter installationen:

1. Installera mobilitetstjänsten. 
   > [!Note]
   > 
   > Installationen kommer att misslyckas i steget "Post install configuration". 
2. Så här kringgår du VSS-installationen:
   1. Öppna installationskatalogen för Azure Site Recovery Mobility Service som finns på:
   
      C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent
   2. Ändra installationsskripten för Azure Site Recovery VSS Provider **nMageVSSProvider_Install** och **InMageVSSProvider_Uninstall.cmd** för att alltid lyckas genom att lägga till följande rader:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Kör mobilitetsagentinstallationen manuellt. 
4. När installationen lyckas och flyttas till nästa **steg, Konfigurera**, ta bort de rader som du har lagt till.
5. Om du vill installera VSS-providern öppnar du en kommandotolk som administratör och kör följande kommando:
   
    **C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. Kontrollera att ASR VSS-providern är installerad som en tjänst i Windows Services och öppna MMC för komponenttjänst för att kontrollera att ASR VSS-providern finns med i listan.
10. Om VSS-providerinstallationen fortsätter att misslyckas kan du arbeta med CX för att lösa behörighetsfelen i CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS-providerinstallationen misslyckas på grund av att klustertjänsten aktiveras på en icke-klusterdator

Det här problemet medför att Azure Site Recovery Mobility Agent-installationen misslyckas under installationssteget ASAzure Site RecoveryR VSS Provider på grund av ett problem med COM+ som förhindrar installation av VSS-providern.
 
### <a name="to-identify-the-issue"></a>Så här identifierar du problemet

I loggen på konfigurationsservern på C:\ProgramData\ASRSetupLogs\UploadedLogs\<datum-tid>UA_InstallLogFile.log hittar du följande undantag:

COM+ kunde inte prata med Microsoft Distributed Transaction Coordinator (Undantag från HRESULT: 0x8004E00F)

Så här löser du problemet:

1.  Kontrollera att den här datorn är en icke-klusterdator och att klusterkomponenterna inte används.
3.  Om komponenterna inte används tar du bort klusterkomponenterna från datorn.

## <a name="drivers-are-missing-on-the-source-server"></a>Drivrutiner saknas på källservern

Om mobility agent-installationen misslyckas undersöker du loggarna under C:\ProgramData\ASRSetupLogs för att avgöra om några av de nödvändiga drivrutinerna saknas i vissa kontrolluppsättningar.
 
Så här löser du problemet:
  
1. Öppna registret med en registerredigerare som regedit.msc.
2. Öppna noden HKEY_LOCAL_MACHINE\SYSTEM.
3. Leta reda på kontrollen Uppsättningar i SYSTEM-noden.
4. Öppna varje kontrolluppsättning och kontrollera att det finns följande Windows-drivrutiner:

   - Atapi
   - Vmbus (vmbus)
   - Storflt (storflt)
   - Storvsc (storvsc)
   - intelide (intelide)
 
Installera om alla drivrutiner som saknas.

## <a name="next-steps"></a>Nästa steg

[Läs om hur du](vmware-azure-tutorial.md) konfigurerar haveriberedskap för virtuella datorer med VMware.
