---
title: Felsöka fel vid installation av Mobilitetstjänsten push under aktivera Replication(VMware to Azure) | Microsoft Docs
description: Felsöka mobility service/push-installationsfel när du replikerar virtuella Azure-datorer.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 09/19/2018
ms.openlocfilehash: 22c01f2bd9c763eeb681bf2d60e0ccffe1154d85
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497628"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsöka installationsproblem med Mobilitetstjänsten push

Installationen av mobilitetstjänsten är ett viktigt steg vid aktivering av replikering. Det här steget beror helt på uppfyller kraven och arbeta med konfigurationer som stöds. De vanligaste felen som uppstår under mobilitetstjänsten är på grund av

* Autentiseringsuppgifter/behörighet fel
* Anslutningsfel
* Operativsystem som stöds inte

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
  * Från kommandoraden för källservern datorn använda Telnet för att pinga konfigurationsservern / skala ut processervern med https-porten (standard 9443) som visas nedan för att se om det finns problem med nätverksanslutningen eller brandväggen port blockerande problem.

     `telnet <CS/ scale-out PS IP address> <port>`

  * Om du inte kan ansluta, kan du inkommande port 9443 på konfigurationsservern / skalbar processerver.
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
  * I informationsfönstret dubbelklickar du på **hindra användare från att dela filer i profilen för deras**. Om du vill inaktivera grupprincipinställningen och aktivera användarens möjlighet att dela filer, klickar du på inaktiverad. Klicka på OK för att spara ändringarna. Mer information klickar du på [här](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

För **senare versioner**, följer du [här](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) att aktivera fil- och skrivardelning

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

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](vmware-azure-tutorial.md) att konfigurera haveriberedskap för virtuella VMware-datorer.