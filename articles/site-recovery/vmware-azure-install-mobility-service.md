---
title: Installera Mobilitetstjänsten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure | Microsoft Docs
description: Lär dig hur du installerar mobilitetstjänstagenten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 14be544c53bf3393466cfa33b2ad815f07d0005d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007424"
---
# <a name="install-the-mobility-service-for-disaster-recovery-of-vmware-vms-and-physical-servers"></a>Installera mobilitetstjänsten för haveriberedskap för virtuella VMware-datorer och fysiska servrar

När du ställer in katastrofåterställning för virtuella VMware-datorer och fysiska servrar med [Azure Site Recovery](site-recovery-overview.md), du installerar den [Site Recovery-mobilitetstjänsten](vmware-physical-mobility-service-overview.md) på varje virtuell dator med en lokal VMware och fysiska servrar.  Mobilitetstjänsten samlar in skrivna data på datorn och vidarebefordrar dem till processervern för Site Recovery.

## <a name="install-on-windows-machine"></a>Installera på Windows-dator

På varje Windows-dator som du vill skydda, gör du följande:

1. Kontrollera att det finns nätverksanslutning mellan datorn och processervern. Om du inte har konfigurerat en separat processerver sedan körs som standard den på konfigurationsservern.
1. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste ha administratörsbehörighet, antingen lokalt eller via domänadministratör. Använd det här kontot bara för push-installation och för agentuppdateringar.
2. Om du inte använder ett domänkonto, inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn på följande sätt:
    - Under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registernyckeln lägger du till ett nytt DWORD-värde: **LocalAccountTokenFilterPolicy**. Ange värdet till **1**.
    -  Om du vill göra detta i en kommandotolk, kör du följande kommando:  
   ”REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. I Windows-brandväggen på den dator du vill skydda, Välj **Tillåt en app eller funktion i brandväggen**. Aktivera **File and Printer Sharing** och **Windows Management Instrumentation (WMI)**. För datorer som tillhör en domän kan konfigurera du brandväggsinställningarna med ett grupprincipobjekt (GPO).

   ![Brandväggsinställningar](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Lägg till kontot som du skapat i CSPSConfigtool. Om du vill göra detta måste du logga in på konfigurationsservern.
5. Öppna **cspsconfigtool.exe**. Det är tillgängligt som en genväg på skrivbordet och i mappen %ProgramData%\home\svsystems\bin.
6. På den **hantera konton** fliken **Lägg till konto**.
7. Lägg till kontot som du skapade.
8. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.

## <a name="install-on-linux-machine"></a>Installera på Linux-dator

På varje Linux-dator som du vill skydda, gör du följande:

1. Kontrollera att det finns nätverksanslutning mellan Linux-datorn och processervern.
2. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste vara en **rotanvändare** på Linux-källservern. Använd det här kontot bara för push-installation och för uppdateringar.
3. Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
4. Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
5. Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
4. Aktivera SFTP undersystemet och lösenordsautentisering i sshd_config-filen. Om du vill göra detta måste logga in som **rot**.
5. I den **/etc/ssh/sshd_config** filen, leta reda på raden som börjar med **PasswordAuthentication**.
6. Ta bort raden och ändra värdet till **Ja**.
7. Hitta raden som börjar med **undersystem**, och ta bort den.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Starta om **sshd**-tjänsten.
9. Lägg till kontot som du skapat i CSPSConfigtool. Om du vill göra detta måste du logga in på konfigurationsservern.
10. Öppna **cspsconfigtool.exe**. Det är tillgängligt som en genväg på skrivbordet och i mappen %ProgramData%\home\svsystems\bin.
11. På den **hantera konton** fliken **Lägg till konto**.
12. Lägg till kontot som du skapade.
13. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.

## <a name="next-steps"></a>Nästa steg

När Mobilitetstjänsten har installerats i Azure portal, Välj **+ replikera** att börja skydda dessa virtuella datorer. Läs mer om hur du aktiverar replikering för [VMware VMs(vmware-azure-enable-replication.md) och [fysiska servrar](physical-azure-disaster-recovery.md#enable-replication).


