---
title: Felsöka fel vid installation av Mobilitetstjänsten push när du aktiverar replikering för haveriberedskap | Microsoft Docs
description: Felsöka Mobilitetstjänster installationen när du aktiverar replikering för haveriberedskap
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 12/12/2018
ms.openlocfilehash: fef0cfd05fe0d44966cbb9f15ba1148f8473207d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789915"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsöka installationsproblem med Mobilitetstjänsten push

Installationen av mobilitetstjänsten är ett viktigt steg vid aktivering av replikering. Det här steget beror helt på uppfyller kraven och arbeta med konfigurationer som stöds. De vanligaste felen som uppstår under mobilitetstjänsten är på grund av:

* Autentiseringsuppgifter/behörighet fel
* Misslyckad inloggning
* Anslutningsfel
* Operativsystem som stöds inte
* VSS-installationsfel

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

Om domänen förtroendet relationen upprättas mellan den primära domänen och arbetsstation misslyckas vid försök att logga in på källdatorn mobility agent-installationen att misslyckas med felet id 95518. Kontrollera därför att det användarkonto som används för att installera mobilitetsagenten har administrativ behörighet att logga in via primära domänen för källdatorn.

Om du vill ändra autentiseringsuppgifterna för valda användarkonto, följ instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failure-errorid-95519"></a>Fel vid inloggning (samtalsstatus: 95519)

Det användarkonto som valts vid aktivering av replikering har inaktiverats. Om du vill aktivera användarkontot finns i artikeln [här](https://aka.ms/enable_login_user) eller kör följande kommando genom att ersätta texten *användarnamn* med det verkliga användarnamnet.
`net user 'username' /active:yes`

## <a name="login-failure-errorid-95520"></a>Fel vid inloggning (samtalsstatus: 95520)

Arbete med flera misslyckade försök att få åtkomst till en dator låses användarkontot. Felet kan bero på följande:

* Autentiseringsuppgifter som angavs under konfiguration är felaktiga eller
* Det användarkonto som valts vid aktivering av replikering är fel

Så, ändra autentiseringsuppgifterna valt genom att följa instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) och försök igen efter en stund.

## <a name="login-failure-errorid-95521"></a>Fel vid inloggning (samtalsstatus: 95521)

Det här felet uppstår när inloggningsservrar inte finns på källdatorn. Otillgänglig inloggningsservrar leder till fel i inloggningsbegäran och därmed mobilitetsagenten kan inte installeras. Se till att inloggningsservrar är tillgängliga på källdatorn och starta tjänsten Logon för lyckad inloggning. Detaljerade anvisningar finns klickar du på [här](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available).

## <a name="login-failure-errorid-95522"></a>Fel vid inloggning (samtalsstatus: 95522)

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
  * I informationsfönstret dubbelklickar du på **hindra användare från att dela filer i profilen för deras**. Om du vill inaktivera grupprincipinställningen och aktivera användarens möjlighet att dela filer, klickar du på inaktiverad. Klicka på OK för att spara ändringarna. Mer information klickar du på [här](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

För **senare versioner**, följer du [här](vmware-azure-install-mobility-service.md) att aktivera fil- och skrivardelning.

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

## <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Start- och systempartitionerna / volymerna inte finns på samma disk (samtalsstatus: 95309)

Innan du 9.20 version, Start- och systempartitionerna / volymerna på olika diskar har en konfiguration som inte stöds. Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), den här konfigurationen stöds. Använd senaste versionen för det här.

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systempartitionen på flera diskar (samtalsstatus: 95313)

Har en konfiguration som inte stöds före 9.20 version rotpartitionen eller volymen utspridd på flera diskar. Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), den här konfigurationen stöds. Använd senaste versionen för det här.

## <a name="grub-uuid-failure-errorid-95320"></a>GRUB UUID-fel (samtalsstatus: 95320)

Om källdatorns GRUB använder enhetsnamnet istället för UUID, misslyckas mobility agent-installationen. Nå till system-administratören att göra ändringarna till GRUB-fil.

## <a name="lvm-support-from-920-version"></a>LVM stöd från 9.20 version

Före 9.20 version har LVM stöd för data-diskar. / Boot bör finnas på en diskpartition och inte en LVM-volym.

Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), [OS-disken på LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) stöds. Använd senaste versionen för det här.

## <a name="insufficient-space-errorid-95524"></a>Inte tillräckligt med utrymme (samtalsstatus: 95524)

När mobilitetsagenten kopieras på källdatorn, krävs minst 100 MB ledigt utrymme. Därför se till att källdatorn krävs ledigt utrymme och försök igen.

## <a name="vss-installation-failures"></a>Installera VSS-fel

Installera VSS är en del av Mobility agentinstallation. Den här tjänsten används under processen skapar programmet konsekventa återställningspunkter. Fel under installationen av VSS kan inträffa på grund av flera orsaker. För att identifiera de exakta fel som avser **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Några vanliga fel och Lösningssteg är markerade i följande avsnitt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Fel i VSS-2147023170 [0x800706BE] - slutkoden 511

Det här problemet är främst visas när ett antivirusprogram hindrar drift för Azure Site Recovery-tjänster. Att lösa problemet,

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

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](vmware-azure-tutorial.md) att konfigurera haveriberedskap för virtuella VMware-datorer.
