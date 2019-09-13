---
title: Felsök problem med push-installation av mobilitets tjänsten när du aktiverar replikering för haveri beredskap | Microsoft Docs
description: Felsök installations fel för mobilitets tjänster när du aktiverar replikering för haveri beredskap
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 4aa18379962c289f5094795988a247f4c7e35df2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910646"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsöka problem med push-installation av mobilitets tjänster

Installation av mobilitets tjänsten är ett huvud steg under aktivera replikering. Framgången av det här steget är beroende av att de uppfyller kraven och fungerar med konfigurationer som stöds. De vanligaste felen som du möter under installationen av mobilitets tjänsten beror på följande:

* [Autentiseringsuppgifter/behörighets fel](#credentials-check-errorid-95107--95108)
* [Inloggnings försök](#login-failures-errorid-95519-95520-95521-95522)
* [Anslutningsfel](#connectivity-failure-errorid-95117--97118)
* [Fil-och skrivar delnings fel](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-problem](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Operativ system som inte stöds](#unsupported-operating-systems)
* [Startkonfigurationer som inte stöds](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-installationsfel](#vss-installation-failures)
* [Enhets namn i GRUB-konfiguration i stället för enhets-UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM volym](#lvm-support-from-920-version)
* [Varningar om omstart](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

När du aktiverar replikering försöker Azure Site Recovery push-installera mobilitets tjänst agenten på den virtuella datorn. Som en del av detta försöker konfigurations servern ansluta till den virtuella datorn och kopiera agenten. Om du vill aktivera lyckad installation följer du anvisningarna för steg för steg som visas nedan.

## <a name="credentials-check-errorid-95107--95108"></a>Kontroll av autentiseringsuppgifter (ErrorID: 95107 & 95108)

* Kontrol lera att det användar konto som valdes under aktivera replikering är **giltigt, korrekt**.
* Azure Site Recovery kräver ett **rot** konto eller ett användar konto med **administratörs behörighet** för att utföra push-installation. Annars kommer push-installationen att blockeras på käll datorn.
  * För Windows (**fel 95107**) kontrollerar du om användar kontot har administrativ åtkomst, antingen lokal eller domän, på käll datorn.
  * Om du inte använder ett domän konto måste du inaktivera åtkomst kontroll för fjärran vändare på den lokala datorn.
    * Om du vill inaktivera åtkomst kontroll för fjärranslutna användare, under register nyckeln HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, lägger du till ett nytt DWORD: LocalAccountTokenFilterPolicy. Ange värdet till 1. Kör följande kommando från kommando tolken för att köra det här steget:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * För Linux (**fel 95108**) måste du välja rot kontot för lyckad installation av mobilitets agenten. Dessutom bör SFTP-tjänster köras. Aktivera SFTP-undersystem och lösenordsautentisering i sshd_config-filen:
    1. Logga in som rot.
    2. Gå till/etc/ssh/sshd_config-filen och leta upp raden som börjar med PasswordAuthentication.
    3. Ta bort kommentaren till raden och ändra värdet till Ja.
    4. Hitta raden som börjar med under systemet och ta bort kommentaren mellan linjen.
    5. Starta om sshd-tjänsten.

Om du vill ändra autentiseringsuppgifterna för det valda användar kontot följer du anvisningarna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Otillräckligt med behörighets problem (ErrorID: 95517)

När den användare som valts för att installera mobilitets agenten inte har administratörs behörighet, kommer konfigurations Server/skalbar processerver inte att kunna kopiera mobilitets agentens program vara på käll datorn. Det här felet är alltså resultatet av åtkomst nekad. Kontrol lera att användar kontot har administratörs behörighet.

Om du vill ändra autentiseringsuppgifterna för det valda användar kontot följer du anvisningarna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Otillräckligt med behörighets problem (ErrorID: 95518)

När en domän förtroende Relations etablering mellan den primära domänen och arbets stationen Miss lyckas när du försöker logga in på käll datorn, Miss lyckas installationen av mobilitets agenten med fel-ID 95518. Se därför till att det användar konto som används för att installera mobilitets agenten har administratörs behörighet för att logga in via den primära domänen på käll datorn.

Om du vill ändra autentiseringsuppgifterna för det valda användar kontot följer du anvisningarna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Inloggnings försök (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Autentiseringsuppgifterna för användar kontot har inaktiverats (ErrorID: 95519)

Det användar konto som valdes under aktivera replikering har inaktiverats. Om du vill aktivera användar kontot läser du artikeln [här](https://aka.ms/enable_login_user) eller kör följande kommando genom att ersätta text *användar* namn med det faktiska användar namnet.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Autentiseringsuppgifterna har låsts på grund av flera misslyckade inloggnings försök (ErrorID: 95520)

Flera misslyckade försök att komma åt en dator kommer att låsa användar kontot. Felen kan bero på att:

* De autentiseringsuppgifter som angavs under konfigurations installationen är felaktiga eller
* Det användar konto som valdes under aktiveringen av replikering är felaktigt

Så ändra de angivna autentiseringsuppgifterna genom att följa anvisningarna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) och försök igen om en stund.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Inloggnings servrarna är inte tillgängliga på käll datorn (ErrorID: 95521)

Felet uppstår när inloggnings servrarna inte är tillgängliga på käll datorn. Om inloggnings servrarna inte är tillgängliga leder det till att det inte går att logga in, och därför kan mobilitets agenten inte installeras. För lyckad inloggning kontrollerar du att inloggnings servrar är tillgängliga på käll datorn och startar inloggnings tjänsten. Detaljerade anvisningar finns i KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) ERR-meddelande: Det finns för närvarande inga tillgängliga inloggnings servrar.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Inloggnings tjänsten körs inte på käll datorn (ErrorID: 95522)

Inloggnings tjänsten körs inte på käll datorn och det uppstod ett problem med inloggnings förfrågan. Det går därför inte att installera mobilitets agenten. För att lösa problemet kontrollerar du att inloggnings tjänsten körs på käll datorn för lyckad inloggning. Starta inloggnings tjänsten genom att köra kommandot "net start logon" från kommando tolken eller starta tjänsten NetLogon från aktivitets hanteraren.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Anslutnings problem (ErrorID: 95117 & 97118)**

Konfigurations servern/Scale-Out-processervern försöker ansluta till den virtuella käll datorn för att installera mobilitets agenten. Felet uppstår när käll datorn inte kan anslutas på grund av problem med nätverks anslutningen. För att lösa problemet

* Se till att du kan pinga käll datorn från konfigurations servern. Om du har valt en skalbar processerver under aktivera replikering, se till att du kan pinga käll datorn från processervern.
  * Från kommando raden för käll serverns dator använder du Telnet för att pinga konfigurations servern/den skalbara processervern med HTTPS-port (135) enligt nedan för att se om det finns några problem med nätverks anslutningen eller blockerar brand Väggs porten.

     `telnet <CS/ scale-out PS IP address> <135>`
* För **virtuella Linux-datorer**kan du dessutom
  * Kontrol lera om de senaste OpenSSH-, openssh-server-och openssl-paketen har installerats.
  * Kontrol lera och se till att SSH (Secure Shell) är aktiverat och körs på port 22.
  * SFTP-tjänster ska köras. För att aktivera SFTP-undersystem och lösenordsautentisering i sshd_config-filen
    * Logga in som rot.
    * Gå till/etc/ssh/sshd_config-filen och leta upp raden som börjar med PasswordAuthentication.
    * Ta bort kommentaren till raden och ändra värdet till Ja
    * Hitta raden som börjar med under systemet och ta bort kommentaren mellan linjen
    * Starta om sshd-tjänsten.
* Ett anslutnings försök kan ha misslyckats om det inte finns något korrekt svar efter en tids period eller en upprättad anslutning misslyckades eftersom den anslutna värden inte svarar.
* Det kan vara en anslutning/ett nätverks-/domän relaterat problem. Det kan också bero på problem med DNS-namn matchning eller TCP-port. Kontrol lera om det finns några kända problem i din domän.

## <a name="connectivity-failure-errorid-95523"></a>Anslutnings problem (ErrorID: 95523)

Felet uppstår när nätverket där käll datorn finns inte hittas eller kan ha tagits bort eller inte längre är tillgängligt. Det enda sättet att lösa felet är genom att se till att nätverket finns.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontroll av fil-och skrivar delnings tjänster (ErrorID: 95105 & 95106)

Efter anslutnings kontroll kontrollerar du om fil-och skrivar delnings tjänsten är aktive rad på den virtuella datorn. De här inställningarna krävs för att kopiera mobilitets agenten till käll datorn.

För **windows 2008 R2 och tidigare versioner**

* Om du vill aktivera fil-och skrivar delning via Windows-brandväggen,
  * Öppna kontroll panelen – > system och säkerhet-> Windows-brandväggen-> i den vänstra rutan klickar du på avancerade inställningar-> Klicka på regler för inkommande trafik i konsol trädet.
  * Hitta regel fil och skrivar delning (NB-session-in) och fil-och skrivar delning (SMB-in). För varje regel högerklickar du på regeln och klickar sedan på **Aktivera regel**.
* Om du vill aktivera fildelning med grupprincip
  * Gå till Start, skriv GPMC. msc och Sök.
  * Öppna följande mappar i navigerings fönstret: Princip för lokal dator, användar konfiguration, Administrativa mallar, Windows-komponenter och nätverks delning.
  * I informations fönstret dubbelklickar **du på förhindra användare från att dela filer i profilen**. Om du vill inaktivera inställningen för grupprincip och aktivera användarens möjlighet att dela filer klickar du på inaktive rad. Spara ändringarna genom att klicka på OK. Mer information finns i [Aktivera eller inaktivera fildelning med Grupprincip](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

För **senare versioner**följer du anvisningarna i [Installera mobilitets tjänsten för haveri beredskap för virtuella VMware-datorer och fysiska servrar](vmware-azure-install-mobility-service.md) för att aktivera fil-och skrivar delning.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) konfigurations kontroll (felkod: 95103)

När fil-och skrivar tjänster är markerat aktiverar du WMI-tjänsten för privata, offentliga och domän profiler genom brand väggen. De här inställningarna krävs för att slutföra fjärrkörning på käll datorn. För att aktivera,

* Gå till kontroll panelen, klicka på säkerhet och klicka sedan på Windows-brandväggen.
* Klicka på Ändra inställningar och klicka sedan på fliken undantag.
* I fönstret undantag markerar du kryss rutan för Windows Management Instrumentation (WMI) om du vill aktivera WMI-trafik genom brand väggen. 

Du kan också aktivera WMI-trafik genom brand väggen i kommando tolken. Använd följande kommando`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Andra artiklar om WMI-felsökning finns i följande artiklar.

* [Grundläggande WMI-testning](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-felsökning](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Fel sökning av problem med WMI-skript och WMI-tjänster](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Operativ system som inte stöds

En annan vanligaste orsaken till det kan bero på ett operativ system som inte stöds. Se till att du använder operativ systemet/kernel-versionen som stöds för lyckad installation av mobilitets tjänsten. Undvik användning av privat uppdatering.
Om du vill visa en lista över operativ system och kernel-versioner som stöds av Azure Site Recovery, se vårt [support mat ris dokument](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Konfigurationer för start disk som inte stöds (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Start-och systempartitionerna/volymerna är inte samma disk (ErrorID: 95309)

Före 9,20-versionen var start-och systempartitionerna/volymerna på olika diskar en konfiguration som inte stöds. Den här konfigurationen stöds från [9,20-versionen](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery). Använd den senaste versionen för detta stöd.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Start disken är inte tillgänglig (ErrorID: 95310)

Det går inte att skydda en virtuell dator utan en start disk. Detta är för att säkerställa en smidig återställning av den virtuella datorn under redundansväxlingen. Frånvaro av start disk resulterar i att datorn inte startar efter redundansväxlingen. Se till att den virtuella datorn innehåller start disk och försök sedan igen. Observera också att det inte finns stöd för flera start diskar på samma dator.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Det finns flera start diskar på käll datorn (ErrorID: 95311)

En virtuell dator med flera start diskar är inte en [konfiguration som stöds](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systempartition på flera diskar (ErrorID: 95313)

Innan 9,20-versionen, rotnoden eller volymen på flera diskar, var en konfiguration som inte stöds. Den här konfigurationen stöds från [9,20-versionen](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery). Använd den senaste versionen för detta stöd.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Det gick inte att aktivera skydd eftersom enhets namnet som anges i GRUB-konfigurationen i stället för UUID (ErrorID: 95320)

**Möjlig orsak:** </br>
Konfigurationsfilerna för GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/Boot/grub2/grub.cfg" eller "/etc/default/grub") kan innehålla värdet för parameter **roten** och **återupptas** som faktiska enhets namn i stället för uuid. Site Recovery bestämmer UUID-metoden som enhets namn kan ändras vid omstart av den virtuella datorn eftersom den virtuella datorn kanske inte har samma namn på redundansväxling, vilket leder till problem. Exempel: </br>


- Följande rad är från GRUB-filen **/Boot/grub2/grub.cfg**. <br>
  *Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** Started = tyst tyst showopts*


- Följande rad är GRUB-fil **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **rot = / dev/sda2** **återuppta = / dev/sda1** stänker = tyst crashkernel = 256M-:128M showopts vga = 0x314*

Om du observerar den feta strängen ovan, har GRUB de faktiska enhets namnen för parametrarna "root" och "Resume" i stället för UUID.
 
**Så här åtgärdar du:**<br>
Enhets namnen bör ersättas med motsvarande UUID.<br>


1. Hitta enhetens UUID genom att köra kommandot "blkid \<Device name >". Exempel:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Ersätt nu enhets namnet med dess UUID i formatet "root = UUID =\<UUID >". Om vi till exempel ersätter enhets namnen med UUID för rot-och återställnings parameter som nämns ovan i filerna "/Boot/grub2/grub.cfg", "/Boot/grub2/grub.cfg" eller "/etc/default/grub:, ser raderna i filerna ut som. <br>
   *kernel/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Starter = tyst crashkernel = 256M-: 128M showopts VGA = 0x314*
3. Starta om skyddet igen

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installationen av mobilitets tjänsten slutfördes med en varning för omstart (ErrorID: 95265 & 95266)

Site Recovery mobilitets tjänsten har många komponenter, en av dessa kallas filter driv rutin. Filter driv rutin läses bara in i system minnet i taget vid system omstarten. Det innebär att filter driv rutins korrigeringarna endast kan realiseras när en ny filter driv rutin läses in. Detta kan bara inträffa vid omstarten av systemet.

**Observera** att detta är en varning och att den befintliga replikeringen fungerar även efter den nya agent uppdateringen. Du kan välja att starta om varje gång som du vill få fördelarna med den nya filter driv rutinen, men om du inte startar om den gamla filter driv rutinen fortsätter att fungera. Så efter en uppdatering utan omstart, förutom vid filter driv rutin, kommer **fördelarna med andra förbättringar och korrigeringar i mobilitets tjänsten att realiseras**. Men även om det är rekommenderat, är det inte obligatoriskt att starta om efter varje uppgradering. Information om när en omstart krävs får du genom att ange att [käll datorn ska startas om efter uppgraderingen av mobilitets agenten](https://aka.ms/v2a_asr_reboot) i service updates i Azure Site Recovery.

> [!TIP]
>Bästa praxis om hur du schemalägger uppgraderingar i underhålls fönstret finns i [stöd för de senaste OS/kernel-versionerna](https://aka.ms/v2a_asr_upgrade_practice) i service updates i Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-stöd från 9,20-versionen

Före 9,20-versionen stöddes LVM endast för data diskar. /Boot bör finnas på en diskpartition och inte vara en LVM-volym.

Från [9,20-versionen](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)stöds [OS-disk på LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) . Använd den senaste versionen för detta stöd.

## <a name="insufficient-space-errorid-95524"></a>Otillräckligt med utrymme (ErrorID: 95524)

När mobilitets agenten kopieras till käll datorn krävs minst 100 MB ledigt utrymme. Se därför till att käll datorn har tillräckligt med ledigt utrymme och försök sedan igen.

## <a name="vss-installation-failures"></a>Installations problem med VSS

VSS-installation är en del av mobilitets Agent installationen. Den här tjänsten används för att generera program konsekventa återställnings punkter. Fel under installationen av VSS kan ske på grund av flera orsaker. För att identifiera de exakta felen, se **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Några vanliga fel och lösnings stegen är markerade i följande avsnitt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fel – 2147023170 [0x800706BE] – slutkod 511

Det här problemet visas främst när antivirus program hindrar åtgärder i Azure Site Recovery Services. Gör så här för att lösa problemet:

1. Uteslut alla mappar som anges [här](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Följ de rikt linjer som publicerats av antivirus leverantören för att avblockera registreringen av DLL i Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fel 7 [0x7]-slutkod 511

Detta är ett körnings fel och orsakas på grund av otillräckligt minne för att installera VSS. Se till att öka disk utrymmet för slutförd åtgärd.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fel – 2147023824 [0x80070430] – slutkod 517

Felet uppstår när Azure Site Recovery tjänsten VSS Provider har [marker ATS för borttagning](https://msdn.microsoft.com/library/ms838153.aspx). Försök att installera VSS manuellt på käll datorn genom att köra följande kommando rad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fel – 2147023841 [0x8007041F] – slutkod 512

Felet uppstår när Azure Site Recovery VSS Provider Service-databasen är [låst](https://msdn.microsoft.com/library/ms833798.aspx). Försök att installera VSS manuellt på käll datorn genom att köra följande kommando rad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Om det uppstår ett problem kan du kontrol lera om något antivirus program eller andra tjänster har fastnat i läget "starta". Detta kan behålla låset för databas tjänster. Det leder till problem vid installation av VSS-providern. Se till att ingen tjänst är i ett start läge och försök sedan igen.

### <a name="vss-exit-code-806"></a>Avslutnings kod för VSS 806

Felet uppstår när det användar konto som används för installation inte har behörighet att köra CSScript-kommandot. Ange de behörigheter som krävs för användar kontot för att köra skriptet och försök sedan igen.

### <a name="other-vss-errors"></a>Andra VSS-fel

Försök att installera tjänsten VSS Provider manuellt på käll datorn genom att köra följande kommando rad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS-fel – 0x8004E00F

Det här felet uppstår vanligt vis under installationen av mobilitets agenten på grund av problem i DCOM och DCOM är i ett kritiskt tillstånd.

Använd följande procedur för att ta reda på orsaken till felet.

**Granska installations loggarna**

1. Öppna installations loggen som finns på c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Förekomsten av följande fel indikerar det här problemet:

    Avregistrerar det befintliga programmet...  Skapa ett katalog objekt hämta program samlingen 

    FELS

    - Felkod:-2147164145 [0x8004E00F]
    - Slutkod: 802

Så här löser du problemet:

Kontakta [Microsoft Windows Platform-teamet](https://aka.ms/Windows_Support) för att få hjälp med att lösa DCOM-problemet.

När DCOM-problemet är löst måste du installera om Azure Site Recovery VSS-providern manuellt med följande kommando:
 
**C:\Program Files (x86) \Microsoft Azure Site Recovery\agent. > "C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Om program konsekvens inte är kritiskt för katastrof återställnings kraven kan du kringgå installationen av VSS-providern. 

För att kringgå Azure Site Recovery installation av VSS-providern och installera Azure Site Recovery VSS-Provider efter installationen manuellt:

1. Installera mobilitets tjänsten. 
   > [!Note]
   > 
   > Det går inte att installera vid steget "post installations konfiguration". 
2. Så här kringgår du VSS-installationen:
   1. Öppna installations katalogen för Azure Site Recovery Mobility Service på:
   
      C:\Program Files (x86) \Microsoft Azure Site Recovery\agent.
   2. Ändra Azure Site Recovery VSS-providerns installations skript **nMageVSSProvider_Install** och **InMageVSSProvider_Uninstall. cmd** så att de alltid lyckas genom att lägga till följande rader:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Kör installationen av mobilitets agenten manuellt. 
4. När installationen har slutförts och fortsätter till nästa steg, **Konfigurera**, ta bort de rader som du har lagt till.
5. Om du vill installera VSS-providern öppnar du en kommando tolk som administratör och kör följande kommando:
   
    **C:\Program Files (x86) \Microsoft Azure Site Recovery\agent. > .\InMageVSSProvider_Install.cmd**

9. Kontrol lera att VSS-providern för automatisk återställning är installerad som en tjänst i Windows-tjänster och öppna Component service MMC för att kontrol lera att VSS-providern för ASR visas.
10. Om installationen av VSS-providern fortsätter att fungera arbetar du med CX för att lösa behörighets felen i CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Installationen av VSS-providern Miss lyckas på grund av att kluster tjänsten är aktive rad på en annan dator än kluster

Det här problemet medför att installationen av Azure Site Recovery mobilitets agenten Miss fungerar under installationen av VSS-providern för Azure Site Recovery på grund av ett problem med COM+ som förhindrar installationen av VSS-providern.
 
### <a name="to-identify-the-issue"></a>Identifiera problemet

I den logg som finns på konfigurations servern\<vid C:\ProgramData\ASRSetupLogs\UploadedLogs datum/tid > UA_InstallLogFile. log, kommer du att hitta följande undantag:

COM+ kunde inte kommunicera med Microsoft koordinator för distribuerad transaktion (undantag från HRESULT: 0x8004E00F)

Så här löser du problemet:

1.  Kontrol lera att datorn är en icke-klustrad dator och att kluster komponenterna inte används.
3.  Om komponenterna inte används tar du bort kluster komponenterna från datorn.

## <a name="drivers-are-missing-on-the-source-server"></a>Driv rutiner saknas på käll servern

Om installationen av mobilitets agenten Miss lyckas granskar du loggarna under C:\ProgramData\ASRSetupLogs för att avgöra om några av de nödvändiga driv rutinerna saknas i vissa kontroll uppsättningar.
 
Så här löser du problemet:
  
1. Öppna registret med en registereditor som regedit. msc.
2. Öppna HKEY_LOCAL_MACHINE\SYSTEM-noden.
3. Leta upp kontroll uppsättningarna i SYSTEM-noden.
4. Öppna varje kontroll uppsättning och kontrol lera att följande Windows-drivrutiner finns:

   - ATAPI
   - Vmbus
   - storflt
   - storvsc
   - intelide
 
Installera om eventuella driv rutiner som saknas.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](vmware-azure-tutorial.md) du konfigurerar haveri beredskap för virtuella VMware-datorer.
