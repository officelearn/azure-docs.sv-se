---
title: Felsöka fel vid installation av Mobilitetstjänsten push när du aktiverar replikering för haveriberedskap | Microsoft Docs
description: Felsöka Mobilitetstjänster installationen när du aktiverar replikering för haveriberedskap
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 0278332105f2102fc82122c5a74db6326f011e81
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541203"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsöka installationsproblem med Mobilitetstjänsten push

Installationen av mobilitetstjänsten är ett viktigt steg vid aktivering av replikering. Det här steget beror helt på uppfyller kraven och arbeta med konfigurationer som stöds. De vanligaste felen som uppstår under mobilitetstjänsten är på grund av:

* [Autentiseringsuppgifter/behörighet fel](#credentials-check-errorid-95107--95108)
* [Misslyckad inloggning](#login-failures-errorid-95519-95520-95521-95522)
* [Anslutningsfel](#connectivity-failure-errorid-95117--97118)
* [Fil- och skrivardelning fel](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-fel](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Operativsystem som stöds inte](#unsupported-operating-systems)
* [Konfigurationer som inte stöds start](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-installationsfel](#vss-installation-failures)
* [Enhetsnamn i GRUB konfiguration i stället för enhet UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM-volym](#lvm-support-from-920-version)
* [Starta om varningar](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

När du aktiverar replikering, installera Azure Site Recovery försöker skicka mobilitetstjänstagenten på den virtuella datorn. Som en del av detta försöker konfigurationsservern ansluta med den virtuella datorn och kopiera agenten. Följ steg för steg-felsökningsinformation som anges nedan om du vill aktivera lyckad installation.

## <a name="credentials-check-errorid-95107--95108"></a>Autentiseringsuppgifter kontroll (samtalsstatus: 95107 & 95108)

* Kontrollera om det användarkonto som valdes när Aktivera replikering är **giltig, korrekt**.
* Azure Site Recovery kräver **rot** eller användarkonto med **administratörsbehörighet** att utföra push-installation. Annars kommer att blockeras push-installation på källdatorn.
  * För Windows (**fel 95107**), kontrollera om användarkontot har administratörsbehörighet, antingen lokalt eller via domänadministratör på källdatorn.
  * Om du inte använder ett domänkonto, måste du inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn.
    * Inaktivera kontroll av åtkomst för fjärranvändare, under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registernyckeln, lägga till ett nytt DWORD-värde: LocalAccountTokenFilterPolicy. Ange värdet till 1. Om du vill köra det här steget kör du följande kommando från Kommandotolken:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * För Linux (**fel 95108**), måste du välja root-kontot för installation av mobilitetsagenten. Dessutom bör SFTP-tjänsterna körs. Att aktivera SFTP undersystemet och lösenordsautentisering i sshd_config-filen:
    1. Logga in som rot.
    2. Gå till /etc/ssh/sshd_config, leta reda på raden som börjar med PasswordAuthentication.
    3. Ta bort raden och ändra värdet till Ja.
    4. Hitta raden som börjar med undersystemet och ta bort raden.
    5. Starta om tjänsten sshd.

Om du vill ändra autentiseringsuppgifterna för valda användarkonto, följ instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Inte tillräcklig behörighet för fel (samtalsstatus: 95517)

När du valt att installera mobilitetsagenten inte har administratörsbehörighet kommer Configuration server-/ skalbar processerver inte att kunna kopiera mobility Agentprogrammet in på källdatorn. Så beror det här felet på fel om nekad. Kontrollera att användarkontot har administratörsbehörighet.

Om du vill ändra autentiseringsuppgifterna för valda användarkonto, följ instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Inte tillräcklig behörighet för fel (samtalsstatus: 95518)

Om domänen förtroendet relationen upprättas mellan den primära domänen och arbetsstation misslyckas vid försök att logga in på källdatorn mobility agent-installationen att misslyckas med felet ID 95518. Kontrollera därför att det användarkonto som används för att installera mobilitetsagenten har administrativ behörighet att logga in via primära domänen för källdatorn.

Om du vill ändra autentiseringsuppgifterna för valda användarkonto, följ instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Misslyckade inloggningar (samtalsstatus: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Autentiseringsuppgifter för användarkontot har inaktiverats (samtalsstatus: 95519)

Det användarkonto som valts vid aktivering av replikering har inaktiverats. Om du vill aktivera användarkontot finns i artikeln [här](https://aka.ms/enable_login_user) eller kör följande kommando genom att ersätta texten *användarnamn* med det verkliga användarnamnet.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Autentiseringsuppgifter som har låsts ute på grund av flera misslyckade inloggningsförsök (samtalsstatus: 95520)

Arbete med flera misslyckade försök att få åtkomst till en dator låses användarkontot. Felet kan bero på följande:

* Autentiseringsuppgifter som angavs under konfiguration är felaktiga eller
* Det användarkonto som valts vid aktivering av replikering är fel

Så, ändra autentiseringsuppgifterna valt genom att följa instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) och försök igen efter en stund.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Inloggningsservrar finns inte på källdatorn (samtalsstatus: 95521)

Det här felet uppstår när inloggningsservrar inte finns på källdatorn. Otillgänglig inloggningsservrar leder till fel i inloggningsbegäran och därmed mobilitetsagenten kan inte installeras. Se till att inloggningsservrar är tillgängliga på källdatorn och starta tjänsten Logon för lyckad inloggning. Detaljerade anvisningar finns i KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) felmeddelande: Det finns för närvarande ingen inloggning servrar.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Logon-tjänsten inte körs på källdatorn (samtalsstatus: 95522)

Inloggnings-tjänsten körs inte på källdatorn och orsakas av fel i inloggningsbegäran. Mobilitetsagenten kan därför inte installeras. Lös genom att se till att Logon-tjänsten körs på källdatorn för lyckad inloggning. Om du vill starta tjänsten inloggning, kör kommandot ”net start inloggning” från Kommandotolken eller starta tjänsten ”NetLogon” från Aktivitetshanteraren.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Anslutningsfel (samtalsstatus: 95117 & 97118)**

Konfigurationsservern / processervern skalbar försöker ansluta till den Virtuella installera mobilitetsagenten källdatorn. Det här felet uppstår när källdatorn inte kan nås på grund av problem med nätverksanslutningen. Att lösa,

* Kontrollera att du kan pinga källdatorn från konfigurationsservern. Om du har valt skalbar processerver under Aktivera replikering, kontrollera att du kan pinga källdatorn från processervern.
  * Från kommandoraden för källservern datorn, använda Telnet för att pinga konfigurationsservern / skalbar processerver med https-porten (135) som visas nedan för att se om det finns problem med nätverksanslutningen eller brandväggen port blockerande problem.

     `telnet <CS/ scale-out PS IP address> <135>`
* Dessutom för **virtuell Linux-dator**,
  * Kontrollera om senaste openssh, openssh-server och openssl paketen har installerats.
  * Kontrollera och se till att Secure Shell (SSH) är aktiverad och körs på port 22.
  * SFTP-tjänsterna ska köras. Att aktivera SFTP-undersystemet och lösenordsautentisering i sshd_config-filen
    * Logga in som rot.
    * Gå till /etc/ssh/sshd_config, leta reda på raden som börjar med PasswordAuthentication.
    * Ta bort raden och ändra värdet på Ja
    * Hitta raden som börjar med undersystemet och ta bort raden
    * Starta om tjänsten sshd.
* Ett anslutningsförsök kan ha misslyckats om det finns inget rätt svar efter en viss tidsperiod, eller etablerade anslutningen eftersom den anslutna värden inte svarade.
* Det kan vara en anslutning/nätverk/domän problem. Det kan också bero på DNS-namn som löser problemet eller TCP-port överbelastning problem. Kontrollera om det finns några kända problem i din domän.

## <a name="connectivity-failure-errorid-95523"></a>Anslutningsfel (samtalsstatus: 95523)

Det här felet uppstår när nätverk där källdatorn finns hittades inte eller kan ha tagits bort eller är inte längre tillgänglig. Det är det enda sättet att lösa problemet genom att se till att nätverket finns.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Fil- och skrivardelning tjänster kontroll (samtalsstatus: 95105 & 95106)

När du anslutningskontroll, kontrollera om fil- och skrivardelning tjänsten är aktiverad på den virtuella datorn. De här inställningarna krävs för att kopiera mobilitetsagenten på källdatorn.

För **windows 2008 R2 och tidigare versioner**,

* Aktivera fil- och skrivardelning via Windows-brandväggen
  * Öppna Kontrollpanelen -> System och säkerhet > Windows-brandväggen -> i vänstra fönstret, klicka på Avancerat Inställningar -> Klicka på regler för inkommande trafik i konsolträdet.
  * Leta upp regler fil och skrivardelning (NB-Session-In) och File and Printer Sharing (SMB-In). Högerklicka på regeln för varje regel och klicka sedan på **Aktivera regel**.
* Aktivera fildelning med en Grupprincip
  * Gå till Start, Skriv gpmc.msc och söka.
  * I navigeringsfönstret öppnar du följande mappar: Lokal datorprincip, Användarkonfiguration, administrativa mallar, Windows-komponenter och nätverksdelning.
  * I informationsfönstret dubbelklickar du på **hindra användare från att dela filer i profilen för deras**. Om du vill inaktivera grupprincipinställningen och aktivera användarens möjlighet att dela filer, klickar du på inaktiverad. Klicka på OK för att spara ändringarna. Mer information finns i [aktivera eller inaktivera fildelning med Grupprincip](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

För **senare versioner**, följ instruktionerna i [installera mobilitetstjänsten för haveriberedskap för virtuella VMware-datorer och fysiska servrar](vmware-azure-install-mobility-service.md) att aktivera fil- och skrivardelning.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Konfigurationskontroll för Windows Management Instrumentation (WMI) (felkod: 95103)

När fil-och skrivare kontrollerar du aktivera WMI-tjänsten för privat, offentlig och domän profiler genom brandväggen. De här inställningarna krävs för att slutföra fjärrkörning på källdatorn. Att aktivera,

* Gå till Kontrollpanelen, klicka på Security och klicka sedan på Windows-brandväggen.
* Klicka på Ändra inställningar och klickar sedan på fliken undantag.
* I fönstret undantag väljer du kryssrutan för Windows Management Instrumentation (WMI) att WMI-trafik genom brandväggen. 

Du kan också aktivera WMI-trafik genom brandväggen i Kommandotolken. Använd följande kommando `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Andra felsökning WMI-artiklar hittades i följande artiklar.

* [Grundläggande WMI-testning](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Felsökning av WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Felsökning av problem med WMI-skript och WMI-tjänster](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Operativsystem som stöds inte

En annan vanligaste orsaken till felet kan bero på operativsystem som inte stöds. Se till att du är på den operativsystem/Kernel-versionen som stöds för installation av mobilitetstjänsten. Undvik att användningen av privata patch.
Om du vill visa listan över operativsystem och kernel-versioner som stöds av Azure Site Recovery, referera till vår [matris stöddokument](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Boot diskkonfigurationer som inte stöds (samtalsstatus: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Start- och systempartitionerna / volymerna inte finns på samma disk (samtalsstatus: 95309)

Innan du 9.20 version, Start- och systempartitionerna / volymerna på olika diskar har en konfiguration som inte stöds. Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), den här konfigurationen stöds. Använd senaste versionen för det här.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Startdisken är inte tillgänglig (samtalsstatus: 95310)

En virtuell dator utan en startdisk kan inte skyddas. Detta är att säkerställa smidig återställning av virtuell dator under redundansväxlingen. Avsaknad av startdisk resulterar i fel att starta datorn efter redundans. Se till att den virtuella datorn innehåller startdisk och försök igen. Observera också att flera startdiskar på samma dator inte stöds.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Flera startdisketter finns på källdatorn (samtalsstatus: 95311)

En virtuell dator med flera startdiskar är inte en [stöds configuration](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systempartitionen på flera diskar (samtalsstatus: 95313)

Har en konfiguration som inte stöds före 9.20 version rotpartitionen eller volymen utspridd på flera diskar. Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), den här konfigurationen stöds. Använd senaste versionen för det här.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Aktivera skydd kunde inte utföras eftersom enhetsnamn som nämns i GRUB-konfigurationen istället för UUID (samtalsstatus: 95320)

**Möjlig orsak:** </br>
GRUB-konfigurationsfilerna (”/ boot/grub/menu.lst” ”, / boot/grub/grub.cfg” ”, / boot/grub2/grub.cfg” eller ”/ etc/standard/grub”) kan innehålla värdet för parametrarna **rot** och **återuppta** som den namn på verkliga enheter istället för UUID. Site Recovery mandat UUID-metod som namn på enheter som kan ändras över omstart av den virtuella datorn när virtuell dator inte kanske kommer upp med samma namn på redundans, vilket resulterar i problem. Exempel: </br>


- Följande rad är GRUB-fil **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- Följande rad är GRUB-fil **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **rot = / dev/sda2** **återuppta = / dev/sda1 ** stänker = tyst crashkernel = 256M-:128M showopts vga = 0x314*

Om du upptäcker fetstil strängen ovan, innehåller GRUB faktiska enhetsnamn för parametrar ”rot” och ”återuppta” istället för UUID.
 
**Så här åtgärdar du:**<br>
Enhetsnamn ska ersättas med motsvarande UUID.<br>


1. Hitta UUID för enheten genom att köra kommandot ”blkid \<enhetens namn >”. Exempel:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Ersätt namnet på enheten med dess UUID i format som ”rot = UUID =\<UUID >”. Om vi ersätta enhetsnamn med UUID för roten och återuppta parameter som nämns ovan i filerna till exempel ”/ boot/grub2/grub.cfg” ”, / boot/grub2/grub.cfg” eller ”/ etc/standard/grub: sedan raderna i filerna ska se ut. <br>
   *Kernel /boot/vmlinuz-3.0.101-63-default **rot = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **återuppta = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** stänker = tyst crashkernel = 256M-:128M showopts vga = 0x314*
3. Starta om skyddet igen

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installera Mobilitetstjänsten slutfördes med varning ska startas om (samtalsstatus: 95265 & 95266)

Site Recovery-mobilitetstjänsten har flera komponenter, varav kallas filter-drivrutinen. Filterdrivrutinen hämtar läsas in i systemminnet endast vid tiden för omstart av systemet. Det innebär att filtret drivrutinen korrigeringar bara kan realiseras när ett nytt filter-drivrutinen har lästs in; vilket kan inträffa endast vid tidpunkten för omstart av systemet.

**Observera** som detta är en varning och befintliga replikeringen fungerar även efter den nya agent uppdateringen. Du kan välja att starta om när du vill få fördelarna med nya filterdrivrutinen men om du inte starta om de gamla filter-drivrutinen är fortfarande om hur du arbetar. I så fall efter en uppdatering utan omstart, förutom filterdrivrutinen, **fördelarna med andra förbättringar och korrigeringar i mobilitetstjänsten hämtar insåg**. Så det alternativ som rekommenderas, det inte är obligatoriskt att starta om efter varje uppgradering. Information om när en omstart är obligatorisk, ange den [omstart av källdatorn efter mobility agentuppgraderingen ](https://aka.ms/v2a_asr_reboot) avsnittet i tjänstuppdateringar i Azure Site Recovery.

> [!TIP]
>Bästa metoder för schemaläggning uppgraderingar under underhållsperiod finns den [stöd för senaste OS/kernel-versionerna](https://aka.ms/v2a_asr_upgrade_practice) i tjänstuppdateringar i Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM stöd från 9.20 version

Före 9.20 version har LVM stöd för data-diskar. / Boot bör finnas på en diskpartition och inte en LVM-volym.

Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), [OS-disken på LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) stöds. Använd senaste versionen för det här.

## <a name="insufficient-space-errorid-95524"></a>Inte tillräckligt med utrymme (samtalsstatus: 95524)

När mobilitetsagenten kopieras på källdatorn, krävs minst 100 MB ledigt utrymme. Därför se till att källdatorn krävs ledigt utrymme och försök igen.

## <a name="vss-installation-failures"></a>Installera VSS-fel

Installera VSS är en del av Mobility agentinstallation. Den här tjänsten används under processen skapar programmet konsekventa återställningspunkter. Fel under installationen av VSS kan inträffa på grund av flera orsaker. För att identifiera de exakta fel som avser **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Några vanliga fel och Lösningssteg är markerade i följande avsnitt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Fel i VSS-2147023170 [0x800706BE] - slutkoden 511

Det här problemet är främst visas när ett antivirusprogram hindrar drift för Azure Site Recovery-tjänster. Gör så här för att lösa problemet:

1. Undanta alla mappar som tidigare nämnts [här](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Följ riktlinjerna som publicerats av antivirus-leverantören att avblockera registreringen av DLL-filen i Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Fel i VSS 7 [0x7] - slutkoden 511

Det här är ett körningsfel och beror på grund av otillräckligt med minne för att installera VSS. Se till att öka diskutrymmet för den här åtgärden slutförs.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Fel i VSS-2147023824 [0x80070430] - slutkoden 517

Det här felet uppstår när Azure Site Recovery VSS Provider-tjänsten är [markerats för borttagning](https://msdn.microsoft.com/en-us/library/ms838153.aspx). Försök att installera VSS manuellt på källdatorn under installationen genom att köra följande kommandorad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Fel i VSS-2147023841 [0x8007041F] - slutkoden 512

Det här felet uppstår när Azure Site Recovery VSS Provider-tjänstdatabasen är [låst](https://msdn.microsoft.com/en-us/library/ms833798.aspx). Försök att installera VSS manuellt på källdatorn under installationen genom att köra följande kommandorad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS slutkod 806

Det här felet uppstår när användarkontot som används för installation inte har behörighet att köra kommandot CSScript. Ger behörighet till användarkontot som du kör skriptet och försök igen.

### <a name="other-vss-errors"></a>Andra VSS-fel

Försök att installera VSS-provider-tjänsten manuellt på källdatorn under installationen genom att köra följande kommandorad

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS error - 0x8004E00F

Det här felet uppstår vanligen under installationen av mobilitetsagenten på grund av problem i DCOM och DCOM är i kritiskt tillstånd.

Använd följande procedur för att fastställa orsaken till felet.

**Kontrollera installationsloggarna**

1. Öppna installationsloggen finns på c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Förekomst av följande fel anger det här problemet:

    Avregistrera det befintliga programmet...  Skapa objektet catalogue hämta samling av program 

    FEL:

    - Felkod:-2147164145 [0x8004E00F]
    - Slutkod: 802

Att lösa problemet:

Kontakta den [Microsoft Windows platform-teamet](https://aka.ms/Windows_Support) att få hjälp med att lösa problemet DCOM.

När DCOM problemet är löst, installera om Azure Site Recovery VSS-providern manuellt med hjälp av följande kommando:
 
**C:\Program filer (x86) \Microsoft Azure Site Recovery\agent > ”C:\Program filer (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Om programkonsekvens inte är kritiska för Disaster Recovery-krav, kan du kringgå VSS-leverantören installationen. 

Kringgå Azure Site Recovery VSS Provider-installationen och installera Azure Site Recovery VSS Provider efter installationen manuellt:

1. Installera mobilitetstjänsten. 
   > [!Note]
   > 
   > Installationen misslyckas vid ”Post installera configuration” steg. 
2. Kringgå VSS-installationen:
   1. Öppna Azure Site Recovery-Mobilitetstjänsten installationskatalogen finns på:
   
      C:\Program filer (x86) \Microsoft Azure Site Recovery\agent
   2. Ändra installationsskript för Azure Site Recovery VSS Provider **nMageVSSProvider_Install** och **InMageVSSProvider_Uninstall.cmd** ska alltid lyckas genom att lägga till följande rader:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Kör Mobilitetsagenten installationen manuellt. 
4. När installationen lyckas och flyttar till nästa steg, **konfigurera**, ta bort rader som du har lagt till.
5. För att installera VSS-leverantören, öppna en kommandotolk som administratör och kör följande kommando:
   
    **C:\Program filer (x86) \Microsoft Azure Site Recovery\agent >.\InMageVSSProvider_Install.cmd**

9. Kontrollera att ASR VSS-providern är installerad som en tjänst i Windows-tjänster och öppna komponenten Service MMC för att kontrollera att ASR VSS-providern finns.
10. Om VSS-leverantören installerar fortsätter att misslyckas, arbeta med CX att åtgärda felen i CAPI2 behörigheter.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Installera VSS-providern misslyckas på grund av klustertjänsten håller på att aktiveras på datorn för icke-kluster

Det här problemet orsakar Azure Site Recovery Mobility Agent-installationen misslyckas under installationssteget ASAzure Site RecoveryR VSS-providern på grund av ett problem med COM + som hindrar installationen av VSS-leverantören.
 
### <a name="to-identify-the-issue"></a>Att identifiera problemet

I loggen på konfigurationsservern på C:\ProgramData\ASRSetupLogs\UploadedLogs\<datum / tid-> UA_InstallLogFile.log, hittar du följande undantag:

COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator (undantag från HRESULT: 0x8004E00F)

Att lösa problemet:

1.  Kontrollera att den här datorn är en dator för icke-kluster och klusterkomponenter inte som används.
3.  Om komponenterna inte som används, kan du ta bort klusterkomponenter från datorn.

## <a name="drivers-are-missing-on-the-source-server"></a>Drivrutiner saknas på källservern

Om Mobilitetsagenten misslyckas, kan du granska loggarna under C:\ProgramData\ASRSetupLogs att avgöra om några av de nödvändiga drivrutinerna saknas i vissa kontrolluppsättningar.
 
Att lösa problemet:
  
1. Använd en registereditor, till exempel regedit.msc, öppna registret.
2. Öppna noden HKEY_LOCAL_MACHINE\SYSTEM.
3. Leta upp kontrollen i noden SYSTEM uppsättningar.
4. Öppna varje kontrolluppsättning och kontrollera att följande Windows-drivrutiner finns:

   - ATAPI
   - Vmbus
   - storflt
   - Storvsc
   - Intelide
 
Installera om alla drivrutiner som saknas.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](vmware-azure-tutorial.md) att konfigurera haveriberedskap för virtuella VMware-datorer.
