---
title: Förbered källdatorer för att installera Mobilitetstjänsten genom push-installation för haveriberedskap för virtuella datorer och fysiska servrar till Azure | Microsoft-dokument
description: Lär dig hur du förbereder servern för att installera Mobilitetsagenten genom push-installation för haveriberedskap av virtuella datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186457"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Förbered källmaskin för push-installation av mobilitetsagent

När du ställer in haveriberedskap för virtuella datorer med VMware och fysiska servrar med [Azure Site Recovery](site-recovery-overview.md)installerar du tjänsten Site Recovery [Mobility](vmware-physical-mobility-service-overview.md) på varje lokal virtuell dator för VMware och fysisk server.  Mobilitetstjänsten samlar in dataskrivningar på datorn och vidarebefordrar dem till site recovery-processservern.

## <a name="install-on-windows-machine"></a>Installera på Windows-dator

Gör följande på varje Windows-dator som du vill skydda:

1. Kontrollera att det finns nätverksanslutning mellan datorn och processservern. Om du inte har konfigurerat en separat processserver körs den som standard på konfigurationsservern.
1. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot ska ha administratörsrättigheter, antingen lokala eller domän. Använd det här kontot endast för push-installation och för agentuppdateringar.
2. Om du inte använder ett domänkonto inaktiverar du kontrollen Fjärråtkomst på den lokala datorn på följande sätt:
    - Lägg till en ny DWORD: **LocalAccountTokenFilterPolicy**under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System- och systemnyckeln. Ange värdet till **1**.
    -  Om du vill göra detta i en kommandotolk kör du följande kommando:  
   'REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. I Windows-brandväggen på den dator som du vill skydda väljer du **Tillåt en app eller funktion via Brandvägg**. Aktivera **fil- och skrivardelning** och **WMI (Windows Management Instrumentation).** För datorer som tillhör en domän kan du konfigurera brandväggsinställningarna med hjälp av ett grupprincipobjekt .

   ![Brandväggsinställningar](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Lägg till kontot som du skapat i CSPSConfigtool. Det gör du genom att logga in på konfigurationsservern.
5. Öppna **cspsconfigtool.exe**. Den är tillgänglig som en genväg på skrivbordet och i mappen %ProgramData%\ASR\home\svsystems\bin.
6. Välj **Lägg till konto**på fliken Hantera **konton** .
7. Lägg till kontot som du skapade.
8. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.

## <a name="install-on-linux-machine"></a>Installera på Linux-maskin

Gör följande på varje Linux-dator som du vill skydda:

1. Se till att det finns nätverksanslutning mellan Linux-datorn och processservern.
2. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste vara en **rotanvändare** på Linux-källservern. Använd det här kontot endast för push-installation och för uppdateringar.
3. Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
4. Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
5. Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
4. Aktivera SFTP-undersystem och lösenordsautentisering i sshd_config filen. Det gör du genom att logga in som **root**.
5. I filen **/etc/ssh/sshd_config** hittar du den rad som börjar med **PasswordAuthentication**.
6. Ta av raden och ändra värdet till **ja**.
7. Leta reda på raden som börjar med **Delsystemet**och ta bort kommentaren.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Starta om **sshd**-tjänsten.
9. Lägg till kontot som du skapat i CSPSConfigtool. Det gör du genom att logga in på konfigurationsservern.
10. Öppna **cspsconfigtool.exe**. Den är tillgänglig som en genväg på skrivbordet och i mappen %ProgramData%\home\svsystems\bin.
11. Välj **Lägg till konto**på fliken Hantera **konton** .
12. Lägg till kontot som du skapade.
13. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus på replikerade maskiner

Om datorer som du vill replikera har aktiva antivirusprogram som körs kontrollerar du att du utesluter installationsmappen för mobilitetstjänsten från antivirusåtgärder (*C:\ProgramData\ASR\agent*). Detta säkerställer att replikeringen fungerar som förväntat.

## <a name="next-steps"></a>Nästa steg

När mobilitetstjänsten har installerats väljer du **+ Replikera** i Azure-portalen för att börja skydda dessa virtuella datorer. Läs mer om hur du aktiverar replikering för virtuella datorer med [VMware](vmware-azure-enable-replication.md) och [fysiska servrar](physical-azure-disaster-recovery.md#enable-replication).


