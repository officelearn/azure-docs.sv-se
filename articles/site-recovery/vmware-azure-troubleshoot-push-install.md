---
title: Felsöka fel vid installation av Mobilitetstjänsten push när du aktiverar replikering för haveriberedskap | Microsoft Docs
description: Felsöka Mobilitetstjänster installationen när du aktiverar replikering för haveriberedskap
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: a9738f95ce8a0de750ffa348e167bce3b0e659f6
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821403"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsöka installationsproblem med Mobilitetstjänsten push

Installationen av mobilitetstjänsten är ett viktigt steg vid aktivering av replikering. Det här steget beror helt på uppfyller kraven och arbeta med konfigurationer som stöds. De vanligaste felen som uppstår under mobilitetstjänsten är på grund av

* Autentiseringsuppgifter/behörighet fel
* Anslutningsfel
* Operativsystem som stöds inte
* VSS-installationsfel

När du aktiverar replikering, installera Azure Site Recovery försöker skicka mobilitetstjänstagenten på den virtuella datorn. Som en del av detta försöker konfigurationsservern ansluta med den virtuella datorn och kopiera agenten. Följ steg för steg-felsökningsinformation som anges nedan om du vill aktivera lyckad installation.

## <a name="credentials-check-errorid-95107--95108"></a>Autentiseringsuppgifter kontroll (samtalsstatus: 95107 & 95108)

* Kontrollera om det användarkonto som valdes när Aktivera replikering är **giltig, korrekt**.
* Azure Site Recovery kräver **administratörsbehörighet** att utföra push-installation.
  * Kontrollera om användarkontot har administrativ åtkomst för Windows, antingen lokalt eller via domänadministratör på källdatorn.
  * Om du inte använder ett domänkonto, måste du inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn.
    * Inaktivera kontroll av åtkomst för fjärranvändare, under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registernyckeln, lägga till ett nytt DWORD-värde: LocalAccountTokenFilterPolicy. Ange värdet till 1. Om du vill köra det här steget kör du följande kommando från Kommandotolken:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Du måste välja root-kontot för installation av mobilitetsagenten för Linux.

Om du vill ändra autentiseringsuppgifterna för valda användarkonto, följ instruktionerna [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="connectivity-check-errorid-95117--97118"></a>**Anslutningskontroll (samtalsstatus: 95117 & 97118)**

* Kontrollera att du kan pinga källdatorn från konfigurationsservern. Om du har valt skalbar processerver under Aktivera replikering, kontrollera att du kan pinga källdatorn från processervern.
  * Från kommandoraden för källservern datorn, använda Telnet för att pinga konfigurationsservern / skalbar processerver med https-porten (135) som visas nedan för att se om det finns problem med nätverksanslutningen eller brandväggen port blockerande problem.

     `telnet <CS/ scale-out PS IP address> <135>`
  * Kontrollera status för tjänsten **InMage Scout VX Agent – Sentinel/Outpost**. Starta tjänsten, om den inte körs.
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

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Fil- och skrivardelning tjänster kontroll (samtalsstatus: 95105 & 95106)

När du anslutningskontroll, kontrollera om fil- och skrivardelning tjänsten är aktiverad på den virtuella datorn.

För **windows 2008 R2 och tidigare versioner**,

* Aktivera fil- och skrivardelning via Windows-brandväggen
  * Öppna Kontrollpanelen -> System och säkerhet > Windows-brandväggen -> i vänstra fönstret, klicka på Avancerat Inställningar -> Klicka på regler för inkommande trafik i konsolträdet.
  * Leta upp regler fil och skrivardelning (NB-Session-In) och File and Printer Sharing (SMB-In). Högerklicka på regeln för varje regel och klicka sedan på **Aktivera regel**.
* Aktivera fildelning med en Grupprincip
  * Gå till Start, Skriv gpmc.msc och söka.
  * I navigeringsfönstret öppnar du följande mappar: lokal datorprincip, Användarkonfiguration, Administrationsmallar, Windows-komponenter och nätverksdelning.
  * I informationsfönstret dubbelklickar du på **hindra användare från att dela filer i profilen för deras**. Om du vill inaktivera grupprincipinställningen och aktivera användarens möjlighet att dela filer, klickar du på inaktiverad. Klicka på OK för att spara ändringarna. Mer information klickar du på [här](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

För **senare versioner**, följer du [här](vmware-azure-install-mobility-service.md) att aktivera fil- och skrivardelning.

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Konfigurationskontroll för Windows Management Instrumentation (WMI)

När fil-och skrivare kontrollerar du aktivera WMI-tjänsten via brandväggen.

* I Kontrollpanelen på säkerhet och klicka sedan på Windows-brandväggen.
* Klicka på Ändra inställningar och klickar sedan på fliken undantag.
* I fönstret undantag väljer du kryssrutan för Windows Management Instrumentation (WMI) att WMI-trafik genom brandväggen. 

Du kan också aktivera WMI-trafik genom brandväggen i Kommandotolken. Använd följande kommando `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Andra felsökning WMI-artiklar hittades i följande artiklar.

* [Grundläggande WMI-testning](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Felsökning av WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Felsökning av problem med WMI-skript och WMI-tjänster](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Operativsystem som stöds inte

En annan vanligaste orsaken till felet kan bero på operativsystem som inte stöds. Se till att du är på den operativsystem/Kernel-versionen som stöds för installation av mobilitetstjänsten.

Läs om vilka operativsystem som stöds av Azure Site Recovery, vår [matris stöddokument](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="vss-installation-failures"></a>Installera VSS-fel

Installera VSS är en del av Mobility agentinstallation. Den här tjänsten används under processen skapar programmet konsekventa återställningspunkter. Fel under installationen av VSS kan inträffa på grund av flera orsaker. För att identifiera de exakta fel som avser **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Några vanliga fel och Lösningssteg är markerade i följande avsnitt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Fel i VSS-2147023170 [0x800706BE] - slutkoden 511

Det här problemet är främst visas när ett antivirusprogram hindrar drift för Azure Site Recovery-tjänster. Att lösa problemet,

1. Undanta alla mappar som tidigare nämnts [här](vmware-azure-set-up-source.md#exclude-antivirus-on-the-configuration-server).
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